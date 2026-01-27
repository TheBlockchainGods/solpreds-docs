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
    "phase": "LIVE",
    "upPool": 1000000000,
    "downPool": 500000000,
    "upPayout": 1.39,
    "downPayout": 2.79,
    "timeRemaining": 180000,
    "startPrice": 250.50
  },
  "next": {
    "roundId": 3995,
    "phase": "NEXT",
    "upPool": 0,
    "downPool": 0,
    "upPayout": 1.96,
    "downPayout": 1.96,
    "timeRemaining": 240000
  }
}
```

### bet_activity

**Trigger:** When any user places a bet

**Payload:**

```json
{
  "wallet": "ABC...",
  "username": "player123",
  "amount": 100000000,
  "side": "Up",
  "roundId": 3995
}
```

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
  "winningPayout": 1.39
}
```

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
socket.on('market-update', (data) => {
  if (data.next) {
    const { roundId, upPayout, downPayout, timeRemaining } = data.next;
    console.log(`Round ${roundId}: UP ${upPayout.toFixed(2)}x | DOWN ${downPayout.toFixed(2)}x | ${Math.round(timeRemaining/1000)}s`);
    
    // Alert on high payout opportunities
    if (upPayout > 2.5 || downPayout > 2.5) {
      console.log('>>> HIGH PAYOUT DETECTED <<<');
    }
  }
});

// Watch for bets
socket.on('bet_activity', (data) => {
  const solAmount = data.amount / 1e9;
  console.log(`${data.username || data.wallet.slice(0,8)} bet ${solAmount} SOL on ${data.side}`);
});

// Round results
socket.on('round-settled', (data) => {
  console.log(`Round ${data.roundId}: ${data.outcome} wins at ${data.winningPayout.toFixed(2)}x`);
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

- **Don't poll REST API** - Use WebSocket for real-time data
- **Auto-reconnect** - Socket.io handles reconnection automatically
- **Rate limits don't apply** - WebSocket events are push-only, no limits
- **Combine with REST** - Use REST for auth and prepare-bet, WebSocket for monitoring
