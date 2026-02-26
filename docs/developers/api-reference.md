# SOLPREDS API Reference

**Base URL:** `https://solpreds.fun/api/bot`

**Last Updated:** February 2026 (Mainnet Optimization)

---

## Overview

The SOLPREDS Bot API provides endpoints for programmatic betting on Solana price predictions. 

**Important:** This API prepares transaction data for your bot to sign and submit. The server does NOT submit transactions on your behalf. Your bot must:
1. Call `/auth/prepare-bet` to get transaction accounts and instructions
2. Construct the transaction client-side
3. Sign with your wallet keypair
4. Submit to Solana RPC yourself

---

## Authentication Endpoints

### Get Nonce

`GET /auth/nonce`

**Auth:** Not required

**Parameters:**
- `wallet` (query, required): Solana wallet address

**Response:**
```json
{
  "nonce": "solpreds_1234567890_abc123",
  "message": "SOLPREDS Bot Authentication\nWallet: ABC...\nNonce: solpreds_...\nTimestamp: 1234567890",
  "expiresIn": 300
}
```

**Example:**
```javascript
const res = await fetch(`${API}/auth/nonce?wallet=${wallet}`);
const { nonce, message } = await res.json();
// Sign the `message` with your wallet
```

---

### Verify Signature

`POST /auth/verify`

**Auth:** Not required

**Body:**
- `wallet` (string): Wallet address
- `nonce` (string): Nonce from `/auth/nonce`
- `signature` (string): Base58-encoded signature

**Response:**
```json
{
  "success": true,
  "token": "bot_ABC123_1234567890_xyz",
  "wallet": "ABC...",
  "expiresAt": "2026-01-28T12:00:00.000Z"
}
```

**Example:**
```javascript
const signature = nacl.sign.detached(messageBytes, keypair.secretKey);
const res = await fetch(`${API}/auth/verify`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ 
    wallet, 
    nonce, 
    signature: bs58.encode(signature) 
  }),
});
const { token } = await res.json();
```

---

### Check Auth Status

`GET /auth/status`

**Auth:** Required (Bearer token)

**Response:**
```json
{
  "authenticated": true,
  "wallet": "ABC..."
}
```

---

## Public Endpoints

### Get Market State

`GET /market`

**Auth:** Not required

**Response:**
```json
{
  "timestamp": 1234567890,
  "currentRoundId": 3995,
  "live": {
    "roundId": 3994,
    "phase": "LIVE",
    "upPool": 1000000000,
    "downPool": 500000000,
    "upPayout": 1.39,
    "downPayout": 2.79,
    "timeRemaining": 180000,
    "startPrice": 250.50,
    "lockTime": 1234567000,
    "closeTime": 1234867000
  },
  "next": {
    "roundId": 3995,
    "phase": "NEXT",
    "upPool": 0,
    "downPool": 0,
    "upPayout": 1.96,
    "downPayout": 1.96,
    "timeRemaining": 240000
  },
  "serverTime": 1234567890,
  "phaseDuration": 300000,
  "roundDuration": 600000
}
```

---

### Get History

`GET /history`

**Auth:** Not required

**Parameters:**
- `limit` (query, optional): 1-100, default 20
- `offset` (query, optional): Pagination offset, default 0

**Response:**
```json
{
  "rounds": [
    {
      "roundId": 3993,
      "startPrice": 250.00,
      "closePrice": 251.50,
      "upPool": 2000000000,
      "downPool": 1500000000,
      "totalPool": 3500000000,
      "resolved": true,
      "outcome": "Up"
    }
  ],
  "recentBets": [
    {
      "roundId": 3995,
      "side": "Up",
      "amount": 100000000,
      "timestamp": "2026-01-27T12:00:00.000Z"
    }
  ],
  "stats": {
    "totalRounds": 100,
    "upWins": 52,
    "downWins": 48,
    "upWinRate": 0.52
  }
}
```

---

### Get Rules

`GET /rules`

**Auth:** Not required

**Response:**
```json
{
  "phaseDurationSeconds": 300,
  "roundDurationSeconds": 600,
  "epochStartMs": 1767000000000,
  "betFeePercent": 2,
  "winFeePercent": 5,
  "referralLevels": 5,
  "referralPercentages": [25, 3.5, 2.5, 2, 1],
  "minBetLamports": 1000000,
  "minBetSol": 0.001,
  "maxBetPerUserPerRoundSol": 1000,
  "payoutFormula": {
    "description": "Winner payout = (betAmount * 0.98) * (totalPool / winningPool) * 0.95",
    "example": "1 SOL bet, 10 SOL total, 5 SOL winning: 1.862 SOL"
  },
  "edgeCases": {
    "tie": "If startPrice === closePrice, all bets refunded minus fees",
    "oracleFailure": "If price unavailable at closeTime, round void, all bets refunded",
    "emptyPool": "If no bets on winning side, losing side gets refund minus fees"
  },
  "phases": {
    "NEXT": "Betting open until lockTime",
    "LIVE": "Betting locked, awaiting closeTime",
    "RESOLVED": "Winners determined"
  },
  "network": "mainnet-beta",
  "programId": "2Bivb6opV9DbgJu9DWSRMkuA53skap7XgYo7EEBSNn21",
  "pythFeed": "H6ARHf6YXhGYeQfUzQNGk6rDNnLBQKrenN712K4AQJEG"
}
```

**Note:** `minBetLamports` is **1,000,000 (0.001 SOL)**, not 10,000,000.

---

### Get Positions (Public)

`GET /positions/:wallet`

**Auth:** Not required

**Parameters:**
- `wallet` (path): Wallet address to query

**Response:**
```json
{
  "wallet": "ABC...",
  "positions": [
    {
      "roundId": 3993,
      "side": "Up",
      "amount": 100000000,
      "status": "won",
      "pnl": 86200000,
      "claimed": false,
      "txHash": "abc123...",
      "timestamp": "2026-01-27T12:00:00.000Z",
      "round": {
        "startPrice": 250.00,
        "closePrice": 251.50,
        "outcome": "Up"
      }
    }
  ],
  "summary": {
    "totalBets": 10,
    "wins": 6,
    "losses": 4,
    "winRate": 0.6,
    "totalPnl": 500000000,
    "totalVolume": 1000000000
  }
}
```

---

### Get Price

`GET /price`

**Auth:** Not required

**Response:**
```json
{
  "price": 250.50,
  "confidence": 0.15,
  "timestamp": 1234567890
}
```

---

### Get API Documentation

`GET /docs`

**Auth:** Not required

**Response:**
```json
{
  "version": "2.0",
  "endpoints": {
    "public": [...],
    "authenticated": [...]
  },
  "examples": {...}
}
```

Returns a self-describing API reference with all available endpoints, parameters, and examples.

---

## Authenticated Endpoints

### Get My Positions

`GET /auth/positions`

**Auth:** Required (Bearer token)

**Response:**
```json
{
  "wallet": "ABC...",
  "positions": [
    {
      "roundId": 3993,
      "side": "Up",
      "amount": 100000000,
      "amountSol": 0.1,
      "status": "won",
      "pnl": 86200000,
      "pnlSol": 0.0862,
      "potentialPayout": 0,
      "claimed": false,
      "txHash": "abc123..."
    }
  ],
  "openPositions": [],
  "summary": {
    "totalBets": 10,
    "wins": 6,
    "losses": 4,
    "winRate": 0.6,
    "totalPnlLamports": 500000000,
    "totalPnlSol": 0.5,
    "openPositionCount": 0
  }
}
```

---

### Prepare Bet

`POST /auth/prepare-bet`

**Auth:** Required (Bearer token)

**Body:**
- `roundId` (number): Round to bet on
- `side` (string): "Up" or "Down"
- `amount` (number): Amount in lamports (min 1,000,000 = 0.001 SOL)

**Response:**
```json
{
  "success": true,
  "roundId": 3995,
  "side": "Up",
  "amount": 100000000,
  "amountSol": 0.1,
  "lockTime": 1234567890,
  "timeRemaining": 180000,
  "serverTime": 1234567710,
  "accounts": {
    "programId": "2Bivb6opV9DbgJu9DWSRMkuA53skap7XgYo7EEBSNn21",
    "globalState": "7vMW8kVqWzqXoZ8T1L4m3UfDJNnzrqeN5K3rJYjLp9Qr",
    "roundState": "[PDA - derive with seeds=['round', roundId]]",
    "vault": "[PDA - derive with seeds=['vault', roundState, 'up' or 'down']]",
    "position": "[PDA - derive with seeds=['position', roundState, userWallet]]",
    "feeVault": "[PDA - derive with seeds=['fee_vault']]",
    "userWallet": "YOUR_WALLET_ADDRESS",
    "systemProgram": "11111111111111111111111111111111"
  },
  "instructions": {
    "method": "place_bet_or_create",
    "args": {
      "side": { "up": {} },
      "amount": "100000000"
    }
  },
  "fees": {
    "betFeePercent": 2,
    "netBetAmount": 98000000,
    "estimatedTxFee": 5000
  }
}
```

!!! warning "Client-Side Transaction Construction Required"
    This endpoint **only prepares** the bet data. You must:
    
    1. Use the returned `accounts` and `instructions` to build the transaction
    2. Sign it with your wallet keypair
    3. Submit it to Solana RPC yourself
    
    The server does NOT submit transactions on your behalf.

**Example (Client-Side Transaction Construction):**
```javascript
import { Connection, Transaction, TransactionInstruction, PublicKey } from '@solana/web3.js';
import * as borsh from 'borsh';

// 1. Get prepared bet data
const response = await fetch(`${API}/auth/prepare-bet`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    roundId: 3995,
    side: 'Up',
    amount: 100_000_000 // 0.1 SOL
  })
});

const { accounts, instructions } = await response.json();

// 2. Build transaction instruction
const instructionData = borsh.serialize(
  InstructionSchema, // Define based on your program IDL
  {
    instruction: 'PlaceBetOrCreate',
    side: instructions.args.side,
    amount: instructions.args.amount
  }
);

const ix = new TransactionInstruction({
  keys: [
    { pubkey: new PublicKey(accounts.globalState), isSigner: false, isWritable: true },
    { pubkey: new PublicKey(accounts.roundState), isSigner: false, isWritable: true },
    { pubkey: new PublicKey(accounts.vault), isSigner: false, isWritable: true },
    { pubkey: new PublicKey(accounts.position), isSigner: false, isWritable: true },
    { pubkey: new PublicKey(accounts.feeVault), isSigner: false, isWritable: true },
    { pubkey: keypair.publicKey, isSigner: true, isWritable: true },
    { pubkey: new PublicKey(accounts.systemProgram), isSigner: false, isWritable: false }
  ],
  programId: new PublicKey(accounts.programId),
  data: Buffer.from(instructionData)
});

// 3. Sign and send
const tx = new Transaction().add(ix);
const connection = new Connection('https://api.mainnet-beta.solana.com');
const signature = await connection.sendTransaction(tx, [keypair]);
await connection.confirmTransaction(signature);

console.log(`Bet placed! TX: ${signature}`);
```

**Minimum Bet:** 1,000,000 lamports (0.001 SOL)

**Errors:**
- `400`: Missing fields, invalid side, amount too low
- `400`: Round locked for betting
- `400`: Amount below minimum (1,000,000 lamports)
- `401`: Invalid or expired token

---

### Claiming Winnings

!!! warning "No Server-Side Claim Endpoint"
    There is **NO** `POST /auth/claim` endpoint. You must construct and submit the claim transaction yourself using the Solana program directly.

**To claim winnings:**

1. Query your positions with `GET /auth/positions` to find unclaimed wins
2. For each unclaimed position, construct a `claim_winnings` transaction:
   - Program: `2Bivb6opV9DbgJu9DWSRMkuA53skap7XgYo7EEBSNn21`
   - Instruction: `claim_winnings`
   - Accounts: globalState, roundState, position, vault, userWallet, systemProgram
3. Sign and submit the transaction

**Example (Client-Side Claim):**
```javascript
import { Connection, Transaction, TransactionInstruction, PublicKey } from '@solana/web3.js';

// 1. Get unclaimed positions
const positionsRes = await fetch(`${API}/auth/positions`, {
  headers: { 'Authorization': `Bearer ${token}` }
});
const { positions } = await positionsRes.json();

const unclaimedWins = positions.filter(p => 
  p.status === 'won' && !p.claimed
);

// 2. For each unclaimed win, build claim transaction
for (const pos of unclaimedWins) {
  const [roundState] = PublicKey.findProgramAddressSync(
    [Buffer.from('round'), Buffer.from(pos.roundId.toString())],
    new PublicKey(PROGRAM_ID)
  );
  
  const [position] = PublicKey.findProgramAddressSync(
    [Buffer.from('position'), roundState.toBuffer(), keypair.publicKey.toBuffer()],
    new PublicKey(PROGRAM_ID)
  );
  
  const [vault] = PublicKey.findProgramAddressSync(
    [Buffer.from('vault'), roundState.toBuffer(), Buffer.from(pos.side.toLowerCase())],
    new PublicKey(PROGRAM_ID)
  );
  
  // Build claim instruction
  const instructionData = borsh.serialize(
    InstructionSchema,
    { instruction: 'ClaimWinnings' }
  );
  
  const ix = new TransactionInstruction({
    keys: [
      { pubkey: globalState, isSigner: false, isWritable: true },
      { pubkey: roundState, isSigner: false, isWritable: true },
      { pubkey: position, isSigner: false, isWritable: true },
      { pubkey: vault, isSigner: false, isWritable: true },
      { pubkey: keypair.publicKey, isSigner: true, isWritable: true },
      { pubkey: SystemProgram.programId, isSigner: false, isWritable: false }
    ],
    programId: new PublicKey(PROGRAM_ID),
    data: Buffer.from(instructionData)
  });
  
  // Sign and send
  const tx = new Transaction().add(ix);
  const signature = await connection.sendTransaction(tx, [keypair]);
  await connection.confirmTransaction(signature);
  
  console.log(`Claimed ${pos.pnlSol} SOL from round ${pos.roundId}. TX: ${signature}`);
}
```

!!! success "Rent Refund"
    Claiming automatically closes your Position account and refunds ~0.00113 SOL rent back to your wallet. This happens automatically—no extra steps needed.

---

## Rate Limits

| Type | Limit | Scope |
|------|-------|-------|
| Public reads | 100/min | Per IP |
| Auth reads | 100/min | Per wallet |
| Auth writes | 10/min | Per wallet |

**Headers returned:**
- `X-RateLimit-Limit`: Max requests
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Seconds until reset

**429 Response:**
```json
{
  "error": "Rate limit exceeded",
  "retryAfter": 30
}
```

---

## Error Responses

| Status | Meaning |
|--------|---------|
| 400 | Bad request (missing/invalid params) |
| 401 | Invalid or expired auth token |
| 429 | Rate limit exceeded |
| 500 | Server error |

**Error format:**
```json
{
  "error": "Description of what went wrong"
}
```

---

## February 2026 Optimization Changes

The SOLPREDS program was optimized for mainnet deployment with the following improvements:

**Cost Reductions:**
- Position accounts now auto-close after claiming, refunding ~0.00113 SOL rent to users
- Compute units reduced by ~10,000 per transaction (~50% savings)
- Program binary size reduced by ~5-10KB

**API Changes:**
- ~~`place_bet` instruction~~ (removed - deprecated)
- ✅ `place_bet_or_create` used for all betting operations
- ✅ Automatic round creation (no need to check if round exists)
- ✅ Automatic Position account cleanup on claim (rent refunded)

**Migration Required:** None. All existing bots continue to work. The `/auth/prepare-bet` endpoint automatically uses the optimized instruction.

**Impact:**
- ~50% reduction in effective per-bet costs for users
- Estimated savings: ~135 SOL/year at scale
- Zero breaking changes for API consumers

---

## Quick Start Guide

**1. Authenticate:**
```javascript
// Get nonce
const { message } = await fetch(`${API}/auth/nonce?wallet=${wallet}`).then(r => r.json());

// Sign message
const signature = nacl.sign.detached(
  Buffer.from(message, 'utf8'), 
  keypair.secretKey
);

// Verify and get token
const { token } = await fetch(`${API}/auth/verify`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    wallet,
    nonce,
    signature: bs58.encode(signature)
  })
}).then(r => r.json());
```

**2. Get Market State:**
```javascript
const market = await fetch(`${API}/market`).then(r => r.json());
console.log(`Next round: ${market.next.roundId}, UP payout: ${market.next.upPayout}x`);
```

**3. Place Bet:**
```javascript
// Prepare bet
const prepared = await fetch(`${API}/auth/prepare-bet`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    roundId: market.next.roundId,
    side: 'Up',
    amount: 100_000_000 // 0.1 SOL
  })
}).then(r => r.json());

// Build, sign, and submit transaction (see examples above)
const tx = buildTransaction(prepared.accounts, prepared.instructions);
const signature = await connection.sendTransaction(tx, [keypair]);
```

**4. Check Positions:**
```javascript
const { positions } = await fetch(`${API}/auth/positions`, {
  headers: { 'Authorization': `Bearer ${token}` }
}).then(r => r.json());

console.log(`You have ${positions.length} positions`);
```

**5. Claim Winnings:**
```javascript
// Find unclaimed wins
const wins = positions.filter(p => p.status === 'won' && !p.claimed);

// Build and submit claim transactions (see examples above)
for (const win of wins) {
  const claimTx = buildClaimTransaction(win);
  await connection.sendTransaction(claimTx, [keypair]);
}
```

---

## Important Notes

!!! warning "Transaction Construction"
    This API does **NOT** submit transactions on your behalf. You must:
    - Build transactions client-side using the provided accounts/instructions
    - Sign with your wallet keypair
    - Submit to Solana RPC yourself
    
    The server only prepares the data you need.

!!! info "Minimum Bet"
    Minimum bet is **1,000,000 lamports (0.001 SOL)**, not 0.01 SOL as previously documented.

!!! success "Rent Refunds"
    Position accounts auto-close when claiming, refunding ~0.00113 SOL rent to you automatically.

---

## Need Help?

- **Documentation:** [docs.solpreds.fun](https://docs.solpreds.fun)
- **Telegram:** [t.me/SOLPREDS](https://t.me/SOLPREDS)
- **Twitter:** [@SOLPREDS_FUN](https://x.com/SOLPREDS_FUN)
- **Support:** Contact via Telegram for bot API issues