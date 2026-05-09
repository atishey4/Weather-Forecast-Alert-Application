# 🌤️ Weather Forecast & Alert Application

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-009688?style=flat-square&logo=fastapi&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-1.33+-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![Open-Meteo](https://img.shields.io/badge/Open--Meteo-Free%20API-00BFFF?style=flat-square)
![SQLite](https://img.shields.io/badge/SQLite-3-003B57?style=flat-square&logo=sqlite&logoColor=white)
![APScheduler](https://img.shields.io/badge/APScheduler-3.x-4CAF50?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

> ⚠️ **Educational Project** — This application is built for learning purposes only.
> It is **not** a professional weather service. Do not rely on it for safety-critical decisions.

---

## 📸 Screenshots

![Screenshot 1](screenshots/Screenshot%202026-05-09%20195051.png)

![Screenshot 2](screenshots/Screenshot%202026-05-09%20195102.png)

![Screenshot 3](screenshots/Screenshot%202026-05-09%20195111.png)

---

## 📖 Project Overview

A full-stack weather forecasting and alerting system built entirely in Python. It fetches real-time 7-day forecasts from the **Open-Meteo API** (completely free, no API key required), stores data in a local SQLite database, evaluates configurable alert rules, dispatches notifications via Telegram and email, and exposes the data through both a **FastAPI REST API** and a **Streamlit interactive dashboard**.

### What this project demonstrates

- Real-world API consumption with `httpx` and data persistence with SQLite
- Rule-based alert engine with debouncing and multi-channel dispatch
- Scheduled background jobs with APScheduler
- REST API design with FastAPI and Pydantic validation
- Interactive data dashboards with Streamlit and Plotly
- Matplotlib chart generation and CSV report export
- Offline simulation pipeline for testing without any API key

---

## 🏗️ Folder Structure

```
Weather-Forecast-Alert-Application/
│
├── main.py                  ← CLI entry point (--mode api | --mode simulate)
├── requirements.txt         ← All Python dependencies
├── .env.example             ← Environment variable template (safe to commit)
├── .gitignore               ← Python standard + .env excluded
│
├── api/
│   ├── __init__.py
│   └── app.py               ← FastAPI application (6 endpoints)
│
├── src/
│   ├── __init__.py
│   ├── ingest.py            ← Open-Meteo fetch, raw persist, upsert series
│   ├── rules.py             ← AlertRule dataclass + 5 default rules + evaluate()
│   ├── plots.py             ← Matplotlib chart functions → outputs/
│   ├── report.py            ← CSV report generator → reports/
│   ├── simulate.py          ← Offline simulation pipeline
│   └── app_streamlit.py     ← Streamlit dashboard
│
├── db/
│   ├── __init__.py
│   ├── schema.sql           ← SQLite table definitions
│   ├── init_db.py           ← DB initialiser + city seed data
│   └── weather.db           ← SQLite database (auto-created, gitignored)
│
├── jobs/
│   ├── __init__.py
│   ├── refresh.py           ← refresh_all() batch fetch job
│   └── scheduler.py         ← APScheduler: refresh/2h, alerts/30min
│
├── notify/
│   ├── __init__.py
│   └── alert.py             ← Telegram + SMTP dispatch with 12h debounce
│
├── data/
│   └── sample_weather.json  ← Realistic Open-Meteo payload for offline demo
│
├── outputs/                 ← Generated PNG charts (gitignored)
├── reports/                 ← Generated CSV reports (gitignored)
├── screenshots/             ← App screenshots
├── tool/                    ← Tool utilities
├── images/                  ← Static assets
└── docs/                    ← Project documentation
```

---

## 🔑 API Setup

### Open-Meteo (Primary — Free, No Key Required)

This project uses [Open-Meteo](https://open-meteo.com/) as its primary weather data source.

- ✅ Completely **free** for non-commercial use
- ✅ **No account** or API key needed
- ✅ Up to **7-day hourly + daily forecasts**
- ✅ Global coverage including all Indian cities

No configuration needed — the app works out of the box.

### OpenWeatherMap (Optional)

The `OWM_API_KEY` in `.env` is reserved for future integration with OpenWeatherMap's API (current conditions, historical data). It is **not required** to run this project.

Get a free key at [openweathermap.org/api](https://openweathermap.org/api) if you plan to extend the project.

### Telegram Notifications (Optional)

To enable Telegram alerts:
1. Message [@BotFather](https://t.me/BotFather) on Telegram → create a bot → copy the token
2. Get your chat ID via [@userinfobot](https://t.me/userinfobot)
3. Add both to your `.env` file

### Email Notifications (Optional)

SMTP email alerts work with any provider. For Gmail:
1. Enable 2FA on your Google account
2. Generate an [App Password](https://myaccount.google.com/apppasswords)
3. Use the app password as `SMTP_PASS` in `.env`

---

## ⚙️ `.env.example` Explanation

Copy `.env.example` to `.env` and fill in your values:

```env
# OpenWeatherMap (optional)
OWM_API_KEY=your_key_here

# Telegram Bot (optional — for push alerts)
TELEGRAM_BOT_TOKEN=your_telegram_bot_token_here
TELEGRAM_CHAT_ID=your_chat_id_here

# SMTP Email (optional — for email alerts)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password_here
SMTP_FROM=your_email@gmail.com
ALERT_TO_EMAIL=recipient@example.com
```

| Variable | Required | Purpose |
|---|---|---|
| `OWM_API_KEY` | No | Reserved for OpenWeatherMap extension |
| `TELEGRAM_BOT_TOKEN` | No | Enables Telegram push alerts |
| `TELEGRAM_CHAT_ID` | No | Target chat for Telegram messages |
| `SMTP_HOST/PORT/USER/PASS` | No | Enables email alert dispatch |
| `ALERT_TO_EMAIL` | No | Recipient address for email alerts |

---

## 🔐 Security Warning

> ⚠️ **NEVER commit your `.env` file or any real API keys to version control.**

This project's `.gitignore` already excludes `.env`. Always verify before pushing:

```bash
git status          # .env must NOT appear here
git diff --cached   # confirm no secrets in staged files
```

**Best practices followed in this project:**
- `.env` is in `.gitignore` — never tracked by Git
- `.env.example` uses placeholder values only — safe to commit
- API keys are loaded at runtime via `python-dotenv`, never hardcoded
- `db/weather.db` is gitignored — no personal location data in version control

---

## 🚀 How to Run

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Initialise the database

Seeds Delhi, Mumbai, and Bengaluru automatically:

```bash
python db/init_db.py
```

### 3. Mode A — Offline Simulation (no API key needed)

Loads `data/sample_weather.json`, runs the full pipeline, generates charts and reports:

```bash
python main.py --mode simulate
```

### 4. Mode B — Live API Mode

Fetches real data from Open-Meteo for all cities, then starts the FastAPI server:

```bash
python main.py --mode api
# API docs available at: http://127.0.0.1:8000/docs

# Custom port:
python main.py --mode api --port 8080
```

### 5. Streamlit Dashboard

```bash
streamlit run src/app_streamlit.py
```

### 6. Background Scheduler (runs continuously)

```bash
python jobs/scheduler.py
# Refreshes data every 2 hours
# Evaluates alerts every 30 minutes
```

### 7. Generate reports only

```bash
python src/report.py         # CSV for all cities
python src/simulate.py       # Full offline run with charts
```

---

## 🖥️ Sample Terminal Output

```
╔══════════════════════════════════════════════════════════════╗
║       🌤   Weather Forecast & Alert Application  🌤          ║
║            Powered by Open-Meteo (free, no key)              ║
╚══════════════════════════════════════════════════════════════╝

╔══════════════════════════════════════════════════════════════╗
║  ⚠   EDUCATIONAL PROJECT — NOT A PROFESSIONAL SERVICE   ⚠   ║
║  This project is for educational purposes only.              ║
║  Do NOT rely on it for safety-critical weather decisions.    ║
╚══════════════════════════════════════════════════════════════╝

───────────────────────────────────────────────────────────────
  Terminal Summary — All Cities
───────────────────────────────────────────────────────────────

  ┌─ DELHI ────────────────────────────────────────────────────
  City         Delhi
  Temp         43.8°C
  Humidity     14%
  SMA Signal   RISING  ↑
  Alerts       HEAT_WAVE, UV_HIGH, RAIN_SOON, WIND_HIGH
  Report       reports/delhi_forecast.csv
  └────────────────────────────────────────────────────────────

  ┌─ MUMBAI ───────────────────────────────────────────────────
  City         Mumbai
  Temp         32.1°C
  Humidity     78%
  SMA Signal   STABLE  →
  Alerts       none
  Report       reports/mumbai_forecast.csv
  └────────────────────────────────────────────────────────────
```

---

## 🚨 Sample Alert Output

```
⚠️  Alert fired: [WARN    ] RAIN_SOON
     Rain expected in the next 12 hours (≥ 60 % probability)

🔴  Alert fired: [CRITICAL] HEAT_WAVE
     Heat wave alert: tomorrow's high ≥ 40 °C

⚠️  Alert fired: [WARN    ] WIND_HIGH
     Strong wind gusts (≥ 15 m/s) expected in the next 24 hours

⚠️  Alert fired: [WARN    ] UV_HIGH
     High UV index today (UV ≥ 8) — sun protection advised
```

**Alert rules (configurable in `src/rules.py`):**

| Code | Condition | Severity |
|---|---|---|
| `RAIN_SOON` | Precip probability ≥ 60% in next 12 h | ⚠️ warn |
| `HEAT_WAVE` | Tomorrow's high ≥ 40 °C | 🔴 critical |
| `WIND_HIGH` | Wind gusts ≥ 15 m/s in next 24 h | ⚠️ warn |
| `UV_HIGH` | UV index ≥ 8 today | ⚠️ warn |
| `FOG_RISK` | Visibility < 1 km in next 6 h | ℹ️ info |

---

## 📊 Generated Charts

All charts are saved as PNGs to `outputs/` using a dark-themed matplotlib style.

| File | Description |
|---|---|
| `{city}_temp_trend.png` | 24-hour temperature line chart with feels-like overlay, annotated min/max |
| `{city}_rain_forecast.png` | 7-day rainfall bar chart with precipitation probability overlay |
| `{city}_humidity_wind.png` | Dual-axis: humidity % (filled area) + wind speed & gusts |
| `{city}_alert_summary.png` | Horizontal bar chart of alert counts by severity |

---

## 🌐 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/locations` | List all monitored cities |
| `POST` | `/locations` | Add a new city |
| `GET` | `/forecast/hourly?location_id=&hours=24` | Upcoming hourly rows |
| `GET` | `/forecast/daily?location_id=&days=7` | Upcoming daily rows |
| `GET` | `/alerts?location_id=` | Evaluate and return fired alerts |
| `POST` | `/refresh/{location_id}` | Fetch fresh data for one city |
| `GET` | `/health` | API liveness probe |

Interactive docs: **http://127.0.0.1:8000/docs**

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.11+ |
| Weather Data | [Open-Meteo](https://open-meteo.com/) (free, no key) |
| HTTP Client | `httpx` (async-ready) |
| REST API | FastAPI + Uvicorn |
| Data Validation | Pydantic v2 |
| Database | SQLite (via stdlib `sqlite3`) |
| Scheduling | APScheduler 3.x |
| Dashboard | Streamlit + Plotly |
| Charts | Matplotlib |
| Data Processing | Pandas |
| Notifications | `requests` (Telegram) + `smtplib` (SMTP) |
| Config | `python-dotenv` |

---

## 🎓 Learning Outcomes

By studying and running this project, you will learn:

1. **API Integration** — How to consume a RESTful weather API with `httpx`, handle errors, and store responses for replay.

2. **Database Design** — SQLite schema with foreign keys, composite primary keys, WAL mode, and index design for time-series data.

3. **Rule Engine Pattern** — Building a clean, extensible alert rule system with dataclasses and pure callable conditions.

4. **FastAPI** — Designing REST endpoints with Pydantic models, dependency injection, and automatic OpenAPI documentation.

5. **Scheduling** — Running background jobs at fixed intervals with APScheduler, including graceful shutdown on SIGTERM.

6. **Multi-channel Notifications** — Dispatching alerts via Telegram Bot API and SMTP with deduplication (debouncing).

7. **Streamlit Dashboards** — Building interactive data dashboards with Plotly charts, custom CSS, and real-time DB queries.

8. **Data Visualisation** — Using Matplotlib for publication-quality dark-themed charts with dual axes, fills, and annotations.

9. **Project Structure** — Organising a multi-module Python project with clear separation of concerns (ingest / rules / notify / api / jobs).

10. **Security Practices** — Managing secrets with `.env`, keeping keys out of version control, and using `.gitignore` correctly.

---

## 📄 License

This project is released under the **MIT License** for educational use.

Data is provided by [Open-Meteo](https://open-meteo.com/) under the
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) license.

---

*Built as an educational Python project · Not affiliated with any weather service*
