# SOLPREDS Code Examples
Working examples for common bot patterns.

---

## 1. Simple Trading Bot
Authenticates, fetches market data, and prepares a bet.

```javascript
const nacl = require('tweetnacl');
const bs58 = require('bs58');
const { Keypair } = require('@solana/web3.js');

const API = 'https://solpreds.fun/api/bot';

// Load your keypair (NEVER commit private keys!)
const keypair = Keypair.fromSecretKey(
  bs58.decode(process.env.SOLANA_PRIVATE_KEY)
);
const wallet = keypair.publicKey.toBase58();

let authToken = null;

// Authenticate with wallet signature
async function authenticate() {
  // Get nonce
  const nonceRes = await fetch(`${API}/auth/nonce?wallet=${wallet}`);
  const { nonce, message } = await nonceRes.json();

  // Sign message
  const messageBytes = new TextEncoder().encode(message);
  const signature = nacl.sign.detached(messageBytes, keypair.secretKey);
  const signatureB58 = bs58.encode(signature);

  // Verify and get token
  const verifyRes = await fetch(`${API}/auth/verify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ wallet, nonce, signature: signatureB58 }),
  });

  const data = await verifyRes.json();
  authToken = data.token;
  console.log('Authenticated, expires:', data.expiresAt);
  return authToken;
}

// Get current market state
async function getMarket() {
  const res = await fetch(`${API}/market`);
  return res.json();
}

// Prepare a bet (get transaction data)
async function prepareBet(roundId, side, amountSol) {
  const amount = Math.floor(amountSol * 1e9); // Convert to lamports

  const res = await fetch(`${API}/auth/prepare-bet`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${authToken}`,
    },
    body: JSON.stringify({ roundId, side, amount }),
  });

  return res.json();
}

// Get my positions
async function getMyPositions() {
  const res = await fetch(`${API}/auth/positions`, {
    headers: { 'Authorization': `Bearer ${authToken}` },
  });
  return res.json();
}

// Main trading logic
async function main() {
  // Authenticate
  await authenticate();

  // Get market data
  const market = await getMarket();
  console.log('Current round:', market.currentRoundId);

  if (market.next) {
    const { roundId, upPayout, downPayout, timeRemaining } = market.next;
    console.log(`Next round ${roundId}:`);
    console.log(`  UP: ${upPayout.toFixed(2)}x | DOWN: ${downPayout.toFixed(2)}x`);
    console.log(`  Time: ${Math.round(timeRemaining / 1000)}s remaining`);

    // Simple strategy: bet on higher payout side
    const side = upPayout > downPayout ? 'UP' : 'DOWN';
    const payout = Math.max(upPayout, downPayout);

    // Only bet if payout > 2x and enough time remains
    // Minimum bet is 0.001 SOL (1,000,000 lamports)
    if (payout > 2.0 && timeRemaining > 30000) {
      console.log(`Preparing ${side} bet...`);
      const betData = await prepareBet(roundId, side, 0.001); // 0.001 SOL minimum

      if (betData.success) {
        console.log('Bet prepared:', betData);
        console.log('Accounts:', betData.accounts);
        console.log('Instructions:', betData.instructions);
        // Now construct and send the Solana transaction using betData
      } else {
        console.error('Bet failed:', betData.error);
      }
    }
  }

  // Check positions
  const positions = await getMyPositions();
  console.log('\nMy positions:', positions.summary);
}

main().catch(console.error);
```

---

## 2. WebSocket Listener
Monitors market in real-time and logs events.

> **Note:** The `market-update` event does **not** include payout multipliers (`upPayout`/`downPayout`). Use the REST `GET /market` endpoint to get payouts, and calculate them yourself from pool ratios if needed.

```javascript
const io = require('socket.io-client');

const socket = io('wss://solpreds.fun');

console.log('Connecting to SOLPREDS...');

socket.on('connect', () => {
  console.log('Connected! Socket ID:', socket.id);
});

socket.on('connect_error', (err) => {
  console.error('Connection error:', err.message);
});

// Market updates every 10 seconds
// Note: upPool/downPool are in lamports. Payout multipliers are not included here —
// fetch GET /market if you need them.
socket.on('market-update', (data) => {
  const timestamp = new Date().toLocaleTimeString();

  if (data.live) {
    const { roundId, upPool, downPool, timeRemaining } = data.live;
    const totalPool = ((upPool + downPool) / 1e9).toFixed(2);
    console.log(`[${timestamp}] LIVE #${roundId}: Pool ${totalPool} SOL | ${Math.round(timeRemaining / 1000)}s`);
  }

  if (data.next) {
    const { roundId, upPool, downPool, timeRemaining } = data.next;
    const totalPool = (upPool + downPool) / 1e9;

    // Calculate payouts from pool ratios (95% payout pool)
    const upPayout = totalPool > 0 ? (totalPool * 0.95) / (upPool / 1e9) : 0;
    const downPayout = totalPool > 0 ? (totalPool * 0.95) / (downPool / 1e9) : 0;

    console.log(`[${timestamp}] NEXT #${roundId}: UP ~${upPayout.toFixed(2)}x | DOWN ~${downPayout.toFixed(2)}x | Pool: ${totalPool.toFixed(2)} SOL | ${Math.round(timeRemaining / 1000)}s`);

    // Alert on opportunities
    if (upPayout > 3.0) console.log('[ALERT] High UP payout!');
    if (downPayout > 3.0) console.log('[ALERT] High DOWN payout!');
  }
});

// New bets
// Note: data.amount is already in SOL (not lamports). data.side is "UP" or "DOWN".
socket.on('bet_activity', (data) => {
  const solAmount = data.amount.toFixed(2); // Already in SOL — do NOT divide by 1e9
  const user = data.username || data.wallet.slice(0, 8) + '...';
  console.log(`BET: ${user} placed ${solAmount} SOL on ${data.side} (Round #${data.roundId})`);
});

// Round settlements
// Note: winningPayout is sent as a string (already formatted server-side).
socket.on('round-settled', (data) => {
  console.log('');
  console.log('='.repeat(50));
  console.log(`SETTLED: Round #${data.roundId}`);
  console.log(`  Winner: ${data.outcome}`);
  console.log(`  Price: $${data.startPrice.toFixed(2)} → $${data.closePrice.toFixed(2)}`);
  console.log(`  Payout: ${data.winningPayout}x`); // Already a string — don't call .toFixed()
  console.log('='.repeat(50));
  console.log('');
});

// Handle disconnection
socket.on('disconnect', (reason) => {
  console.log('Disconnected:', reason);
});

// Graceful shutdown
process.on('SIGINT', () => {
  console.log('\nShutting down...');
  socket.disconnect();
  process.exit(0);
});

console.log('Listening for events... (Ctrl+C to exit)');
```

---

## 3. Position Tracker
Monitors a wallet's positions and calculates P&L.

```javascript
const API = 'https://solpreds.fun/api/bot';

// Wallet to track (can be any wallet)
const WALLET = process.argv[2] || 'YOUR_WALLET_ADDRESS';

async function fetchPositions() {
  const res = await fetch(`${API}/positions/${WALLET}`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

async function fetchMarket() {
  const res = await fetch(`${API}/market`);
  return res.json();
}

function formatSol(lamports) {
  return (lamports / 1e9).toFixed(4);
}

async function displayPositions() {
  console.clear();
  console.log('='.repeat(60));
  console.log(`SOLPREDS Position Tracker - ${new Date().toLocaleTimeString()}`);
  console.log(`Wallet: ${WALLET.slice(0, 8)}...${WALLET.slice(-8)}`);
  console.log('='.repeat(60));

  try {
    const [positions, market] = await Promise.all([
      fetchPositions(),
      fetchMarket()
    ]);

    // Summary
    const { summary } = positions;
    console.log('\nSUMMARY:');
    console.log(`  Total Bets: ${summary.totalBets}`);
    console.log(`  Wins: ${summary.wins} | Losses: ${summary.losses}`);
    console.log(`  Win Rate: ${(summary.winRate * 100).toFixed(1)}%`);
    console.log(`  Total P&L: ${formatSol(summary.totalPnl)} SOL`);
    console.log(`  Total Volume: ${formatSol(summary.totalVolume)} SOL`);

    // Open positions
    const open = positions.positions.filter(p => p.status === 'pending');
    if (open.length > 0) {
      console.log('\nOPEN POSITIONS:');
      for (const pos of open) {
        const isLive = market.live?.roundId === pos.roundId;
        const status = isLive ? 'LIVE' : 'PENDING';
        console.log(`  Round #${pos.roundId} [${status}]: ${formatSol(pos.amount)} SOL on ${pos.side}`);
      }
    }

    // Recent settled
    const settled = positions.positions.filter(p => p.status !== 'pending').slice(0, 5);
    if (settled.length > 0) {
      console.log('\nRECENT RESULTS:');
      for (const pos of settled) {
        const emoji = pos.status === 'won' ? '+' : '-';
        const pnl = pos.status === 'won' ? pos.pnl : -pos.amount;
        console.log(`  Round #${pos.roundId}: ${pos.side} ${pos.status.toUpperCase()} | ${emoji}${formatSol(Math.abs(pnl))} SOL`);
      }
    }

    // Current market
    console.log('\nCURRENT MARKET:');
    if (market.next) {
      console.log(`  Next Round #${market.next.roundId}: UP ${market.next.upPayout?.toFixed(2)}x | DOWN ${market.next.downPayout?.toFixed(2)}x`);
    }

  } catch (err) {
    console.error('Error:', err.message);
  }

  console.log('\n' + '-'.repeat(60));
  console.log('Refreshing every 30 seconds... (Ctrl+C to exit)');
}

// Initial display
displayPositions();

// Refresh every 30 seconds
setInterval(displayPositions, 30000);

// Graceful shutdown
process.on('SIGINT', () => {
  console.log('\nGoodbye!');
  process.exit(0);
});
```

**Usage:**
```bash
node position-tracker.js YOUR_WALLET_ADDRESS
```

---

## Dependencies
All examples require:

```bash
npm install tweetnacl bs58 @solana/web3.js socket.io-client
```

Set your private key:
```bash
export SOLANA_PRIVATE_KEY="your-base58-private-key"
```

> ⚠️ **Never commit private keys to version control.**