# ⚡ AlgoArena Setup & Operations Manual

**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Target Environments:** Google Cloud Linux (GCP Compute Engine Ubuntu 22.04/24.04 LTS), Windows Server VPS, and Local Workstations  
**Broker Accounts:** Funding Pips Evaluation & Instant Accounts (`FundingPips-Trial`) / MT5 64-bit  

---

## ⚡ 1-Click Master Quick Start

AlgoArena is fully cross-platform and provides automated zero-config launchers for both Windows and Linux:

### 🪟 Windows (Local PC or Azure/Windows VPS)
1. Launch **MetaTrader 5**, log into your Funding Pips account (`#40000306475`), and ensure **"Algo Trading"** is enabled (green icon).
2. Double-click **`start_all.bat`**.

### 🐧 Linux (Google Cloud Platform / Ubuntu 22.04+ LTS)
1. Connect via SSH to your Google Cloud Linux instance:
   ```bash
   cd ~/algoarena
   chmod +x setup_gcp_linux.sh start_all.sh
   ./start_all.sh
   ```

### What the Master Launcher Automatically Executes:
- **Environment Detection**: Detects Python 3.10/3.11 64-bit.
- **Auto-Virtualenv**: Creates `.venv` automatically if missing.
- **Dependency Verification**: Verifies `MetaTrader5` (or `mt5linux` on Linux), `LightGBM`, `Scikit-Learn`, `FastAPI`, `Uvicorn`, `Requests`, and `Pandas`.
- **Strategy Auto-Promotion**: Runs `promote_all.py` to register all strategies in SQLite:
  - `ml_gold_m1_scalper` on `M1` (Dedicated Two-Stage AI Brain)
  - `ml_gold_oracle` on `M5` & `M15` (Momentum Scalper & Macro Expansion Runner)
  - Multi-asset oracles (`BTCUSD`, `EURUSD`, `USDJPY`, `GBPUSD`)
- **Service Supervision**: Launches all 4 core services with health monitoring:
  1. **Trading Engine**: Live autonomous execution with News Sentinel & AlgoArena Risk Guard.
  2. **Web Dashboard**: Dark-themed TradingView telemetry terminal on Port 8090.
  3. **Telegram Commander**: Push notification alerts & mobile command center (`@algoarena711bot`).
  4. **Cloudflare Public Tunnel**: Encrypted HTTPS remote access (`https://*.trycloudflare.com`).

---

## 📱 Telegram Mobile Command Center (`@algoarena711bot`)

Control and inspect your trading bot 24/7 directly from your smartphone anywhere in the world:

### Configuration (`.env`):
```env
TELEGRAM_BOT_TOKEN=your_bot_token_here
TELEGRAM_CHAT_ID=your_chat_id_here
```

### Full Command Directory:
- **`/start`** or **`/help`** — Displays the institutional welcome banner with creator credits and quick navigation.
- **`/scalper`** — Real-time M1 Gold Scalper radar: live quotes, spread, 9/21 Dual-EMA ribbon state, and LightGBM model probabilities.
- **`/macro`** — Cross-Asset Macro Intelligence: Synthetic DXY 15m/1h velocity momentum, market regime state, and USD bias.
- **`/news`** — Economic Calendar Sentinel: Upcoming high-impact (Red Folder) USD catalysts, dual IST (+05:30) and UTC release times, and quarantine countdowns.
- **`/dashboard`** — Instant Remote Access: Returns the live encrypted Cloudflare HTTPS URL and direct VPS link.
- **`/history [today|yesterday]`** — Closed deal analytics: paired trade durations (e.g. `1m 24s`), win rates, and daily net P&L.
- **`/positions`** — Active open positions: symbols, lots, entry prices, Stop-Loss, Take-Profit, and floating dollar P&L.
- **`/balance`** — Real-time equity, balance, used margin, free margin, and drawdown utilization.
- **`/status`** — System health, session window status (London/NY active vs Asian quarantine), and risk guard metrics.
- **`/debates`** — Live Multi-Agent Consensus Stream: Real-time trade approvals, trend vetoes, profit locks, and Risk Guard decisions.
- **`/closeall`** — Emergency 1-tap flatten button to immediately close all positions on MetaTrader 5 and reconcile SQLite state.
- **`/best`** — Performance rankings across all active strategies.
- **`/ping`** — Bot response latency and watchdog heartbeat check.

---

## 🐧 Google Cloud Linux (GCP) Deployment Guide

Deploying AlgoArena on Google Cloud Compute Engine (Ubuntu Linux) provides maximum reliability, zero Windows license fees, and fast execution:

### 1. Requirements:
- GCP Compute Engine instance (e.g., `e2-medium` or `e2-micro`, Ubuntu 22.04 LTS).
- Inbound firewall rules allowing Port `8090` (Dashboard).
- Pre-installed Wine & Xvfb (automated by setup script).

### 2. Automated 1-Command Installation:
```bash
git clone https://github.com/mehaksandhudev/algoarena.git
cd algoarena
chmod +x setup_gcp_linux.sh
./setup_gcp_linux.sh
```

### 3. Service Control on Linux:
```bash
# Start all services in background
./start_all.sh

# Check running processes
ps aux | grep algoarena

# Inspect dashboard logs
tail -f logs/dashboard.log

# Stop all services cleanly
./stop_all.sh
```

---

## 🌐 Remote Dashboard Access from Anywhere

You have two secure methods to view the live dashboard on any smartphone or browser:

### Method A: Cloudflare Encrypted HTTPS Tunnel (Recommended)
1. The tunnel launches automatically with `./start_all.sh` or `start_all.bat`.
2. Retrieve your live URL anytime by sending **`/dashboard`** to your Telegram bot.
3. Opens immediately on any phone browser with zero firewall configuration!

### Method B: Direct VPS IP (`http://34.63.131.236:8090`)
Ensure Port `8090` is allowed in your Google Cloud VPC firewall or Windows Defender Firewall.

---

## 🛡️ Prop-Firm Risk Guard Safeguards (Funding Pips Certified)

- **$250.00 Static Daily Loss Barrier**: Strictly enforced 5% daily loss limit on $5,000 capital. Trading halts immediately if hit.
- **$4,500.00 Hard Drawdown Floor**: Hard equity floor protecting the maximum $500 total loss buffer.
- **+$100.00 Daily Profit Banker**: Automatically banks gains and stands down until the next day once +$100 is achieved.
- **Economic Calendar Shield**: Automatic 15-minute quarantine before and after high-impact USD events.
- **Rollover Auto-Flatten (20:45 UTC)**: Closes all positions before broker rollover to eliminate spread blowout risks.
- **Single Position Lock**: Enforces strictly 1 position per symbol, preventing dangerous martingale or grid stacking.
