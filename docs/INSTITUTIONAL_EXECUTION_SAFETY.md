# AlgoArena — Institutional Execution Safety & Position Protection
**Author:** Mehak Sandhu (`@mehaksandhudev`)  
**Subsystem:** Core Engine & Risk Guard Execution Safety Architecture  
**Status:** PRODUCTION OPERATIONAL  

---

## 1. Executive Summary & Root Cause Resolution

Following forensic analysis of historical trades where orphaned trades with Magic Number `0` were held through adverse reversals, three critical execution safeguards were designed and integrated into AlgoArena:

1. **Bi-Directional Position Reconciliation & Orphan Adoption:**  
   Prevents invisible trades. Any open position detected on the MetaTrader 5 terminal—regardless of whether it was placed with Magic 0, placed manually, or orphaned after an unexpected VPS reboot—is immediately recognized, adopted into the database ledger, and monitored on every tick.
2. **Universal Emergency Profit Sentinel:**  
   Protects massive floating winners from ever turning into losses. If any position reaches a significant floating gain (e.g. +$20 to +$300+ like the $42 Gold plunge on Sept 15), the sentinel locks in profit and auto-cashes out if price pulls back 20% from peak gain.
3. **Hard Dynamic Lot Size Ceiling:**  
   Prevents margin blowouts by mathematically restricting single-order volume based on account equity (strictly 0.01 micro-lots for accounts < $500).

---

## 2. Technical Architecture & Safeguards

```
                      ┌────────────────────────────────────────┐
                      │        MetaTrader 5 Terminal           │
                      │      (Broker Server Live Feed)         │
                      └──────────────────┬─────────────────────┘
                                         │
                         [mt5.positions_get() every 200ms]
                                         │
                                         ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │                 AlgoArena Engine Reconciliation Loop                       │
  ├────────────────────────────────────────────────────────────────────────────┤
  │ 1. MT5 Positions vs. SQLite Ledger Check:                                  │
  │    • Is an internal trade closed on MT5?  -> Reconcile deal & reflect.     │
  │    • Is an MT5 trade missing from SQLite? -> ADOPT IMMEDIATELY!            │
  │                                                                            │
  │ 2. Emergency Broker-Side SL/TP Injection:                                  │
  │    • Does the adopted position have NO stop-loss?                          │
  │    • Injects hard server-side SL & TP via TRADE_ACTION_SLTP!               │
  │                                                                            │
  │ 3. Universal Profit Sentinel (Anti-Greed Windfall Protection):             │
  │    • Track peak floating profit for every active ticket.                   │
  │    • If floating gain >= $20 and pulls back 20% from peak -> CASH OUT!     │
  │    • If trail trigger reached -> Move SL progressively in green profit!    │
  └────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Account-Tier Sizing Safeguards

| Account Equity Tier | Single Order Lot Cap | Max Total Account Lots | Risk Per $1 Gold Move |
| :--- | :--- | :--- | :--- |
| **Small (< $500)** | **0.01 Micro-Lots** | **0.02 Lots** | **$1.00 - $2.00** |
| **Growth ($500 - $1,500)** | **0.02 Lots** | **0.05 Lots** | **$2.00 - $5.00** |
| **Scale ($1,500 - $3,500)** | **0.05 Lots** | **0.15 Lots** | **$5.00 - $15.00** |
| **Prop-Firm ($5,000+)** | **0.08 Lots (Gold) / 0.05 Lots (BTC)** | **0.40 Lots** | **$8.00 - $16.00** |

---

## 4. EOD Rollover Auto-Flatten (20:45 UTC)

Every broker resets liquidity between 21:00 and 22:00 UTC, causing Gold and FX spreads to blow out dramatically (often $2.50 to $3.50+ on Gold).
- **Automated Flattening:** At **20:45 UTC**, the engine automatically evaluates all active paper positions and force-closes them cleanly at market.
- **Session Quarantine:** Order opening is locked between **20:45 UTC and 06:00 UTC** (London Open). No trades are held into the rollover spread blowout window.

---

## 5. Net USD Directional Correlation Guard

To prevent correlation clustering (e.g. opening Gold BUY and EURUSD BUY simultaneously, resulting in double-dollar exposure):
- Every symbol and direction is categorized into its net dollar exposure:
  - `EURUSD`, `GBPUSD`, `XAUUSD`, `BTCUSD`: BUY = `SHORT_USD`, SELL = `LONG_USD`
  - `USDJPY`, `USDCAD`, `USDCHF`: BUY = `LONG_USD`, SELL = `SHORT_USD`
- **Correlation Veto:** If a position is already open in a specific USD direction, any new candidate order in the same USD direction is automatically vetoed by `RiskGuard`.

---

## 6. Daily Profit Lock ($100 Banker Target) & Static Drawdown Floors

- **Static Daily Loss Barrier ($250.00 / 5.0%):** Enforced statically from the 00:00 server balance. Intraday unrealized equity highs do not trail or restrict the daily loss floor.
- **Static Drawdown Barrier ($4,500.00):** Hard account floor representing the maximum $500 overall loss tolerance for a $5,000 challenge.
- **Daily Profit Banker ($100.00):** Once closed P&L reaches +$100.00 in a single day, the bot locks in the banked gains and stands down until the next morning session, preventing giveback during late-session chop.

Even if a strategy proposes a 4x or 5x multiplier, `RiskGuard.can_open_order()` strictly rejects any order exceeding the tier ceiling.

---

## 7. Post-Fill SL/TP Verification

Certain brokers (e.g. Exness, IC Markets under Market Execution mode) occasionally reject or strip Stop-Loss / Take-Profit parameters if passed directly inside the market order payload.
- `algoarena/paper/orders.py` now resolves the exact position ticket and queries MT5 after fill.
- If `p.sl == 0.0` or `p.tp == 0.0`, it immediately dispatches an asynchronous `TRADE_ACTION_SLTP` modification request to attach hard broker-side protection.
