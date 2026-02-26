# Introduction to SOLPREDS

SOLPREDS is a decentralized prediction market platform built on Solana. It combines short-term price prediction rounds with permissionless community-created markets — giving anyone the ability to trade on outcomes or create their own markets and earn from them.

---

## Two Products, One Platform

### 🎯 Prediction Rounds
Available at [solpreds.fun/solana](https://solpreds.fun/solana) (mainnet) and [devnet.solpreds.fun/solana](https://devnet.solpreds.fun/solana) (devnet).

Bet on whether SOL/USD will go **UP** or **DOWN** in 5-minute windows. Rounds run continuously, payouts are automatic, and outcomes are determined by the [Pyth Network](https://pyth.network) price oracle — no human intervention.

### 🌐 Community Markets
Available at [solpreds.fun](https://solpreds.fun).

A permissionless prediction market where anyone can create a market on any topic — crypto, sports, politics, or anything else. Users trade outcome shares powered by SOLPREDS' custom bonding curve AMM. Creators earn **1% of trading fees** and **1% of the final liquidity jackpot** when a market resolves.

!!! note "Beta Notice"
    Community Markets currently runs on virtual SOL balances during beta. Real funds are not at risk while the system is being tested.

---

## Why SOLPREDS?

### 🚀 Built on Solana
Sub-second transactions and minimal fees (~$0.00001 per transaction) make high-frequency prediction trading practical for everyone.

### 🔒 Fully Transparent
All bets and trades are recorded on-chain and verifiable on [Solana Explorer](https://explorer.solana.com). No centralized control over outcomes.

### 🎲 Fair & Automated
Pyth Network oracle pricing, automatic round settlements, and staking-based decentralized market validation mean no human can manipulate results.

### 💰 Creator Economy
Anyone can launch a market and earn from it. SOLPREDS is the only prediction market platform where permissionless market creation comes with built-in revenue sharing for creators.

### 🤝 Referral System
A 5-level referral program works across both Prediction Rounds and Community Markets. Share your link and earn from every bet placed by users you bring in.

---

## The SOLPREDS Bonding Curve

Most prediction markets use order books — you need someone on the other side of your trade before anything executes. SOLPREDS takes a different approach, merging a traditional AMM (the same concept powering Uniswap) with a prediction market using a **dynamic virtual base bonding curve**.

Think of it as **Pump.fun meets Polymarket**.

### How It Works

The price of any outcome share is determined by:
```
price = (b + shares) / (N × b + totalShares)
```

Where:
- `b` = dynamic virtual base (starts at a set value, adjusts with volume)
- `shares` = shares held for a specific outcome (e.g., YES)
- `N` = number of outcomes (2 for binary YES/NO, more for multiple choice)
- `totalShares` = sum of all shares across all outcomes

### The Key Innovation: Dynamic Virtual Base

The dynamic virtual base (`b`) acts as **synthetic liquidity** — you don't need real money in the pool for the market to function. This means:

- **Zero liquidity needed upfront** — anyone can create a market and the very first trade gets instant execution at a fair price
- **Early price discovery** — early traders move prices significantly, reflecting genuine sentiment
- **Natural stability** — as volume grows, the base adjusts so later trades have less price impact and less slippage
- **Mathematical fairness** — all outcome prices always sum to 100%, guaranteed by the formula

This design removes the biggest barrier to permissionless prediction markets: the cold start problem. No market maker, no seed liquidity, no waiting. A market works from the very first trade.

---

## Key Concepts

**Prediction Round**: A 5-minute window where users bet on SOL price direction (UP or DOWN)

**Community Market**: A user-created binary or multiple-choice market on any topic

**Lock Price**: The price when betting closes in a Prediction Round

**Close Price**: The final price that determines winners in a Prediction Round

**Payout Multiplier**: Your potential winnings based on pool distribution

**Dynamic Virtual Base**: The synthetic liquidity mechanism that enables zero-liquidity market creation

**Market Creator**: Anyone who deploys a Community Market and earns fees from it

**$SOLPREDS Token**: The native platform token used for governance, staking for market validation, and fee reductions

---

## Getting Started

### Play Prediction Rounds
1. [Set up a Solana wallet](how-to-play.md)
2. [Place your first bet](how-to-play.md#how-to-bet)
3. [Claim your winnings](how-to-play.md#claiming-rewards)

### Try on Devnet First
1. [Set up a Solana wallet](how-to-play-devnet.md)
2. [Get free devnet SOL](how-to-play-devnet.md#step-3-get-free-devnet-sol)
3. [Place your first bet](how-to-play-devnet.md#placing-bets)

### Build a Bot
Check out the [Developer Quickstart](../developers/quickstart.md) to start trading programmatically via the API.

---

## Learn More

- [Presale](../presale.md) — Join the $SOLPREDS token presale
- [Community Markets](../community-markets.md) — Create and trade on any topic
- [Airdrop](../airdrop.md) — Earn $SOLPREDS rewards
- [API Reference](../developers/api-reference.md) — Build bots and integrations