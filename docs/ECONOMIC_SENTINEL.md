# 🛡️ AlgoArena Economic News Sentinel & Macro Risk Shield
**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Module:** `algoarena/risk/economic_calendar.py`  
**Feed Source:** FairEconomy / ForexFactory Institutional Economic Calendar Feed  

---

## ⚡ Overview & The News Problem

During High-Impact macroeconomic releases (such as **US Non-Farm Payrolls [NFP]**, **Consumer Price Index [CPI]**, and **FOMC Interest Rate Decisions**), broker spreads on Gold and Forex pairs regularly blow out from normal levels ($0.20 - $0.35) to catastrophic levels (**$2.00 - $6.00+**), accompanied by violent 50-pip slippage gaps.

Conventional retail Expert Advisors (EAs) blindly hold trades into news, causing instant prop-firm challenge blowouts.

The **AlgoArena Economic News Sentinel** solves this completely by ingesting zero-cost live calendar data and applying a **Two-Stage Dual-Strategy Protocol**:

```
                              [ HIGH-IMPACT RED-FOLDER EVENT TIMELINE ]
                                                 │
          T - 15 Mins                   T - 5 Mins               T = 0              T + 15 Mins to + 45 Mins
               │                             │                     │                           │
               ▼                             ▼                     ▼                           ▼
      [ STRATEGY A1: QUARANTINE ]    [ STRATEGY A2: FLATTEN ]   [ NEWS RELEASE ]    [ STRATEGY B: CONTINUATION ]
      - Zero new entries allowed     - Auto-close all open      - 50-pip spread     - Stabilization confirmed
      - Stand down all bots          - Bank all profits         - Zero open risk    - Ride dominant institutional
      - Mute signal triggers         - 100% Cash / Margin Safe  - Account safe      - trend with clean stops
```

---

## 🏛️ Strategy A: The Pre-News Capital Shield

1. **Phase 1: Entry Quarantine ($T - 15\text{ minutes}$)**:
   - Exactly 15 minutes before any High-Impact release impacting the traded asset (e.g., USD events for `XAUUSD`, `EURUSD`, `GBPUSD`), the engine blocks all new order creation.
   - AlgoArena Risk Guard logs:
     ```text
     🛡️ [ECONOMIC SENTINEL] Trade quarantined on XAUUSD: High-impact USD event 'CPI m/m' at 12:30 UTC. Standing down.
     ```

2. **Phase 2: Emergency Auto-Flatten ($T - 5\text{ minutes}$)**:
   - Exactly 5 minutes before the event fires, the position manager sweeps all open tickets for the affected symbol, closes them at market price, and banks floating profits into balance.
   - Accounts enter news releases in **100% cash with zero floating exposure**, completely immune to spread spikes and slippage.

---

## 🚀 Strategy B: Post-News Institutional Continuation

1. **Phase 3: The Stabilization Window ($T + 0\text{ to } T + 15\text{ minutes}$)**:
   - The initial 15 minutes post-release feature wild whipsaws and algorithmic stop-hunts. The Sentinel maintains the quarantine during this window.
2. **Phase 4: Directional Trend Riding ($T + 15\text{ to } T + 45\text{ minutes}$)**:
   - Once spread normalizes ($\le \$0.35$ on Gold) and the market confirms institutional order flow direction, the engine evaluates trend continuation signals with normal tight stops.

---

## 📱 Telegram Integration (`/news`)

Traders can inspect upcoming high-impact events directly from Telegram:

```text
/news
```

**Bot Response:**
```text
📅 ALGOARENA ECONOMIC CALENDAR SENTINEL
━━━━━━━━━━━━━━━━━━━━
🔴 [USD] Non-Farm Employment Change
   ⏰ 18:00 IST (12:30 UTC) | Impact: High
   ⚡ Status: Stand Down 15m Before (Quarantine Active)

🔴 [USD] Unemployment Rate
   ⏰ 18:00 IST (12:30 UTC) | Impact: High
   ⚡ Status: Auto-Flatten Open Trades at 17:55 IST

🔴 [USD] FOMC Statement & Rate Decision
   ⏰ 23:30 IST (18:00 UTC) | Impact: High
   ⚡ Status: 100% Cash Protection Enforced
━━━━━━━━━━━━━━━━━━━━
🛡️ Protected by Mehak Sandhu (@mehaksandhudev) Risk Shield
```

---

[⬅️ Back to Main README](../README.md)
