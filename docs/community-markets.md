# Community Prediction Markets

**Create markets. Trade outcomes. Earn from volume.**

!!!  "Status: BETA"
    Community Markets is currently in testing phase. Coming soon to mainnet!

---

## The Innovation

SOLPREDS Community Markets combines the best of **Pump.fun** and **Polymarket** to solve the memecoin problem.

### Comparison Table

| Feature | Pump.fun | Polymarket | Kalshi | **SOLPREDS Markets** |
|---------|----------|------------|--------|---------------------|
| **Asset Type** | Memecoins | Predictions | Predictions | Predictions |
| **Price Driver** | Hype/Narrative | Real Events | Real Events | ✅ Real Events |
| **Creator Earnings** | >0.8% of volume | None | None | ✅ 1% volume |
| **Bonding Curve** | ✅ Yes | ❌ Order Book | ❌ Order Book | ✅ Yes |
| **Permissionless** | ✅ Yes | ❌ Curated | ❌ Regulated | ✅ Yes |
| **Dev Risk** | High (rugpulls) | N/A | N/A | ✅ Low |

---

## The Memecoin Problem - SOLVED

### Traditional Memecoins (Pump.fun)

❌ Price depends on team/narrative/hype  
❌ Developers blamed for price crashes  
❌ Community anger when tokens dump  
❌ No sustainable value for creators  
❌ High risk of rugpulls  

### SOLPREDS Community Markets

✅ Outcome depends on **REAL WORLD EVENTS**  
✅ Creators earn from **volume, not price**  
✅ No blowback - market resolves based on **facts**  
✅ Sustainable creator revenue (1% trades + 2% winnings)  
✅ Price reflects **probability, not hype**  

!!! example "Example Comparison"
    **Pump.fun:** "price faces infinite volatility, many tokens inevitably pump & dump"  
    → Price based on belief in team/community
    
    **SOLPREDS:** "Will BTC hit $100K by March?"  
    → Outcome is objective, verifiable, real

---

## How It Works

### 1. Market Creation (Permissionless)

Anyone can create a YES/NO prediction market:

- **Cost:** 0.1 SOL
- **Creator earns:** 1% of all trading volume + 2% of winner payouts
- **No liquidity needed:** Bonding curve handles everything
```
Example: "Will Elon buy Microsoft by 2026?"
Creator spends: 0.1 SOL
Market attracts: 1,000 SOL in trading volume
Creator earns: 10 SOL (1% of volume) + 2% of final payouts
```

---

### 2. Bonding Curve Trading

Markets use a **constant product AMM** (like Uniswap):

$$k = yes_{liquidity} \times no_{liquidity} = 1,000,000$$

**How prices work:**

- Initial state: YES = 50%, NO = 50%
- Someone buys YES → YES price increases, NO decreases
- Always: YES% + NO% = 100%

!!! info "Live Price Discovery"
    Prices automatically adjust based on real demand. No order books, no matching - instant execution!

**Visual Example:**
```
Market: "Will BTC hit $100K by March 1?"

Initial:  YES: 50%  |  NO: 50%

Alice buys 100 SOL of YES
→ YES: 59%  |  NO: 41%

Bob buys 50 SOL of NO
→ YES: 53%  |  NO: 47%

Market reflects real-time probability!
```

---

### 3. Resolution System

Markets resolve through **decentralized voting**:

### 3. Resolution System

Markets resolve through a **multi-phase process** with dispute protection:

#### Phase 1: Proposal (6 hours)
- Anyone stakes $200 in $SOLPREDS tokens and proposes outcome (YES or NO)
- 6-hour dispute window begins
- If correct and no dispute, proposer earns 0.5% of pot

#### Phase 2: Dispute Window (6 hours)
**Two possible paths:**

**Path A - No Dispute (Most Common):**
- No one challenges the proposal
- After 6 hours, market automatically finalizes with proposed outcome
- Winners can claim payouts
- Proposer gets stake back + 0.5% reward from winning amount

**Path B - Disputed:**
- Someone disagrees and matches the $200 stake
- Proposes opposite outcome (e.g., if original said YES, dispute says NO)
- Triggers community voting period from SOLPREDS holders

#### Phase 3: Community Vote (24 hours)
**Only happens if disputed:**
- All $SOLPREDS token holders can vote
- Votes weighted by token balance (1 token = 1 vote)
- 5% quorum required (5% of total supply must vote)
- Winning side determined by majority

**Vote Resolution:**
- Winning voters share 25% of losing staker's $200 stake
- Winning staker gets their $200 back + 50% of loser's stake ($100)
- 25% of loser's stake burned

#### Phase 4: Settlement
- Market resolves based on proposal (if undisputed) or vote result (if disputed)
- Winners claim payouts (minus 5% fee)
- Losers get $0
- Fees split: Creator (2%) + Platform (2.5%) + validator (0.5%)

---

**Example Flow:**

**Scenario 1 - Undisputed (90% of cases):**
---

## Fee Structure

### Trading Fees (3% total)
- **1%** → Market creator
- **2%** → Platform

### Winning Fees (5% total)
- **2%** → Market creator
- **2.5%** → Platform
- **0.5%** → Validator

!!! success "Creator Benefits"
    Creators earn passively from every trade AND from final settlements. The more popular your market, the more you earn! Validaors earn from solving markets.

---

## Why This Matters

### For Creators

✅ **Sustainable income** from volume  
✅ **No price risk** - earn regardless of outcome  
✅ **No community blowback** - not your fault if wrong side wins  
✅ **Permissionless** - create any market instantly  

### For Traders

✅ **Fair prices** - bonding curve, not market makers  
✅ **Instant execution** - no waiting for orders  
✅ **Real outcomes** - not dependent on dev actions  
✅ **Transparent resolution** - community-driven  

### For the Ecosystem

✅ **Solves rugpull problem** - no dev control over outcomes  
✅ **Aligns incentives** - creators want high volume, not price manipulation  
✅ **Brings legitimacy** - outcomes based on real events  
✅ **Scalable** - anyone can create markets on anything  

---

## Example Markets

!!! example "Popular Market Ideas"
    - "Will BTC hit $100K in Q1 2026?"
    - "Will Trump announce presidential run by March?"
    - "Will Nvidia reach $1T market cap this year?"
    - "Will SOL flip ETH in TVL by 2026?"
    - "Will GPT-5 launch in 2026?"

---

## Launch Status

| Milestone | Status |
|-----------|--------|
| Smart Contract Development | 🟡 In Progress |
| Bonding Curve Testing | ✅ Complete |
| Resolution System | ✅ Complete |
| UI/UX Design | 🟡 In Progress |
| Security Audit | ⏳ Pending |
| Mainnet Launch | ⏳ Q2 2026 |

---

## Get Involved

!!! info "Join the Beta"
    Want to test Community Markets before launch?
    
    - Join our [Telegram](https://t.me/SOLPREDS)
    - Follow updates on [X](https://x.com/SOLPREDS_FUN)
    - Check [dev environment](https://markets.solpreds.fun/dev)

---

[Back to Home](index.md){ .md-button }
[View Main Platform](https://solpreds.fun){ .md-button .md-button--primary }