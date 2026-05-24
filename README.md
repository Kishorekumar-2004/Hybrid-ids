# 🛡️ Hybrid IDS — AI-Driven Hybrid Framework for Automated Threat Hunting

> **AN AI-DRIVEN HYBRID FRAMEWORK FOR AUTOMATED THREAT HUNTING USING STATISTICAL ANOMALY DETECTION AND RULE-BASED INTELLIGENCE**

---

## 📌 Overview

**Hybrid IDS** is a full-stack intrusion detection system built with **Ruby on Rails** that combines three layers of threat intelligence into a unified decision engine:

| Layer | Method | Description |
|-------|--------|-------------|
| 🔢 **Statistical** | Z-Score Anomaly Detection | Detects abnormal packet sizes using standard deviation analysis |
| 📜 **Rule-Based** | Signature / Heuristic Rules | Flags known attack patterns (high ports, packet spikes) |
| 🤖 **AI-Powered** | GPT-4o-mini via OpenAI API | Classifies traffic with natural language reasoning |

The **Hybrid Engine** fuses all three layers to produce a final verdict — giving security analysts a smarter, faster, and more explainable threat hunting experience.

---

## 🚀 Features

- **📊 Live Dashboard** — Real-time traffic stats, threat counters, and recent log table
- **🧠 Hybrid Detection Engine** — Combines statistical + rule signals for confident classification
- **🤖 AI Threat Analyzer** — GPT-4o-mini classifies each traffic log (with local fallback)
- **📈 Anomaly Scoring** — Z-score calculated per log entry and stored for analysis
- **📋 Rule Alerts** — Automatic rule triggers for High Port Access & Packet Spikes
- **📤 JSON Import** — Bulk import traffic logs via JSON upload
- **📄 PDF Export** — One-click export of all confirmed threats as a Prawn-generated PDF report
- **🔐 Session Auth** — Simple login/logout session management
- **🐳 Dockerized** — Ready-to-run with Docker and Docker Compose

---

## 🏗️ System Architecture

```
Network Traffic Logs
        │
        ▼
┌───────────────────┐
│   Preprocessor    │  ← Normalize & validate log entries
└────────┬──────────┘
         │
    ┌────▼────────────────────────────────────┐
    │           Detection Service              │
    │                                          │
    │  ┌──────────────────────────────────┐   │
    │  │   StatisticalDetector            │   │
    │  │   Z-Score vs. Threshold (±3σ)    │   │
    │  └──────────────┬───────────────────┘   │
    │                 │ statistical_flag        │
    │  ┌──────────────▼───────────────────┐   │
    │  │   RuleEngine                     │   │
    │  │   High Port (>50000) | Spike     │   │
    │  └──────────────┬───────────────────┘   │
    │                 │ rule_flag               │
    │  ┌──────────────▼───────────────────┐   │
    │  │   HybridEngine (Decision Fusion)  │   │
    │  │   Both  → Confirmed Threat        │   │
    │  │   Stat  → Suspicious Activity     │   │
    │  │   Rule  → Known Threat            │   │
    │  │   None  → Normal Traffic          │   │
    │  └──────────────┬───────────────────┘   │
    └─────────────────┼───────────────────────┘
                      │
         ┌────────────▼──────────────┐
         │   AiThreatAnalyzer        │
         │   GPT-4o-mini (OpenAI)    │
         │   Fallback: local rules   │
         └────────────┬──────────────┘
                      │
              DetectionResult saved
                      │
              Dashboard / PDF Report
```

---

## 🔍 Detection Logic

### Statistical Anomaly Detection
Uses **Z-score normalization** across all recorded packet sizes:

```
z = (packet_size - μ) / σ

If |z| > 3 → Statistical anomaly flagged
```

The anomaly score is stored per log entry for audit and visualization.

### Rule-Based Intelligence
Two signature rules trigger `RuleAlert` records:

- **High Port Access** → `port > 50,000`
- **Packet Size Spike** → `packet_size > 1,500 bytes`

### Hybrid Fusion (Final Classification)

```
statistical_flag=true  + rule_flag=true  → "Confirmed Threat"
statistical_flag=true  + rule_flag=false → "Suspicious Activity"
statistical_flag=false + rule_flag=true  → "Known Threat"
statistical_flag=false + rule_flag=false → "Normal Traffic"
```

### AI Layer (GPT-4o-mini)
Sends packet size + port to OpenAI for natural language classification with graceful local fallback if API is unavailable.

---

## 🧱 Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | Ruby on Rails 7.2 |
| Database | SQLite3 |
| Frontend | Bootstrap 5.3 + Hotwire (Turbo/Stimulus) |
| AI | OpenAI GPT-4o-mini (`ruby-openai`) |
| Statistics | `descriptive_statistics` gem |
| PDF Export | `prawn` + `prawn-table` |
| Web Server | Puma |
| Deployment | Docker + Docker Compose |

---

## 📁 Project Structure

```
hybrid_ids/
├── app/
│   ├── controllers/
│   │   ├── dashboard_controller.rb     # Main dashboard + AI insights + PDF export
│   │   ├── traffic_logs_controller.rb  # Log CRUD + JSON import
│   │   └── session_controller.rb       # Auth
│   ├── services/
│   │   ├── hybrid_engine.rb            # ⭐ Core decision fusion
│   │   ├── statistical_detector.rb     # Z-score anomaly detection
│   │   ├── rule_engine.rb              # Signature-based rules
│   │   ├── ai_threat_analyzer.rb       # OpenAI GPT-4o-mini classifier
│   │   ├── detection_service.rb        # Orchestrates all detectors
│   │   ├── preprocessor.rb             # Log normalization
│   │   ├── data_collector.rb           # Data pipeline
│   │   └── json_import_service.rb      # Bulk JSON import
│   ├── models/
│   │   ├── traffic_log.rb              # Core log model
│   │   ├── detection_result.rb         # Final verdict store
│   │   └── rule_alert.rb              # Rule trigger records
│   └── views/
│       ├── dashboard/                  # Dashboard UI
│       └── traffic_logs/               # Log management UI
├── config/
│   └── routes.rb
├── Dockerfile
└── Gemfile
```

---

## ⚙️ Setup & Installation

### Prerequisites
- Ruby 3.x
- Bundler
- SQLite3
- OpenAI API Key

### Local Setup

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/hybrid_ids.git
cd hybrid_ids

# Install dependencies
bundle install

# Setup database
rails db:create db:migrate

# Configure environment variables
cp .env.example .env
# Add your OpenAI API key to .env:
# OPENAI_API_KEY=sk-...

# Start the server
rails server
```

Visit `http://localhost:3000`

### Docker Setup

```bash
docker-compose up --build
```

---

## 🔧 Environment Variables

Create a `.env` file in the root:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

---

## 📊 API Endpoints

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/` | Dashboard home |
| GET | `/traffic_logs` | List all traffic logs |
| GET | `/traffic_logs/upload_json` | JSON upload form |
| POST | `/traffic_logs/import_json` | Import traffic logs from JSON |
| DELETE | `/traffic_logs/reset_all` | Delete all logs |
| GET | `/generate_ai_insight` | Trigger AI dashboard insight |
| POST | `/dashboard/generate_ai_summary` | Generate AI threat summary |
| GET | `/export_threats_pdf` | Download PDF threat report |
| GET | `/login` | Login page |
| POST | `/login` | Authenticate |
| DELETE | `/logout` | Sign out |

---

## 🧪 Sample JSON Import Format

```json
[
  {
    "source_ip": "192.168.1.10",
    "destination_ip": "10.0.0.5",
    "port": 52345,
    "protocol": "TCP",
    "packet_size": 1800,
    "logged_at": "2024-01-15T10:30:00Z"
  }
]
```

---

## 📄 PDF Report

Export all **Confirmed Threats** to a formatted PDF report via the dashboard. The report includes:
- Source & Destination IP
- Port, Protocol, Packet Size
- Detection timestamp

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

---

## 📜 License

This project is licensed under the MIT License.

---

## 👨‍💻 Author

Built as an academic/research project exploring the intersection of **machine learning**, **statistical methods**, and **rule-based intelligence** for next-generation intrusion detection systems.

---

> *"Threats don't announce themselves — Hybrid IDS hunts them."*
