# 🌐 AlgoArena Dashboard V2, Real-Time Charting & Multi-Strategy Architecture

**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Platform Version:** AlgoArena V2.4  
**Scope:** Web Terminal, Real-Time TradingView Engine, Multi-Strategy Leaderboard, and Microstructure Matrix  

---

## 🎯 Executive Summary

The **AlgoArena V2 Dashboard** is an institutional-grade monitoring and quantitative execution terminal designed to provide crystal-clear visual order-flow monitoring, live machine learning probabilities, and real-time trade verification on MetaTrader 5 accounts.

Recent major updates eliminate visual clutter, expand multi-asset coverage, introduce H4 swing analysis, snap real broker trades directly to candlestick bars, and unify all promoted strategies under one authoritative leaderboard.

```
       ┌────────────────────────────────────────────────────────┐
       │             TradingView Lightweight Charts             │
       │    100% Clean Candlesticks + Dynamic Decimals          │
       │      [M1]  [M5]  [M15]  [H1]  [H4]  [D1]               │
       └──────────────────────────┬─────────────────────────────┘
                                  │
      ┌───────────────────────────┴───────────────────────────┐
      │                                                       │
┌─────▼─────────────────────────┐   ┌─────────────────────────▼─────┐
│ Microstructure Conviction     │   │ Multi-Strategy Performance    │
│            Matrix             │   │          Leaderboard          │
│ • Body Conviction %           │   │ • ml_gold_m1_scalper (M1)     │
│ • Wick Rejection %            │   │ • ml_asset_oracle (BTCUSD)    │
│ • Directional Edge & Probs    │   │ • ml_gold_oracle (M5/M15)     │
│ • Live M5 Candle Countdown    │   │ • ml_euro_oracle (EURUSD)     │
└───────────────────────────────┘   └───────────────────────────────┘
```

---

## 🕯️ 1. 100% Clean TradingView Candlestick Engine

### Elimination of Clutter Overlays
Earlier iterations drew synthetic 2D canvas boxes (FRVP Asian range blue boxes, Judas sweep zones, and Point of Control lines) across the chart. While informative theoretically, in live execution they obstructed candlestick anatomy and created browser repainting lag.

- **Canvas Overlay Removed:** The `<canvas id="tv-vector-overlay">` and legacy drawing loops were completely removed.
- **Pristine Price Action:** The chart now displays pure, unobstructed Japanese candlesticks with institutional EMA order-flow ribbons (9 EMA and 21 EMA).

### Dynamic Multi-Asset Decimal Precision
Lightweight Charts requires explicit `priceFormat.precision` per asset to prevent scientific notation or incorrect trailing zeroes:

| Asset | Ticker | Decimals | Minimum Point |
| :--- | :--- | :--- | :--- |
| **Gold** | `XAUUSD` | **2 decimals** | `$0.01` |
| **Bitcoin** | `BTCUSD` | **2 decimals** | `$0.01` |
| **Japanese Yen** | `USDJPY` | **3 decimals** | `¥0.001` |
| **Euro** | `EURUSD` | **5 decimals** | `$0.00001` |
| **British Pound** | `GBPUSD` | **5 decimals** | `$0.00001` |

---

## ⏱️ 2. Expanded Multi-Timeframe Matrix (Including H4)

Traders can dynamically switch between intraday scalping and macro swing timeframes with a single click:

- **`M1`** — 1-Minute: High-frequency impulse scalping execution.
- **`M5`** — 5-Minute: Primary momentum and microstructure ML radar.
- **`M15`** — 15-Minute: Macro trend guard (`EMA20` vs `EMA50`).
- **`H1`** — 1-Hour: Session structure and liquidity sweep levels.
- **`H4`** — **4-Hour Swing Timeframe (NEW):** Major market regime direction and multi-day structural support/resistance.
- **`D1`** — Daily: Institutional order-flow bias.

---

## 📍 3. Real MT5 Trade Marker Snapping

### The Problem Solved
TradingView Lightweight Charts discards any trade marker whose timestamp does not match an exact bar open timestamp. MetaTrader 5 deals execute at exact seconds (e.g., `19:04:37 UTC`), causing markers to disappear on standard M1 or M5 bar charts (`19:04:00 UTC`).

### The Snapping Solution
`app.js` now maps deal timestamps to the closest matching bar:
```javascript
// Snap execution timestamp to nearest bar open
const targetBarTime = latestBars.reduce((closest, bar) => {
    return Math.abs(bar.time - unixSec) < Math.abs(closest - unixSec) ? bar.time : closest;
}, latestBars[0].time);
```
- **Visual Badges:** 
  - 🟢 **`BUY`** arrow below candle with entry price and lot size.
  - 🔴 **`SELL`** arrow above candle with entry price and lot size.
  - 💰 **`TP / SL`** markers showing exit execution and booked net P&L.
- **Historical Buffer:** Chart bar lookback buffer expanded to **1,000 bars** so earlier session trades remain visible.

---

## ⚡ 4. Real-Time Microstructure Conviction Matrix

The **Microstructure Conviction Matrix** runs continuous 5-minute candle analytics across 5 key assets (`XAUUSD`, `BTCUSD`, `EURUSD`, `USDJPY`, `GBPUSD`):

1. **Body Conviction %:** Ratio of physical candle body to total range.
   $$\text{Body Conviction} = \frac{|\text{Close} - \text{Open}|}{\text{High} - \text{Low}} \times 100\%$$
   High values ($\ge 65\%$) indicate strong directional expansion without hesitation.
2. **Wick Rejection %:** Ratio of the counter-trend wick to total range. Identifies absorption and liquidity traps.
3. **Machine Learning Directional Edge:** Difference between $P(\text{BUY})$ and $P(\text{SELL})$:
   $$\text{Edge} = |P(\text{BUY}) - P(\text{SELL})|$$
4. **Live Candle Countdown Timer:** Displays remaining seconds until the active M5 candle closes (`MM:SS`).
5. **Dynamic Broker Resolution:** Automatically maps standard symbols to broker-specific symbols (e.g. `XAUUSD` on Funding Pips) with instant parquet dataset fallback.

---

## 🏆 5. Unified Multi-Strategy Performance Leaderboard

The Strategy Performance table now features a complete view of all active strategies registered in `paper_promotions`:

1. **`ml_gold_m1_scalper`** — 1-Minute Autonomous Gold Scalper:
   - Dedicated `TwoStageGoldBrain` (Regime Volatility Gate + Directional Sniper).
   - Dynamic profit banking at **+$2.50 to +$3.50+**.
   - $1.50 tight stop loss and +$1.20 breakeven ratchet.
2. **`ml_asset_oracle`** — Multi-Asset Momentum Oracle on Bitcoin (`BTCUSD`).
3. **`ml_gold_oracle`** — M5/M15 Gold Trend Expansion Engine.
4. **`ml_euro_oracle`** — Institutional Euro (`EURUSD`) Order-Flow Model.

Even before a newly promoted strategy executes its first trade of the day, it is displayed in the leaderboard with cumulative performance metrics, win rates, and active status.

---

## 🛡️ 6. AlgoArena Risk Guard Compliance

All strategies and dashboard actions operate under strictly enforced prop-firm boundaries:
- **Daily Loss Circuit Breaker:** **$250.00** hard stop (5% of $5,000 starting challenge equity).
- **Maximum Drawdown Floor:** **$4,500.00** hard equity barrier.
- **Economic News Quarantine:** 15-minute freeze before and after high-impact events.
- **Branding:** Standardized to **AlgoArena Risk Guard** across all logs, UI elements, and Telegram alerts.

---

## 🚀 7. Deployment & Operation

To deploy and run the dashboard:

```bash
# Start Dashboard on Port 8090
python -m algoarena.cli dashboard

# Launch Secure Remote Cloudflare Tunnel
cloudflared tunnel --url http://127.0.0.1:8090
```

Access locally via `http://localhost:8090` or globally via the encrypted Cloudflare tunnel URL delivered by typing `/dashboard` in the Telegram bot.
