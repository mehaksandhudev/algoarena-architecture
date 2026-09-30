# ⚡ AlgoArena M1 Impulse Scalper & LightGBM Brain
**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Asset Focus:** Gold (`XAUUSD` / `XAUUSDm`)  
**Timeframe:** 1-Minute (M1) Execution with M15 Macro Confluence  

---

## 🎯 Architecture Overview

The **M1 Impulse Scalper** (`custom_strategies/ml_gold_m1_scalper.py`) is an autonomous, high-frequency execution system designed to exploit micro-expansions on Gold during London and New York market sessions. Rather than relying on simple moving average crosses, it applies a **6-Pillar Confluence Engine** powered by a dedicated LightGBM machine learning classifier trained on 25,000 real 1-minute bars alongside real-time synthetic DXY order-flow analytics.

```mermaid
graph TD
    A[New M1 Candle Close] --> B{Macro Trend & DXY Oracle}
    B -- DXY Velocity Counter-Trend Spike --> C[VETO: USD Momentum Conflict]
    B -- M15 Trend Aligned & DXY Neutral/Favorable --> D{Order-Flow Ribbon}
    D -- 9 EMA < 21 EMA --> E[Reject: Ribbon Misaligned]
    D -- 9 EMA >= 21 EMA --> F{Pullback Proximity}
    F -- Price > 9 EMA + $1.80 --> G[Reject: Chasing Premium High]
    F -- Within $1.80 Wholesale Window --> H{Candle Anatomy}
    H -- Counter-Rejection Wick Detected --> I[Reject: Reversal Risk]
    H -- Clean Body Anatomy --> J{LightGBM M1 Brain}
    J -- Conviction < 38% or Edge < 4% --> K[Stand Down / Chop Filter]
    J -- Conviction >= 75% --> L[⚡ EXECUTE 0.08 LOT BUY (High Conviction)]
    J -- Conviction 38% - 74% --> M[⚡ EXECUTE 0.04 LOT BUY (Standard Conviction)]
    L & M --> N[AlgoArena Risk Guard Green Ratchet Exits]
    N --> O[+$0.80 Profit: Move SL to Breakeven + Spread]
    N --> P[+$1.20+ Profit: Walk Dynamic Trailing Stop]
    N --> Q[Bank +$0.80 to +$2.50+ Clean Profit per Point]
```

---

## 🏛️ The 6 Pillars of Confluence (Zero-Guesswork Checklist)

Before any order is dispatched to MetaTrader 5, all 6 independent filters must achieve simultaneous consensus:

### 1. 🌊 M15 Macro Trend Guard & MacroOracle DXY Flow
- **Principle:** Never scalp against higher-timeframe order flow or violent US Dollar counter-surges.
- **Rule:** 
  - If M15 `EMA20 >= EMA50` $\implies$ Macro Trend is **BULLISH**. Counter-trend short scalps are **VETOED**.
  - If M15 `EMA20 < EMA50` $\implies$ Macro Trend is **BEARISH**. Counter-trend long scalps are **VETOED**.
  - **MacroOracle Flow Veto:** If synthetic DXY 15-minute velocity exceeds $\pm 0.15\%$ in the opposite direction of the proposed trade, the scalp is immediately blocked.

### 2. ⚡ Dual-EMA Order-Flow Ribbon (9 EMA & 21 EMA)
- Real-time alignment between short-term momentum (9 EMA) and intermediate order flow (21 EMA).
- Long entries require `EMA9 > EMA21`.
- Short entries require `EMA9 < EMA21`.

### 3. 🎯 Pullback Proximity & Anti-Chasing Wholesale Entry
- **The Retail Trap:** Retail traders see a massive green candle and buy at the very top (chasing), right before it pulls back and stops them out.
- **The Wholesale Rule:** The entry price must be within **$1.80 of the 9 EMA** on Gold.
  $$\text{Distance} = |\text{Close} - \text{EMA9}| \le \$1.80$$
- This guarantees the bot enters on the micro-pullback (wholesale pricing), rather than at the exhaustion point.

### 4. 🕯️ Candle Anatomy & Wick Rejection Filtering
- Inspects the physical microstructure of the triggering bar:
  - Longs: Rejects bars with dominant upper wicks (shooting stars/selling pressure). Requires body dominance $\ge 40\%$ of the total bar range.
  - Shorts: Rejects bars with dominant lower wicks (hammers/buying absorption).

### 5. 🧠 Dedicated LightGBM M1 Brain (`models/gold_m1_scalper_lgbm.pkl`)
- Specialized tree-based model trained on 25,000 M1 bars computing probabilities:
  $$P(\text{BUY}), \quad P(\text{SELL}), \quad P(\text{HOLD/CHOP})$$
- Entry threshold: Conviction $\ge 38\%$, Directional Edge $\ge 4\%$, and $P(\text{HOLD}) \le 38\%$.

### 6. 🛡️ Session & Rollover Quarantine Guard
- **Asian Session Quarantine:** No trades executed between 20:45 UTC and 06:00 UTC.
- **EOD Force-Flatten (20:45 UTC):** All open positions are auto-closed at 20:45 UTC to prevent broker rollover spread spikes ($3.50+).
- **Economic Calendar Shield:** Trades quarantined 15 minutes before and after high-impact USD events (NFP, CPI, FOMC).

---

## 🛡️ AlgoArena AI Green Ratchet Exits (Spread-Safe Profit Banking)

The scalper does not use static, hope-based exits. It employs **AlgoArena Risk Guard In-Trade Profit Locking**:

| Floating Profit State | AI Pilot Action | Outcome |
| :--- | :--- | :--- |
| **$0.00 – $0.79** | Position monitored tick-by-tick | Initial stop-loss ($2.50) absorbs normal noise |
| **+$0.80 reached** | **Green Ratchet Locks Breakeven** | Stop-Loss moves to `Entry + Spread + $0.20` |
| **+$1.20 reached** | **Dynamic Tight Trailing Activates** | Micro-stop trails $0.50 directly behind live price |
| **$\ge 5$ minutes held** | **Anti-Stagnation Exit** | If trade floats +$0.30+ for $> 5$ mins, auto-banks cash |

---

## 📈 Dynamic Conviction Sizing & Risk Specifications

The system dynamically sizes orders based on the LightGBM brain's model confidence:

- **Conviction Sizing Tiers ($5,000 Challenge Account):**
  - **High Conviction ($\ge 75\%$ Confidence):** **0.08 Lots**
  - **Standard Conviction ($38\% - 74\%$ Confidence):** **0.04 Lots**
- **Single Trade Risk:** $\sim \$8.00 - \$16.00$ (0.16% - 0.32% of account).
- **Static Daily Loss Barrier:** **$250.00** (5.0% of $5,000 starting capital; never trailed intraday).
- **Static Drawdown Barrier:** **$4,500.00** hard floor ($500 maximum overall loss buffer).
- **Daily Profit Banker:** **+$100.00** (Locks daily gains and halts until next session).
- **Average Holding Time:** 45 seconds to 4 minutes.

---

[⬅️ Back to Main README](../README.md)
