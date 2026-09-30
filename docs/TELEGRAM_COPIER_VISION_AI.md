# 🤖 Multimodal Vision AI Telegram Signal Copier & Execution Guardian
**Author & Quantitative Systems Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Subsystem:** Real-Time MTProto Channel Listener, Vision AI Chart Parser & Sub-Second Execution Pipeline  
**Target Platform:** MetaTrader 5 (MT5) with Prop-Firm Drawdown Protection (Funding Pips Certified)

---

## ⚡ 1. Overview & Problem Solved

Many elite trading channels (such as Keshav Trades) communicate trading ideas through **TradingView chart screenshots** accompanied by terse text updates (e.g. `"#GOLD With Min. Qty"`, `"SL CTC"`, `"Closed at Breakeven"`, `"Target 1 Smashed 🎯"`).

Retail traders attempting manual copying suffer from:
1. **Severe Execution Delay (5–30 seconds):** Missing fast-moving entries on Gold or Crypto impulses.
2. **Human Interpretation Errors:** Misreading entry price, stop-loss, or invalid risk-to-reward ratios.
3. **Missed Management Updates:** Failing to move Stop Loss to Breakeven ("SL CTC") in real-time, resulting in winning trades turning into losers.

**The Solution:** An autonomous, event-driven signal ingestion and execution daemon combining **MTProto channel streaming**, **Multimodal Vision AI / OCR**, **Deterministic Hashtag Routing**, and **Sub-Second MT5 Order Dispatch**.

---

## 🏛️ 2. Architectural Pipeline

```
  ┌──────────────────────────────────────────────┐
  │       Telegram Private Channel Feed          │
  │     (TradingView Screenshots + Captions)     │
  └──────────────────────┬───────────────────────┘
                         │
        [Telethon MTProto Channel Listener]
                         │
                         ▼
  ┌──────────────────────────────────────────────┐
  │        Multimodal Vision AI Brain            │
  │  1. Deterministic Hashtag Extraction         │
  │     (#GOLD -> XAUUSD, #BTC -> BTCUSD)        │
  │  2. Multimodal OCR & Visual Structure:       │
  │     - Entry Zone, Stop-Loss, Target levels   │
  │  3. Follow-Up Update Classifier:             │
  │     - "SL CTC" -> Move SL to Breakeven       │
  │     - "Target Smashed" -> Partial Profit Lock│
  │     - "Closed at BE" -> Emergency Flatten    │
  └──────────────────────┬───────────────────────┘
                         │
                         ▼
  ┌──────────────────────────────────────────────┐
  │      Funding Pips Prop-Firm Risk Guard       │
  │  • Enforce 0.01 micro-lots / min-qty rule    │
  │  • Spread filter & maximum slippage ceiling  │
  │  • Daily loss barrier & equity floor checks  │
  └──────────────────────┬───────────────────────┘
                         │
                         ▼
  ┌──────────────────────────────────────────────┐
  │         Sub-Second MT5 Order Executor        │
  │  • Named-Pipe IPC / MT5 API Order Dispatch   │
  │  • Immediate SL & TP Server-Side Injection   │
  │  • Telegram Mobile Dispatcher Confirmation   │
  └──────────────────────────────────────────────┘
```

---

## 🧠 3. Key Components & Implementation

### 1. MTProto Real-Time Channel Listener (`channel_listener.py`)
- Uses Telethon's persistent MTProto protocol for sub-50ms message capture.
- Listens asynchronously to incoming photo media and text captions across private and public channels.

### 2. Multimodal Vision Brain (`vision_brain.py`)
- **Deterministic Tokenizer:** Extracts broker-recognized symbols directly from hashtags (e.g. `#GOLD` $\rightarrow$ `XAUUSD`, `#BTC` $\rightarrow$ `BTCUSD`, `#US30` $\rightarrow$ `US30`).
- **Vision Chart Extraction:** Analyzes TradingView charts to extract exact price coordinates, entry ranges, and invalidation points.
- **Sizing Detection:** Automatically parses phrases like `"With Min. Qty"`, `"MINI SL"`, or `"0.01 lot only"`, locking volume to safe micro-lots.
- **Lifecycle Action Router:** Distinguishes between new signal proposals and in-trade lifecycle updates:
  - `"SL CTC"` / `"Cost to Cost"` $\rightarrow$ Triggers `MOVE_BE` (moves SL to open price + commission buffer).
  - `"Target 1 Done"` $\rightarrow$ Triggers `PARTIAL_CLOSE` (locks 50% volume).
  - `"Closed at Breakeven"` $\rightarrow$ Triggers `FULL_CLOSE` (flattens remaining volume).

### 3. Prop-Firm Risk Guardian (`funding_pips_guard.py`)
- Enforces strict compliance with Funding Pips evaluation metrics:
  - Restricts maximum lot allocation.
  - Blocks entries during volatile spread blowouts ($> 35\text{ pips}$).
  - Verifies daily loss buffer before sending any order to MT5.

### 4. High-Speed MT5 Order Execution (`order_executor.py` & `position_manager.py`)
- Directly dispatches market orders via MT5 with sub-second latency.
- Manages ticket tracking and bi-directional position state in SQLite.
- Sends instant Telegram push notifications confirming execution to the trader's personal phone.

---

## 📊 4. Automated Test Suite

A comprehensive test suite ([`tests/test_keshav_copier.py`](../tests/test_keshav_copier.py)) guarantees deterministic parsing:
- `test_symbol_extraction_from_hashtags()`: Verifies zero AI hallucination on symbol tags.
- `test_min_qty_detection()`: Validates detection of micro-lot flags across prompt variations.
- `test_update_post_detection_and_action()`: Ensures follow-up charts are routed as position modifications rather than duplicate orders.
- `test_text_update_parsing()`: Validates immediate SL-to-breakeven routing on `"SL CTC"`.
