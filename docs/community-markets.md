# Community Prediction Markets

**Create any market. Trade outcomes. Validate results.**

!!! success "Status: LIVE BETA"
    Community Markets is live with virtual tokens for testing! Real $SOLPREDS integration coming after presale.

---

## What Makes Us Different

SOLPREDS Community Markets is the first **fully decentralized prediction market** on Solana with permissionless market creation and game-theory-based resolution.

### Comparison

| Feature | Polymarket | Kalshi | **SOLPREDS Markets** |
|---------|------------|--------|---------------------|
| **Market Creation** | ❌ Curated | ❌ Regulated | ✅ Permissionless |
| **Resolution** | Centralized UMA | Centralized CFTC | ✅ Decentralized Staking |
| **Creator Earnings** | None | None | ✅ 1% trading fees + 1% liquidity jackpot |
| **Trading Model** | Order Book | Order Book | ✅ Bonding Curve AMM |
| **Barriers** | High liquidity needed | US citizens only | ✅ Open to all |
| **Market Types** | Binary + Multi-Outcome | Binary only | ✅ Binary + Multi-Choice |

---

## How It Works

### 1. Create Any Market (0.1 Virtual SOL)

Anyone can create prediction markets:

**Binary Markets (YES/NO):**
- "Will BTC hit $100K by March?"
- "Will SOL flip ETH in TVL?"
- "Will this memecoin bond in the next hour?"

**Multi-Choice Markets:**
- "Who wins the 2026 World Cup?" → Brazil, Argentina, France, Germany...
- "What will SOL price be Dec 31?" → <$100, $100-$200, $200-$500, >$500
- "Which AI company IPOs first?" → OpenAI, Anthropic, xAI, Mistral

**Creator Benefits:**
- Earn **1% of all trading fees**
- Earn **1% of the final liquidity jackpot** when market resolves
- No liquidity needed — bonding curve handles it
- Claimable earnings dashboard
- Passive income from market activity

**Cost:** 0.1 SOL (virtual SOL during beta)

---

### 2. Dynamic Bonding Curve AMM

Markets use a **dynamic virtual base AMM** for instant execution:

**Price Formula:**
```
price_i = (b + shares_i) / (N × b + total_shares)
```

Where:
- `b` = dynamic virtual base (adjusts with volume)
- `N` = number of outcomes (2 for binary, more for multi-choice)
- `shares_i` = shares held for outcome i
- `total_shares` = sum of all outcome shares

**Key Features:**
- No order books needed
- Instant trades at fair prices
- Price impact decreases as volume grows
- All outcome prices always sum to 100%
- Natural price discovery

**How it works:**
```
Binary Market: "Will SOL hit $200 by March?"

Initial:        YES: 50.0%  |  NO: 50.0%

Alice buys $100 YES
→               YES: 59.2%  |  NO: 40.8%

Bob buys $50 NO
→               YES: 53.1%  |  NO: 46.9%

Prices reflect real-time probability!
```

**Multi-Choice Example:**
```
Market: "Who wins MVP 2026?"

Initial:        Player A: 25% | Player B: 25% | Player C: 25% | Player D: 25%

After trading:  Player A: 42% | Player B: 28% | Player C: 18% | Player D: 12%
```

---

### 3. Decentralized Resolution

Markets resolve through a **3-phase staking system** with real economic stakes:

#### Phase 1: Proposal
- Market ends (auto at deadline)
- Validators stake **10,000 $SOLPREDS** to propose outcome
- **6-hour dispute window** begins
- If undisputed → Validator earns **1% of market pot**

!!! note "Stake Amount"
    During beta, the stake is fixed at 10,000 virtual $SOLPREDS. On mainnet, the required stake targets ~$200 USD equivalent, calculated dynamically using the live $SOLPREDS price via Jupiter.

#### Phase 2: Challenge Window (6 hours)

**Path A - No Dispute (90% of cases):**
- No one challenges within 6 hours
- Market auto-finalizes with proposed outcome
- Winners can claim payouts
- Validator gets: stake back + 1% pot reward

**Path B - Disputed:**
- Someone disagrees and stakes 10,000 $SOLPREDS
- Proposes different outcome
- Triggers community vote

#### Phase 3: Community Vote (24 hours - If Disputed)
- All $SOLPREDS holders vote
- Voting power is **balance-weighted** — larger holdings carry more influence
- **Minimum 3 unique voters** required (quorum)
- Majority wins

**Slashing Distribution (Loser's Stake):**
- **25%** → Winning staker bonus
- **50%** → Split among winning voters (by voting power)
- **25%** → Burned permanently

**This ensures:**
- ✅ Validators only propose correct outcomes (lose stake if wrong)
- ✅ Challengers only dispute when confident (lose stake if wrong)
- ✅ Community votes accurately (earn from being right)
- ✅ No centralized oracle needed

---

## Virtual Economy (Beta)

During beta testing, Community Markets operates with virtual assets:

**Every new user receives:**
- **1,000 Virtual SOL** for trading
- **1,000,000 Virtual $SOLPREDS** for validation/voting

**Benefits:**
- Zero risk testing
- Build reputation and strategies
- Creator earnings accumulate (claimable)
- Learn the system before mainnet

**Mainnet Transition:**
- Real $SOLPREDS token integration
- Real SOL trading
- Creator earnings become real
- Referral rewards become real

---

## Fee Structure

### Trading Fees (3% per trade)
| Recipient | Share |
|-----------|-------|
| Market Creator | 1% |
| Platform | 2% |

### Claiming Winnings (5% of payout)
| Recipient | Share |
|-----------|-------|
| Market Creator | 1% |
| Platform | 3% |
| Validator Reward | 1% |

!!! info "How Fees Apply"
    The 3% trade fee is deducted when you buy or sell shares. The 5% claim fee is deducted from your payout when you claim winnings. They apply at different stages and to different amounts.

!!! tip "Creator Earnings"
    Popular markets generate passive income! A market with $10,000 in trading volume earns the creator $100 (1% of fees) plus 1% of the final liquidity jackpot at resolution. All earnings are claimable anytime from the creator dashboard.

---

## 5-Level Referral System

Unified across Prediction Rounds and Community Markets:

| Level | Commission |
|-------|-----------|
| **Level 1** (Direct) | 25% |
| **Level 2** | 3.5% |
| **Level 3** | 2.5% |
| **Level 4** | 2% |
| **Level 5** | 1% |

**How it works:**
- One referral code for both platforms
- Earn from all your referrals' trading fees
- Commissions split from platform share (no extra fees to users)
- During beta: Auto-credits to virtual balance
- On mainnet: Claimable on-chain earnings

**Example:**
```
You refer Alice (Level 1)
Alice refers Bob (Level 2)
Bob refers Carol (Level 3)

Bob trades $1,000 → generates $30 in fees

Your earnings:
- From Alice's trades: 25% of fees
- From Bob's trades: 3.5% of fees
- From Carol's trades: 2.5% of fees
```

---

## Why This Innovation Matters

### No More Centralized Oracles

**Traditional platforms:**
- Polymarket → UMA resolvers (centralized committee)
- Kalshi → CFTC regulated (US only, curated markets)
- Augur → Failed (oracle problem unsolved)

**SOLPREDS solves this:**
- ✅ Permissionless validation
- ✅ Economic penalties for dishonest proposals (stake loss)
- ✅ Community voting as backup
- ✅ No single point of failure

---

### Permissionless Market Creation

**Traditional platforms:**
- Weeks-long review process
- High curation barriers
- Limited market types

**SOLPREDS:**
- ✅ Create any market in 30 seconds
- ✅ 0.1 SOL cost
- ✅ Instant approval
- ✅ Binary + Multi-Choice support
- ✅ Earn from your ideas

---

### Multi-Choice Markets

**Unique to SOLPREDS:**
- Multiple custom outcomes per market
- More nuanced predictions
- Higher engagement
- More trading opportunities

**Examples:**
- "Which team wins?" → Multiple teams
- "Price range prediction?" → Multiple ranges
- "Who's next president?" → Multiple candidates

---

## Example Markets

Browse live markets at **[solpreds.fun](https://solpreds.fun)**

!!! example "Active Markets"
    **Binary:**
    - "Will $SOLPREDS presale hit $1M?"
    - "Will BTC hit $100K by March?"
    - "Will SOL flip ETH in DeFi TVL?"
    
    **Multi-Choice:**
    - "Who wins the 2026 World Cup?" (multiple countries)
    - "SOL price Dec 31?" (multiple price ranges)
    - "Which AI company IPOs first?" (multiple companies)

---

## Roadmap

| Milestone | Status |
|-----------|--------|
| ✅ Dynamic Bonding Curve AMM | Complete |
| ✅ Multi-Choice Markets | Complete |
| ✅ Resolution System | Complete |
| ✅ Creator Earnings Dashboard | Complete |
| ✅ 5-Level Referrals | Complete |
| ✅ Virtual Economy Beta | **Live Now** |
| 🟡 $SOLPREDS Presale | In Progress |
| ⏳ Token Integration | After Presale |
| ⏳ Mainnet Launch | Q2 2026 |
| ⏳ On-Chain Program | Q3 2026 |

---

## Get Started

**Test Community Markets with virtual tokens:**

👉 **[solpreds.fun](https://solpreds.fun)**

**Join the $SOLPREDS presale:**

👉 **[presale.solpreds.fun](https://presale.solpreds.fun)**

**Stay Connected:**
- 🐦 [Twitter/X](https://x.com/SOLPREDS_FUN)
- 💬 [Telegram](https://t.me/SOLPREDS)
- 💬 [Discord](https://discord.gg/5EE29Xrc58)

---

[Back to Home](index.md){ .md-button }
[View Prediction Rounds](https://solpreds.fun/solana){ .md-button .md-button--primary }
[Join Presale](https://presale.solpreds.fun){ .md-button .md-button--primary }