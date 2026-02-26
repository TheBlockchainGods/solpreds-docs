# SOLPREDS WebSocket Reference

Real-time market updates via Socket.io.

## Connection

**URL:** `wss://solpreds.fun` (production)  
**URL:** `ws://localhost:5000` (development)

**Connect:**

```javascript
const io = require('socket.io-client');

const socket = io('wss://solpreds.fun', {
  auth: { wallet: 'YOUR_WALLET_ADDRESS' } // optional
});

socket.on('connect', () => {
  console.log('Connected:', socket.id);
});

socket.on('disconnect', () => {
  console.log('Disconnected');
});
```

**Note:** No authentication required for market events. The optional `wallet` in `auth` is used for chat participation and active user tracking, not for bot trading events.

---

## Events

### market-update

**Trigger:** Every 10 seconds

**Payload:**

```json
{
  "timestamp": 1234567890,
  "currentRoundId": 3995,
  "live": {
    "roundId": 3994,
    "upPool": 1000000000,
    "downPool": 500000000,
    "timeRemaining": 180000
  },
  "next": {
    "roundId": 3995,
    "upPool": 0,
    "downPool": 0,
    "timeRemaining": 240000
  }
}
```

> **Note:** Payout multipliers (`upPayout`, `downPayout`) are **not** included in this event. Use the REST `GET /market` endpoint to get payouts, or calculate them from the pool values yourself.

---

### bet_activity

**Trigger:** When any user places a bet

**Payload:**

```json
{
  "wallet": "ABC...",
  "username": "player123",
  "amount": 0.1,
  "side": "UP",
  "roundId": 3995
}
```

> **Note:** `amount` is in SOL (not lamports). `side` is `"UP"` or `"DOWN"` (uppercase).

---

### round-settled

**Trigger:** When a round is resolved

**Payload:**

```json
{
  "roundId": 3994,
  "outcome": "Up",
  "startPrice": 250.50,
  "closePrice": 251.75,
  "upPool": 1000000000,
  "downPool": 500000000,
  "totalPool": 1500000000,
  "winningPayout": "1.39",
  "timestamp": 1234567890
}
```

> **Note:** `winningPayout` is a string (e.g. `"1.39"`), not a number — do not call `.toFixed()` on it.

---

## Complete Example

```javascript
const io = require('socket.io-client');

const socket = io('wss://solpreds.fun');

// Connection events
socket.on('connect', () => {
  console.log('Connected to SOLPREDS');
});

socket.on('connect_error', (err) => {
  console.error('Connection failed:', err.message);
});

// Market updates (every 10s)
// Note: upPayout/downPayout are NOT in this event — calculate from pools or use REST /market
socket.on('market-update', (data) => {
  if (data.next) {
    const { roundId, upPool, downPool, timeRemaining } = data.next;
    const totalPool = (upPool + downPool) / 1e9;

    // Calculate payouts from pool ratios (95% payout pool)
    const upPayout = totalPool > 0 ? (totalPool * 0.95) / (upPool / 1e9) : 0;
    const downPayout = totalPool > 0 ? (totalPool * 0.95) / (downPool / 1e9) : 0;

    console.log(`Round ${roundId}: UP ~${upPayout.toFixed(2)}x | DOWN ~${downPayout.toFixed(2)}x | ${Math.round(timeRemaining / 1000)}s`);

    // Alert on high payout opportunities
    if (upPayout > 2.5 || downPayout > 2.5) {
      console.log('>>> HIGH PAYOUT DETECTED <<<');
    }
  }
});

// Watch for bets
// Note: amount is already in SOL — do NOT divide by 1e9
// Note: side is "UP" or "DOWN" (uppercase)
socket.on('bet_activity', (data) => {
  console.log(`${data.username || data.wallet.slice(0, 8)} bet ${data.amount.toFixed(3)} SOL on ${data.side}`);
});

// Round results
// Note: winningPayout is a string — do NOT call .toFixed() on it
socket.on('round-settled', (data) => {
  console.log(`Round ${data.roundId}: ${data.outcome} wins at ${data.winningPayout}x`);
  console.log(`Price: ${data.startPrice} → ${data.closePrice}`);
});

// Reconnection logic (built into socket.io)
socket.on('disconnect', (reason) => {
  console.log('Disconnected:', reason);
  // socket.io auto-reconnects by default
});

// Keep process running
process.on('SIGINT', () => {
  socket.disconnect();
  process.exit();
});
```

---

## Tips

- **Don't poll REST API** — Use WebSocket for real-time data
- **Auto-reconnect** — Socket.io handles reconnection automatically
- **Rate limits don't apply** — WebSocket events are push-only, no limits
- **Combine with REST** — Use REST for auth and prepare-bet, WebSocket for monitoring
- **Payouts via REST** — If you need payout multipliers, fetch `GET /market` separately or calculate from pool ratios
