# SOLPREDS Developer Quickstart

Build bots to trade on the Solana prediction market.

## What the API Does

- **Market Data:** Real-time rounds, odds, liquidity, prices
- **Authentication:** Wallet signature auth (non-custodial)
- **Trading:** Prepare bet transactions for on-chain submission
- **WebSocket:** Live updates for market changes and settlements

## Base URLs

**REST API:** `https://solpreds.fun/api/bot`  
**WebSocket:** `wss://solpreds.fun`

## Available Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/market` | GET | No | Current round state, odds, liquidity |
| `/history` | GET | No | Past rounds and recent bets |
| `/rules` | GET | No | Fees, timing, payout formulas |
| `/positions/:wallet` | GET | No | Positions for any wallet |
| `/price` | GET | No | SOL/USD price from Pyth |
| `/auth/nonce` | GET | No | Get nonce for signing |
| `/auth/verify` | POST | No | Submit signature, get token |
| `/auth/status` | GET | Yes | Check token validity |
| `/auth/positions` | GET | Yes | Your detailed positions |
| `/auth/prepare-bet` | POST | Yes | Get transaction data |

## Authentication Flow
```
1. GET /auth/nonce?wallet=YOUR_WALLET
   → Returns { nonce, message, expiresIn }

2. Sign the message with your wallet private key
   → Use nacl.sign.detached() or @solana/web3.js

3. POST /auth/verify { wallet, nonce, signature }
   → Returns { token, expiresAt }

4. Use token in requests:
   Authorization: Bearer YOUR_TOKEN
```

Token expires in 24 hours.

## Quick Code Example
```javascript
const nacl = require('tweetnacl');
const bs58 = require('bs58');
const io = require('socket.io-client');

const API = 'https://solpreds.fun/api/bot';

// Your wallet keypair (never expose private key!)
const keypair = /* your Solana keypair */;
const wallet = keypair.publicKey.toBase58();

// 1. Authenticate
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
  
  return (await verifyRes.json()).token;
}

// 2. Get market data
async function getMarket() {
  const res = await fetch(`${API}/market`);
  return res.json();
}

// 3. Listen to WebSocket events
function connectWebSocket() {
  const socket = io('wss://solpreds.fun');
  
  socket.on('market-update', (data) => {
    console.log('Market:', data.next?.upPayout, data.next?.downPayout);
  });
  
  socket.on('round-settled', (data) => {
    console.log(`Round ${data.roundId}: ${data.outcome} won`);
  });
}

// Run
(async () => {
  const token = await authenticate();
  console.log('Authenticated:', token);
  
  const market = await getMarket();
  console.log('Current round:', market.currentRoundId);
  
  connectWebSocket();
})();
```

## WebSocket Events

| Event | Trigger | Data |
|-------|---------|------|
| `market-update` | Every 10s | Round state, odds, time remaining |
| `bet_activity` | New bet placed | wallet, amount, side, roundId |
| `round-settled` | Round resolved | roundId, outcome, prices, payout |

## Rate Limits

- **Public endpoints:** 100 req/min per IP
- **Authenticated endpoints:** 100 reads/min, 10 writes/min per wallet
- **Headers:** `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`

## Next Steps

- [Full API Reference](api-reference.md)
- [WebSocket Reference](websocket.md)
- [Code Examples](examples.md)