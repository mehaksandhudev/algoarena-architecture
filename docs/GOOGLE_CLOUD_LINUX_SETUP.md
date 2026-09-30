# 🐧 Google Cloud Linux (GCP) Deployment Guide for AlgoArena
**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Target Platform:** Google Cloud Platform (Compute Engine) — Ubuntu 22.04 / 24.04 LTS  
**Cost:** 100% Free on GCP Free-Tier or Covered by GCP Free Credits  

---

## ⚡ Overview

This guide explains how to deploy and run **AlgoArena** 24/7 on a **Google Cloud Linux Virtual Machine (VM)** using your free credits without incurring Windows Server license fees.

---

## 🚀 Step 1: Create Your Google Cloud Linux VM

1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Navigate to **Compute Engine** ➔ **VM Instances** ➔ click **Create Instance**.
3. Configure the VM:
   - **Name:** `algoarena-bot`
   - **Region:** Any low-latency region (e.g., `us-central1`, `us-east1`, or `europe-west1`).
   - **Machine Configuration:** 
     - Series: **E2**
     - Machine type: **`e2-medium`** (2 vCPU, 4 GB RAM) or **`e2-micro`** (Always Free Tier eligible).
   - **Boot Disk:**
     - Click **Change**
     - Operating System: **Ubuntu**
     - Version: **Ubuntu 22.04 LTS** (or 24.04 LTS)
     - Boot disk size: **20 GB** (Standard Persistent Disk).
   - **Firewall:**
     - Check **"Allow HTTP traffic"**
     - Check **"Allow HTTPS traffic"**
4. Click **Create**.

---

## 🔑 Step 2: Connect to Your VM

Once the instance is running, click the **SSH** button next to your VM name to open the terminal in your browser.

---

## 📦 Step 3: 1-Command Installation

In the Google Cloud SSH terminal, run:

```bash
# 1. Clone the repository
git clone https://github.com/mehaksandhudev/algoarena.git
cd algoarena

# 2. Run the automated setup script
chmod +x setup_gcp_linux.sh
./setup_gcp_linux.sh
```

This automated script will:
- Install Python 3, Wine, Xvfb, build tools, and dependencies.
- Create a virtual environment (`.venv`).
- Install `mt5linux` and `rpyc` cross-platform connectors.
- Hydrate your historical trade data and AlgoArena Risk Guard loss memory from Supabase Cloud.

---

## ⚙️ Step 4: Configure Your Environment (`.env`)

Verify or paste your Funding Pips credentials:

```bash
nano .env
```

Ensure your `.env` contains:
```env
MT5_LOGIN=your_mt5_login_here
MT5_PASSWORD=your_mt5_password_here
MT5_SERVER=FundingPips-Trial
TELEGRAM_BOT_TOKEN=your_token_here
TELEGRAM_CHAT_ID=your_chat_id_here
```
Press `Ctrl + O` to save, then `Ctrl + X` to exit.

---

## 🏁 Step 5: Launch 24/7 Autonomous Trading

Start all three components (Trading Engine, Web Dashboard, Telegram Bot) with a single command:

```bash
./start_all.sh
```

You will see:
```text
======================================================================
   [SUCCESS] ALL 3 ALGOARENA SYSTEMS ARE RUNNING 24/7 ON LINUX!
   Dashboard: http://127.0.0.1:8090
   PIDs: Paper=14521 | Dashboard=14519 | Telegram=14520
   To view live logs: tail -f logs/paper.log
   To stop all: ./stop_all.sh
======================================================================
```

---

## 📊 Useful Linux Commands

| Action | Command |
| :--- | :--- |
| **View Live Trading Logs** | `tail -f logs/paper.log` |
| **View Dashboard Logs** | `tail -f logs/dashboard.log` |
| **View Telegram Logs** | `tail -f logs/telegram.log` |
| **Stop All Services** | `./stop_all.sh` |
| **Manual Supabase Sync** | `./sync_supabase.sh` |
| **Restart Services** | `./stop_all.sh && ./start_all.sh` |

---

## 🛡️ Cloud Continuity Guarantee

Because all closed trades and AI decisions are automatically mirrored to **Supabase Cloud**, you can seamlessly switch between your **Local Workstation** and your **Google Cloud Linux VM** anytime without losing a single trade, win rate statistic, or ML memory!
