# AlgoArena — Supabase Cloud Database Synchronization System
**Author:** Mehak Sandhu (`@mehaksandhudev`)  
**Project:** AlgoArena Cloud Sync Engine  
**Cloud Database:** Supabase Cloud PostgreSQL (`algoarena-cloud`)  
**Region:** `ap-south-1` (Mumbai)  

---

## 1. Overview & Architecture

The Supabase Cloud Sync System establishes a bidirectional bridge between your **Azure VPS** (where the 24/7 autonomous bot runs live) and your **Local Workstation** (where you monitor, research, and develop).

```
 ┌────────────────────────┐                   ┌────────────────────────┐
 │       Azure VPS        │                   │   Local Workstation    │
 │ (Autonomous Exness MT5)│                   │  (Research / Monitor)  │
 └───────────┬────────────┘                   └───────────┬────────────┘
             │                                            │
             │ [sync_supabase.bat / Telegram /sync]       │ [sync_supabase.bat / Web UI]
             ▼                                            ▼
   ┌─────────────────────────────────────────────────────────────┐
   │             Supabase Cloud PostgreSQL Database               │
   │               (Project: algoarena-cloud)                     │
   │                                                             │
   │  • paper_deals (Every executed trade, exit reason, PnL)      │
   │  • events (AlgoArena AI debates, reasoning logs, agent votes)  │
   │  • paper_equity_snapshots (Virtual equity telemetry curve)  │
   │  • paper_positions (Active trades & trailing SL/TP)         │
   │  • combo_results (Backtest & forward performance ranks)     │
   └─────────────────────────────────────────────────────────────┘
```

---

## 2. Why Your Trading Memory & History Are 100% Safe

### A. Git Never Touches Your Database
- The SQLite database (`db/algoarena.db`) is listed in `.gitignore` and excluded from all git tracking.
- When you push new code updates to GitHub and pull them on your VPS via `git pull` or `update_from_github.bat`, **Git only updates Python scripts, HTML/CSS, and models. It NEVER overwrites or wipes your local SQLite database!**

### B. Supabase Cloud Redundancy
- Even if a VPS is rebooted, redeployed, or wiped, every single executed deal, AlgoArena AI debate consensus, and equity snapshot is safely stored in Supabase PostgreSQL Cloud.
- Running `sync_supabase.bat` or `python sync_supabase.py --pull` on a new VPS or local workstation automatically reconstructs all historical records using `INSERT OR IGNORE` (no duplicate collisions).

---

## 3. How to Trigger Synchronization

There are **4 convenient ways** to synchronize data anytime:

### Method 1: 1-Click Desktop Batch Script
On either your Local machine or the VPS:
- Double click **`sync_supabase.bat`** (or run `python sync_supabase.py`).
- It automatically pushes local changes to Supabase and pulls remote changes from Supabase.
- Specific direction options:
  ```bash
  python sync_supabase.py --push    # Push local data to Supabase Cloud
  python sync_supabase.py --pull    # Pull Supabase Cloud data into local SQLite
  python sync_supabase.py           # Full bi-directional sync (Push + Pull)
  ```

### Method 2: Web Dashboard 1-Click Button
- Open the AlgoArena dashboard (`http://127.0.0.1:8090` or your Cloudflare Tunnel URL).
- Look at the top right toolbar actions next to "↻ Sync".
- Click **`☁️ Cloud Sync`**.
- An instant confirmation popup displays exactly how many deals, AI debate deliberations, and equity points were pushed and pulled.

### Method 3: Telegram Mobile Bot (`/sync`)
From your phone anywhere in the world, simply message your Telegram bot:
- `/sync` — Full bi-directional synchronization.
- `/sync push` — Force push VPS deals and debates to Cloud.
- `/sync pull` — Pull latest cloud records.
The bot replies with an instant summary report!

### Method 4: REST API Endpoint
Automated callers can trigger sync via HTTP POST:
```bash
curl -X POST http://127.0.0.1:8090/api/sync/supabase?direction=sync
```

---

## 4. Workflows

### Scenario A: After a VPS Trading Session -> Sync to Local
1. Bot executes trades on VPS and generates AlgoArena Risk Guard debate logs.
2. The bot can push automatically or you can send `/sync` to Telegram (or run `sync_supabase.bat` on the VPS).
3. On your local workstation, run `python sync_supabase.py --pull` or click **`☁️ Cloud Sync`** on your local dashboard.
4. All VPS trades and debates are now instantly visible on your local workstation's TradingView charts and history tables!

### Scenario B: Pushing New Code Updates to VPS
1. On your local machine, make your code improvements and push to GitHub:
   ```bash
   git add .
   git commit -m "Your update message"
   git push origin main
   ```
2. On your VPS, run:
   ```bash
   git pull origin main
   ```
   *(Or extract `vps_update.zip` using `install_update.bat`)*
3. Your code is updated to the latest commit. Your local SQLite database `algoarena.db` and all your trades remain completely intact!
