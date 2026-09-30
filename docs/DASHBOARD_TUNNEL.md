# 🌐 AlgoArena Web Dashboard & Cloudflare Public Tunnel
**Author & Quantitative Architect:** Mehak Sandhu ([@mehaksandhudev](https://github.com/mehaksandhudev))  
**Frontend Server:** FastAPI + Uvicorn (Port 8090)  
**Public Tunnel:** Cloudflare Quick Tunnel (`cloudflared`)  

---

## ⚡ Overview & Features

The **AlgoArena Web Dashboard** (`algoarena/server/app.py`) is an institutional-grade, dark-themed trading terminal built with Vanilla CSS and HTML (zero Node.js or npm dependencies). It interfaces directly with MT5 IPC pipes, LightGBM models, and SQLite to provide sub-second telemetry:

1. **Institutional TradingView Canvas Terminal:** Butter-smooth 60 FPS lightweight chart with Fixed Range Volume Profile (FRVP), dynamic phase-anchored Point of Control (POC), Judas Sweep liquidity traps, and 9/21 EMAs.
2. **Autonomous Strategy Selector & Deep Thinking:** Real-time regime classification, multi-timeframe H1 bias, order flow volume delta, news shield status, and dynamic ATR risk/reward targets.
3. **Live ML Market Radar:** 5-asset real-time probability radar (Gold, BTC, Euro, Yen, Pound) with live candle countdowns and edge conviction gauges.
4. **AlgoArena AI Multi-Agent Debates:** Streaming consensus feed showing Bull vs Bear deliberations, risk vetoes, and trade proposals.
5. **Open Positions & Real Exness Deals:** Real-time floating P&L, live balance/equity telemetry, and MT5 execution history.

---

## 🔒 100% Free Remote Phone Access via Cloudflare Tunnel

Connecting to an Azure Cloud VPS from an iPhone or laptop typically requires configuring complex inbound security rules, opening public ports, and exposing the server IP to port-scanners.

AlgoArena completely eliminates this friction with **Cloudflare Quick Tunnel Integration**:

```
 [ Local / Azure VPS: Port 8090 ] ◄──► [ Cloudflare Quick Tunnel ] ◄──► [ Encrypted HTTPS Public URL ] ◄──► [ Any Phone / Laptop Worldwide ]
```

### Key Advantages:
- **Zero Port Forwarding:** Azure Network Security Group (NSG) and Windows Firewall rules do not need to be modified.
- **End-to-End HTTPS Encryption:** Accessible over secure SSL (`https://*.trycloudflare.com`).
- **Telegram Synchronization:** The public link is automatically extracted and delivered whenever you send `/dashboard` on Telegram!

---

## 🚀 How to Launch the Dashboard & Tunnel

### 1. Unified Master Launcher:
Double-clicking **`start_all.bat`** automatically launches the Web Dashboard on Port 8090 and activates the Cloudflare Tunnel.

### 2. Standalone Tunnel Launcher:
Double-clicking **`share_dashboard_public.bat`** automatically:
- Scans `bin\`, `Downloads\`, `Desktop\`, and system `PATH` for existing `cloudflared.exe`.
- Starts the secure tunnel to `http://127.0.0.1:8090`.
- Saves the public HTTPS link to `data/tunnel_url.txt`.
- Streams live traffic logs directly in the terminal window.

---

[⬅️ Back to Main README](../README.md)
