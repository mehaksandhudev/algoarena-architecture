# AlgoArena — Architecture Notes & Deviations

This document logs design decisions and minor deviations made during the implementation of AlgoArena, adhering to the principle: **choose the CONSERVATIVE/REALISTIC option for anything affecting money, and the SIMPLEST option for everything else.**

---

## 1. Money-Affecting & Quantitative Choices

### 1.1 Pessimistic Intrabar Rule (§2.2)
- If both Stop-Loss and Take-Profit price levels are touched within the High/Low span of a single historical bar, the engine always resolves the Stop-Loss first.
- In the event of a price gap through a Stop-Loss (e.g., weekend or economic news release), the exit price is filled at the bar's `open` price (the worse execution price) rather than the theoretical SL price.

### 1.2 Multi-Asset Tick Value & Point Sizing (§9)
- Institutional sizing accurately maps points to account currency for both 5-digit currency pairs (e.g., EURUSD where 1 point = $1.00 per lot) and commodities/indices (e.g., XAUUSD where 1 point = $1.00 per lot).
- Volume calculations always round **DOWN** (`math.floor`) to the broker's `volume_step` (never up) to prevent risk overrun.

### 1.3 Out-of-Sample Leaderboard Ranking (§10)
- Combinations with fewer than 30 Out-of-Sample (OOS) closed trades are assigned an `insufficient-data` flag and excluded from ranking.
- Overfit detection penalizes any combination whose OOS Profit Factor falls below 60% of its In-Sample Profit Factor with a 30% reduction in its composite score and the `overfit-suspect` badge.

### 1.4 M1 Scalper Position Sizing & Micro-Lot Hard Lock
- For high-frequency M1 scalping (`ml_gold_m1_scalper`), standard dynamic equity-based position sizing is overridden with a hard cap of **0.01 micro-lots**.
- Single-trade risk is held to strictly **$1.00 – $2.50** (0.05% - 0.12% of a $2,000 account), completely insulating the account from catastrophic drawdown while still banking high-frequency +$0.80 to +$2.50+ profit expansions per candle.

### 1.5 Multi-Timeframe Alignment & Confluence Guard
- The M1 Scalper does not trade solely on single-candle signals. It integrates an **M15 Macro Trend Guard** (`EMA20` vs `EMA50`).
- If the higher timeframe is bullish, counter-trend short entries are strictly vetoed (refusing to scalp short into rising macro support).
- Pullback proximity requires entry within $1.80 of the 9 EMA, preventing chasing at over-extended tops/bottoms.

---

## 2. Infrastructure & Systems Architecture

### 2.1 Multiprocessing on Windows Spawn (§2.6)
- Windows multiprocessing defaults to `spawn`. The parallel runner uses `multiprocessing.get_context("spawn")` and top-level module functions (`_execute_single_combo_worker`) so that worker processes do not attempt to re-import unpickleable state.

### 2.2 Dry-Run Execution Simulator (§12)
- An internal simulated broker execution engine (`ExecutionSimulator`) is provided alongside live MT5 demo trading. When `--dry-run` is specified, full position tracking, cost accounting (spread, commission, slippage), and SQLite persistence are exercised without requiring active broker credentials.

### 2.3 Standalone Web Dashboard (§13)
- The FastAPI frontend is completely self-contained in `algoarena/server/static/`. Chart.js (v4.4.2) is vendored locally in `algoarena/server/static/vendor/chart.js` with zero Node.js / npm dependencies, allowing immediate zero-build execution on any Windows machine.

### 2.4 Dedicated M1 Machine Learning Brain Architecture
- A specialized LightGBM model (`gold_m1_scalper_lgbm.pkl`) is trained specifically on 25,000 real M1 Gold bars, independent of the M15 macro oracle (`gold_lgbm_oracle.pkl`).
- Slices 38 custom microstructure features (candle anatomy, wick rejection ratios, momentum velocity, EMA ribbon dispersion) with sub-10ms inference time per bar.

### 2.5 Cloudflare Quick Tunnel Dynamic Foreground Discovery & Persistence
- To guarantee zero-configuration access from mobile devices anywhere in the world without requiring Azure NSG / firewall modifications:
- A recursive binary finder locates `cloudflared*.exe` across user `Downloads`, `Desktop`, `Program Files`, and system `PATH`.
- The tunnel operates as a persistent single-process foreground worker streaming logs to the console, regex-extracting the active `https://*.trycloudflare.com` URL and writing it dynamically to `data/tunnel_url.txt` for immediate lookup by the Telegram bot.

### 2.6 Standardized Institutional Terminal Branding Architecture
- Every service entry point (`PaperEngine`, `start_dashboard`, `run_telegram_bot`, `run_tunnel_foreground`) executes `print_banner()` from `algoarena/banner.py` on launch.
- Renders the full UTF-8 ASCII AlgoArena crest with creator attribution to **Mehak Sandhu (`@mehaksandhudev`)** and service-specific subtitle across all 4 terminal windows.


