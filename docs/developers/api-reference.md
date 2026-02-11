# SOLPREDS API Reference

**Base URL:** `https://solpreds.fun/api/bot`

**Last Updated:** February 2026 (Mainnet Optimization)

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
  body: JSON.stringify({ wallet, nonce, signature: bs58.encode(signature) }),
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
  "minBetLamports": 10000000,
  "maxBetLamports": 100000000000,
  "payoutFormula": {
    "description": "Winner payout = (betAmount * 0.98) * (totalPool / winningPool) * 0.95",
    "example": "1 SOL bet, 10 SOL total, 5 SOL winning: 1.862 SOL"
  },
  "phases": {
    "NEXT": "Betting open until lockTime",
    "LIVE": "Betting locked, awaiting closeTime",
    "RESOLVED": "Winners determined"
  },
  "network": "mainnet-beta",
  "programId": "YOUR_MAINNET_PROGRAM_ID"
}
```

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
- `amount` (number): Amount in lamports (min 10,000,000)

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
    "programId": "YOUR_MAINNET_PROGRAM_ID",
    "globalState": "[PDA - derive with seeds=['global']]",
    "roundState": "[PDA - derive with seeds=['round', roundId]]",
    "vault": "[PDA - derive with seeds=['vault', roundState, 'up' or 'down']]",
    "position": "[PDA - derive with seeds=['position', roundState, userWallet]]",
    "feeVault": "[PDA - derive with seeds=['fee_vault']]",
    "userWallet": "YOUR_WALLET_ADDRESS"
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

!!! info "Optimization Note"
    As of February 2026, the program uses `place_bet_or_create` for all bets. This instruction automatically creates rounds if they don't exist, eliminating the need for separate round creation logic.

**Errors:**
- `400`: Missing fields, invalid side, amount too low
- `400`: Round locked for betting

---

### Place Bet

`POST /auth/bet`

**Auth:** Required (Bearer token)

**Body:**
- `side` (string): "Up" or "Down"
- `amount` (number): Amount in lamports (min 10,000,000)

**Response:**
```json
{
  "success": true,
  "txHash": "5X7g...",
  "roundId": 3995,
  "side": "Up",
  "amount": 100000000,
  "amountSol": 0.1,
  "timestamp": "2026-02-10T12:00:00.000Z"
}
```

!!! success "Automatic Round Creation"
    Rounds are created automatically if they don't exist. You don't need to check round status before betting.

**Example:**
```javascript
const response = await fetch(`${API}/auth/bet`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    side: 'Up',
    amount: 100_000_000 // 0.1 SOL
  })
});

const { txHash, roundId } = await response.json();
console.log(`Bet placed! TX: ${txHash}, Round: ${roundId}`);
```

**Errors:**
- `400`: Invalid side or amount
- `401`: Invalid or expired token
- `400`: Round locked (betting window closed)
- `500`: Transaction failed

---

### Claim Winnings

`POST /auth/claim`

**Auth:** Required (Bearer token)

**Body:**
- `roundId` (number): Round to claim from

**Response:**
```json
{
  "success": true,
  "txHash": "9Kp2...",
  "roundId": 3993,
  "payout": 186200000,
  "payoutSol": 0.1862,
  "rentRefund": 1130000,
  "rentRefundSol": 0.00113
}
```

!!! success "Rent Refund"
    As of February 2026, claiming automatically closes your Position account and refunds ~0.00113 SOL rent back to your wallet. This happens automatically—no extra steps needed.

**Example:**
```javascript
const response = await fetch(`${API}/auth/claim`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    roundId: 3993
  })
});

const { payout, rentRefund } = await response.json();
console.log(`Claimed ${payout} lamports + ${rentRefund} rent refund`);
```

**Errors:**
- `400`: Round not resolved yet
- `400`: Position not found or already claimed
- `400`: You didn't win this round

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

**Migration Required:** None. All existing bots and integrations continue to work without changes. The backend automatically uses the optimized instruction.

**Impact:**
- ~50% reduction in effective per-bet costs for users
- Estimated savings: ~135 SOL/year at scale
- Zero breaking changes for API consumers

---

## Need Help?

- **Documentation:** [docs.solpreds.fun](https://docs.solpreds.fun)
- **Telegram:** [t.me/SOLPREDS](https://t.me/SOLPREDS)
- **Twitter:** [@SOLPREDS_FUN](https://x.com/SOLPREDS_FUN)
- **Support:** Contact via Telegram for bot API issues