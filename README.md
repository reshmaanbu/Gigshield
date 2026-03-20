# GigShield - AI-Powered Parametric Insurance Platform for Gig Workers

Protecting delivery workers from income loss caused by external disruptions — automatically, intelligently, and instantly.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Persona-Based Scenarios & Workflow](#persona-based-scenarios--workflow)
3. [Weekly Premium Model & Parametric Triggers](#weekly-premium-model--parametric-triggers)
4. [Web vs Mobile Platform Justification](#web-vs-mobile-platform-justification)
5. [AI/ML Integration Plan](#aiml-integration-plan)
6. [Tech Stack & Development Plan](#tech-stack--development-plan)
7. [Feature Breakdown](#feature-breakdown)
8. [Folder Structure](#folder-structure)
9. [How to Run](#how-to-run)
10. [API Reference](#api-reference)
11. [Roadmap](#roadmap)

---

## Project Overview

India has over 11 million gig delivery workers (Zomato, Swiggy, Blinkit, Zepto, etc.). These workers have no safety net when external conditions — heavy rain, extreme air pollution, traffic shutdowns — make it impossible or dangerous to work.

GigShield solves this with a parametric insurance model:

- No manual claims. No paperwork. No waiting.
- If a measurable condition (e.g., rainfall > 15mm) is triggered, the system automatically detects it, generates a claim, and processes a payout — all without the worker lifting a finger.
- Premiums are weekly, affordable, and dynamically priced based on real-time risk.

**Scope:** GigShield covers income loss due to external environmental disruptions only. It does not cover health, life, accident, or vehicle insurance.

---

## Persona-Based Scenarios & Workflow

### Persona 1 — Ravi, Mumbai Delivery Rider (Zomato)

| Attribute | Detail |
|-----------|--------|
| City | Mumbai |
| Platform | Zomato |
| Weekly Income | Rs. 4,500 |
| Risk Profile | High (monsoon city) |

Ravi registers on GigShield, entering his name, city, platform, and average weekly income. The AI engine pulls Mumbai's real-time rainfall, AQI, and traffic data. His risk score comes out at 72/100 (High), so his weekly premium is Rs. 85.

On Wednesday, Mumbai receives 22mm of rainfall — above the 15mm threshold. GigShield automatically detects the trigger, calculates Ravi's income loss for that day (Rs. 320), and generates a claim. Rs. 320 is instantly credited to his GigShield wallet with a notification: "Heavy rain detected in Mumbai. Rs. 320 credited to your wallet." His no-claim streak resets; his work streak continues.

---

### Persona 2 — Priya, Delhi Delivery Partner (Swiggy)

| Attribute | Detail |
|-----------|--------|
| City | Delhi |
| Platform | Swiggy |
| Weekly Income | Rs. 5,200 |
| Risk Profile | High (severe AQI) |

Priya registers. Delhi's AQI baseline is 210 — immediately flagged as high risk. Her weekly premium is Rs. 96. On a November morning, AQI spikes to 318 (threshold: 150). GigShield triggers an automatic claim for lost income. Priya has maintained a 4-week no-claim streak, earning the "Safe Rider" badge and a Rs. 20 cashback bonus. Payout is processed via simulated UPI within seconds.

---

### Persona 3 — Arjun, Bengaluru Delivery Partner (Blinkit) — Offline Scenario

| Attribute | Detail |
|-----------|--------|
| City | Bengaluru |
| Platform | Blinkit |
| Weekly Income | Rs. 3,800 |
| Risk Profile | Medium |

Arjun is working in a low-connectivity area when heavy rain begins. His app is offline. GigShield's offline mode uses localStorage to log the event and store a pending claim locally. When his phone reconnects, the app auto-syncs with the server, the pending claim is processed, and Rs. 280 is credited to his wallet.

---

### End-to-End User Workflow

```
Register --> Risk Assessment --> Premium Charged --> Work & Earn Streaks
     |
External Disruption Detected (Rain / AQI / Traffic)
     |
Fraud Detection Check --> Claim Auto-Generated --> Payout Processed
     |
Dashboard Updated --> Badges/Rewards Evaluated --> Next Week Cycle
```

---

## Weekly Premium Model & Parametric Triggers

### Premium Calculation

```
risk_score = (rainfall_norm x 0.5) + (aqi_norm x 0.3) + (traffic_norm x 0.2)   [0 to 100]

weekly_premium = 49 + (risk_score x 0.8)

Cap: premium <= 12% of weekly_income
```

| Risk Level | Score Range | Example Premium    |
|------------|-------------|--------------------|
| Low        | 0 to 32     | Rs. 49 to Rs. 75   |
| Medium     | 33 to 65    | Rs. 75 to Rs. 101  |
| High       | 66 to 100   | Rs. 101 to Rs. 129 |

**Why weekly pricing?**

Gig workers earn and spend on a weekly cycle, so premiums aligned to that rhythm are easier to budget. Environmental risk also shifts week to week — a weekly model reflects current conditions rather than annual averages. The lower commitment barrier compared to annual policies increases adoption among workers who are skeptical of traditional insurance.

---

### Parametric Triggers

Parametric insurance pays out based on objective, measurable thresholds rather than subjective loss assessments. This eliminates disputes, fraud, and processing delays.

| Trigger             | Threshold              | Data Source              |
|---------------------|------------------------|--------------------------|
| Rainfall            | > 15 mm/day            | OpenWeatherMap API       |
| AQI                 | > 150                  | AQICN / Mock API         |
| Traffic Disruption  | > 85 congestion index  | Mock Traffic API         |

**Payout formula on trigger:**

```
severity = (trigger_value - threshold) / threshold      [capped at 1.0]
loss_pct = 0.30 + (severity x 0.50)                    [30% to 80% of daily income]
payout   = (weekly_income / 7) x loss_pct
```

**Worked example — Mumbai rainfall = 22mm, threshold = 15mm:**

```
severity = (22 - 15) / 15 = 0.47
loss_pct = 0.30 + (0.47 x 0.50) = 0.535
payout   = (4500 / 7) x 0.535 = Rs. 344
```

---

## Web vs Mobile Platform Justification

**Decision: Web Platform (Progressive Web App approach)**

| Factor                  | Web                          | Native Mobile                    |
|-------------------------|------------------------------|----------------------------------|
| Development speed       | Faster                       | Slower                           |
| Cross-platform          | Works on all devices         | Separate iOS + Android builds    |
| Hackathon demo          | Shareable URL instantly      | APK distribution needed          |
| Offline support         | Via localStorage / SW        | Native support                   |
| Low-end device support  | Browser-based, lightweight   | App size concerns                |
| Regulatory updates      | Deploy instantly             | App store review delays          |

Delivery workers use a wide range of low-end Android devices across varying network conditions. A mobile-responsive web app with offline localStorage support gives maximum reach with minimal friction. It also allows instant deployment and shareable demos during hackathon evaluation without the overhead of APK builds or app store submissions.

---

## AI/ML Integration Plan

### 1. Dynamic Risk Scoring (Current)

Normalizes rainfall, AQI, and traffic variables and computes a weighted composite score. Future upgrade: train a regression model on historical weather and earnings data from NSSO/CMIE gig worker datasets to predict weekly income loss probability per city.

### 2. Premium Pricing Engine (Current)

Linear formula calibrated to city-level baselines. Future upgrade: XGBoost model trained on city, season, historical claim rates, and worker income bracket to output an actuarially sound premium.

### 3. Fraud Detection (Current + Planned)

Current rule-based checks:
- Duplicate claim on the same day for the same trigger type
- More than 3 claims within a 7-day window
- Trigger value exceeds physical possibility
- Location mismatch between registered city and GPS coordinates

Future upgrade: Isolation Forest anomaly detection trained on historical claim patterns to catch sophisticated fraud that fixed rules miss.

### 4. Predictive Risk Alerts (Planned)

LSTM or Facebook Prophet time-series model trained on 7-day weather forecasts to proactively warn workers about high-risk upcoming weeks and prompt them to purchase coverage before the event occurs.

### 5. Gamification Intelligence (Planned)

Reinforcement learning agent that optimizes reward and cashback strategies to balance worker retention with platform financial sustainability.

---

## Tech Stack & Development Plan

### Tech Stack

| Layer              | Technology                              |
|--------------------|-----------------------------------------|
| Frontend           | HTML5, CSS3, Vanilla JavaScript         |
| Charts             | Chart.js                                |
| Maps               | Leaflet.js                              |
| Backend            | Python 3.x + Flask                      |
| Database           | SQLite (via Python sqlite3)             |
| Weather API        | OpenWeatherMap (mock fallback included) |
| AQI API            | AQICN / Mock data                       |
| Traffic API        | Mock data (city-based simulation)       |
| Payment            | Mock UPI/Wallet simulation              |
| Offline            | localStorage + sync API                 |
| Session Management | Flask server-side sessions              |

### Development Phases

**Phase 1 — Foundation**
- Project scaffold (Flask + SQLite)
- User registration and session management
- Database schema design
- Mock API services for weather, AQI, and traffic

**Phase 2 — Core Engine**
- Risk scoring algorithm
- Premium calculation engine
- Parametric trigger detection
- Automated claim generation
- Mock payout processing

**Phase 3 — Fraud and Gamification**
- Rule-based fraud detection
- Work streak and no-claim streak tracking
- Badge award system
- Cashback rewards

**Phase 4 — Frontend and UI**
- Landing and registration page
- Main dashboard with Chart.js analytics
- Hyperlocal risk map via Leaflet.js
- Rewards and badges page
- Mobile-responsive layout

**Phase 5 — Offline and Polish**
- localStorage offline claim storage
- Online sync endpoint
- Final UI polish and animations

---

## Feature Breakdown

### Core Features

| Feature            | Description                                                    |
|--------------------|----------------------------------------------------------------|
| User Onboarding    | Name, city, platform, and weekly income registration           |
| Risk Assessment    | Composite score from rainfall, AQI, and traffic                |
| Weekly Premium     | Dynamic pricing based on live risk score                       |
| Parametric Triggers| Auto-detection of rainfall > 15mm or AQI > 150                 |
| Automated Payout   | Instant mock UPI/wallet credit on trigger                      |
| Fraud Detection    | Duplicate, frequency, and anomaly value checks                 |
| Analytics Dashboard| Earnings, risk levels, claims, and payouts via Chart.js        |

### Differentiator Features

| Feature            | Description                                                    |
|--------------------|----------------------------------------------------------------|
| Gamified Insurance | Work streaks, no-claim streaks, badges, and cashback           |
| Hyperlocal Risk Map| Leaflet.js map with color-coded risk zones per city            |
| Offline Mode       | localStorage pending claims with auto-sync on reconnect        |

---

## Folder Structure

```
gigshield/
├── app.py                  # Main Flask application
├── requirements.txt        # Python dependencies
├── README.md
├── database/
│   └── gigshield.db        # SQLite database (auto-created on first run)
├── static/
│   ├── css/
│   │   └── style.css
│   └── js/
│       ├── main.js         # Registration and risk logic
│       ├── dashboard.js    # Charts and dashboard logic
│       ├── map.js          # Leaflet map and risk zones
│       ├── rewards.js      # Gamification and badges
│       └── offline.js      # localStorage sync logic
└── templates/
    ├── index.html          # Landing and registration page
    ├── dashboard.html      # Main analytics dashboard
    ├── map.html            # Hyperlocal risk map
    └── rewards.html        # Badges and rewards page
```

---

## How to Run

**Prerequisites:** Python 3.8+, pip

```bash
# Clone the repository
git clone https://github.com/your-username/gigshield.git
cd gigshield

# Install dependencies
pip install flask

# Run the application
python app.py
```

Open `http://localhost:5000` in your browser.

No external API keys are required. All weather, AQI, and traffic data uses realistic mock values seeded per city. To connect real APIs, replace the `get_weather_data()` function in `app.py` with actual OpenWeatherMap/AQICN calls and store your keys in a `.env` file.

---

## API Reference

| Endpoint              | Method | Description                                  |
|-----------------------|--------|----------------------------------------------|
| `/register`           | POST   | Register a new user                          |
| `/api/user`           | GET    | Get current user profile                     |
| `/api/risk`           | GET    | Get risk score and premium for user's city   |
| `/api/trigger_check`  | POST   | Check and auto-generate claims if triggered  |
| `/api/claims`         | GET    | Get all claims for the current user          |
| `/api/payouts`        | GET    | Get payout history                           |
| `/api/dashboard_stats`| GET    | Aggregated stats for dashboard charts        |
| `/api/map_data`       | GET    | Risk scores for all cities (map view)        |
| `/api/log_work_day`   | POST   | Log a work day and update streaks            |
| `/api/sync_offline`   | POST   | Sync offline-stored pending claims           |

---

## Roadmap

**Phase 2 — Post Hackathon**
- Real OpenWeatherMap and AQICN API integration
- Real UPI payment gateway (Razorpay / PhonePe)
- SMS/WhatsApp payout notifications (Twilio / MSG91)
- IRDAI sandbox compliance layer

**Phase 3 — Scale**
- ML-based premium pricing model (XGBoost)
- LSTM weather forecasting for proactive alerts
- Isolation Forest fraud detection
- Platform partnerships (Zomato, Swiggy API webhooks)
- Multi-language support (Hindi, Tamil, Telugu, Kannada)
- React Native mobile app

---

## Team

| Role                    | Name |
|-------------------------|------|
| Full Stack Development  | —    |
| ML / AI                 | —    |
| Design                  | —    |
| Product                 | —    |

---

## License

MIT License — free to use, modify, and distribute.

---

*"A rainy day shouldn't mean an empty wallet for someone who delivers your food."*
