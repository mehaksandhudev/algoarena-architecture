# 📱 AlgoArena Telegram Mobile Commander & Alert Dispatcher
**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Telegram Bots:**
- **VM 1 (Account #40000306475):** `@algoarena711bot`
- **VM 2 (Account #40000306477):** `@algomehak0bot`
**Module:** `algoarena/telegram/bot.py`  

---

## ⚡ 24/7 Mobile Command & Telemetry

AlgoArena features an integrated, asynchronous Telegram Bot providing bi-directional mobile control and real-time push alert telemetry. Traders can monitor positions, inspect AI deliberative reasoning, track macroeconomic news, and trigger emergency actions directly from an iPhone or Android phone anywhere in the world.

Each VM in the distributed cluster runs an isolated, dedicated Telegram commander bot mapped directly to its respective Funding Pips challenge account.

---

## 📋 Full Command Directory

| Command | Category | Description & Output |
| :--- | :--- | :--- |
| **`/start`** or **`/help`** | **Navigation** | Displays institutional welcome banner with creator credits to **Mehak Sandhu (`@mehaksandhudev`)** and quick-access command list. |
| **`/scalper`** | **Telemetry** | **Live M1 Gold Radar:** Returns live Bid/Ask quotes, current spread, 9/21 Dual-EMA flow state, and real-time LightGBM probabilities ($P(\text{BUY}), P(\text{SELL}), P(\text{HOLD})$). |
| **`/macro`** | **AI Oracle** | **Cross-Asset Macro Intelligence:** Synthetic DXY velocity (15m/1h momentum), session regime state, economic event countdowns, and directional USD bias. |
| **`/news`** | **Risk Sentinel** | **Economic Calendar Radar:** Upcoming High-Impact (Red Folder) NFP/CPI events, dual IST (+05:30) and UTC release times, and active quarantine countdowns. |
| **`/dashboard`** | **Web Access** | **Instant Remote Links:** Returns both the Cloudflare encrypted HTTPS public link (`https://*.trycloudflare.com`) and direct VPS IP link for phone browser access. |
| **`/history [today\|yesterday]`** | **Analytics** | **Paired Deal Durations & P&L:** Groups MT5 `IN` and `OUT` deals, calculating exact duration held (e.g. `1m 24s`, `2m 10s`), win rates, and daily net P&L. |
| **`/positions`** | **Execution** | **Open Trades Matrix:** Lists all active orders across symbols, lot sizes, entry prices, Stop-Loss, Take-Profit, and real-time floating dollar P&L. |
| **`/balance`** | **Financials** | Live MT5 account equity, balance, margin used, free margin, and drawdown utilization. |
| **`/status`** | **System Health** | Active strategies count, session status (London/NY active vs Asian/Rollover quarantine), static $250 daily barrier, $4,500 static floor, and +$100 daily profit lock. |
| **`/debates`** | **AI Stream** | **Live Multi-Agent Consensus Stream:** Real-time stream of signal approvals, trend vetoes, profit locks, and AlgoArena Risk Guard decisions. |
| **`/closeall`** | **Emergency** | **1-Tap Emergency Flatten:** Instantly closes all open positions on MetaTrader 5 and reconciles SQLite database state. |
| **`/best`** | **Leaderboard** | Out-of-Sample rankings of active strategies sorted by Net Profit, Win Rate, and Profit Factor. |
| **`/ping`** | **Heartbeat** | Latency and heartbeat check verifying bot uptime and polling responsiveness. |

---

## 🔔 Automated Push Notification Daemon

In addition to answering queries, the bot runs a background watchdog thread (`_notification_loop`) that pushes immediate alerts to your phone:

1. **Trade Execution Alerts:** Notifies the exact second a scalp opens, including Ticket, Symbol, Sizing, Entry, SL, and TP.
2. **Profit Banking Alerts:** Pings the moment the Green Profit Ratchet moves SL to breakeven or exits into green profit (+$$$).
3. **Rollover Flatten Alerts:** Pings at 20:45 UTC confirming all open positions have been cleanly force-flattened before broker rollover.
4. **Pre-News Alerts:** Warns 15 minutes before High-Impact events and confirms 100% cash auto-flattening.
5. **Circuit Breaker Alerts:** Alerts if daily drawdown approaches the **$250.00 static limit**, equity approaches the **$4,500.00 static floor**, or the **+$100.00 daily profit banker** locks in profits.

---

[⬅️ Back to Main README](../README.md)

