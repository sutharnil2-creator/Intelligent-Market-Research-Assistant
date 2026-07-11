# Intelligent-Market-Research-Assistant

# ────────────────────────────────────────────────
# Intelligent Market Research Assistant
# README
# IBM watsonx.ai + Granite + LangChain + ChromaDB
# ────────────────────────────────────────────────

# 🧠 Intelligent Market Research Assistant

> **Hackathon-ready Full Stack AI Application**  
> Powered by **IBM watsonx.ai**, **IBM Granite Foundation Model**, **LangChain**, **ChromaDB**, **FastAPI**, and **React**.

---

## 🏗️ Architecture Overview

```
┌──────────────────────────────────────────────────────────────────────┐
│                        React Frontend                                │
│  Dashboard │ Analysis │ Predictions │ Chat (RAG) │ Reports │ Admin  │
└────────────────────────────┬─────────────────────────────────────────┘
                             │ REST API (JWT)
┌────────────────────────────▼─────────────────────────────────────────┐
│                    FastAPI Backend                                    │
│  Auth │ Dashboard │ Analysis │ Predictions │ Reports │ Admin         │
│  Upload │ Chat │ Competitors │ Alerts │ IBM Orchestrate Workflow      │
└───┬─────────────┬──────────────┬──────────────┬────────────────────┘
    │             │              │              │
  PostgreSQL   ChromaDB       IBM COS        Redis
  (main DB)   (vectors)     (file store)   (cache/celery)
    │
┌───▼────────────────────────────────────────────────────────────────┐
│                   IBM watsonx.ai (Granite)                         │
│  Executive Summary │ SWOT │ Trends │ Predictions │ Recommendations │
│  Sentiment │ Competitor Analysis │ Pain Points │ Product Ideas      │
└───────────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
intelligent-market-research/
├── backend/
│   ├── app/
│   │   ├── agents/           # IBM Orchestrate multi-agent pipeline
│   │   │   ├── orchestrator.py    # Master agent (LangChain REACT)
│   │   │   ├── news_agent.py
│   │   │   ├── social_media_agent.py
│   │   │   ├── sentiment_agent.py
│   │   │   ├── competitor_agent.py
│   │   │   ├── forecast_agent.py
│   │   │   └── report_agent.py
│   │   ├── core/
│   │   │   ├── config.py     # Settings (pydantic-settings)
│   │   │   ├── database.py   # Async SQLAlchemy engine
│   │   │   ├── security.py   # JWT + bcrypt
│   │   │   └── dependencies.py
│   │   ├── models/           # SQLAlchemy ORM models
│   │   │   ├── user.py
│   │   │   ├── report.py
│   │   │   ├── market_data.py
│   │   │   ├── competitor.py
│   │   │   └── analytics.py  # Prediction, Alert, Chat, Upload, Analytics
│   │   ├── routes/           # FastAPI routers
│   │   │   ├── auth.py
│   │   │   ├── dashboard.py
│   │   │   ├── analysis.py
│   │   │   ├── predictions.py
│   │   │   ├── reports.py
│   │   │   ├── uploads.py
│   │   │   ├── chat.py
│   │   │   ├── competitors.py
│   │   │   ├── alerts.py
│   │   │   ├── admin.py
│   │   │   └── orchestrate.py
│   │   ├── schemas/          # Pydantic request/response models
│   │   ├── services/
│   │   │   ├── watsonx_service.py   # IBM Granite LLM service
│   │   │   ├── chroma_service.py    # ChromaDB RAG
│   │   │   ├── report_service.py    # PDF/DOCX/PPTX generation
│   │   │   └── storage_service.py   # IBM COS / local file storage
│   │   └── main.py
│   ├── database/
│   │   └── seed.py           # Sample data seeder
│   ├── alembic/              # Database migrations
│   ├── .env.example
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── pages/
│   │   │   ├── auth/         # Login, Register
│   │   │   ├── DashboardPage.tsx
│   │   │   ├── AnalysisPage.tsx
│   │   │   ├── PredictionsPage.tsx
│   │   │   ├── CompetitorsPage.tsx
│   │   │   ├── ChatPage.tsx
│   │   │   ├── UploadPage.tsx
│   │   │   ├── ReportsPage.tsx
│   │   │   ├── AlertsPage.tsx
│   │   │   ├── ProfilePage.tsx
│   │   │   └── AdminPage.tsx
│   │   ├── components/layout/
│   │   │   ├── Layout.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── TopBar.tsx
│   │   ├── store/            # Zustand state management
│   │   ├── lib/              # Axios API client, nav config
│   │   └── App.tsx
│   ├── tailwind.config.js
│   ├── vite.config.ts
│   ├── package.json
│   └── Dockerfile
└── docker-compose.yml
```

---

## 🚀 Quick Start (Local Development)

### Prerequisites
- Python 3.11+
- Node.js 20+
- Docker & Docker Compose
- PostgreSQL 16 (or use Docker)
- IBM watsonx.ai account (for AI features)

### 1. Clone and setup

```bash
git clone <repo-url>
cd intelligent-market-research
```

### 2. Start infrastructure (PostgreSQL + Redis + ChromaDB)

```bash
docker-compose up postgres redis chromadb -d
```

### 3. Backend setup

```bash
cd backend
python -m venv venv
source venv/bin/activate       # Windows: venv\Scripts\activate
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env — add your IBM watsonx.ai credentials

# Run migrations & seed data
python database/seed.py

# Start API server
uvicorn app.main:app --reload --port 8000
```

### 4. Frontend setup

```bash
cd frontend
npm install
npm run dev
```

**App is live at:** http://localhost:3000  
**API docs:** http://localhost:8000/api/docs

---

## 🐳 Docker Deployment (Production)

```bash
# Copy and configure environment
cp backend/.env.example backend/.env
# Edit backend/.env with your credentials

# Build and start all services
docker-compose up --build -d

# Check logs
docker-compose logs -f backend

# Access
# Frontend: http://localhost:3000
# Backend API: http://localhost:8000
# API Docs: http://localhost:8000/api/docs
```

---

## 🤖 IBM AI Stack

### IBM Granite Models Used
| Task | Model |
|------|-------|
| Chat, Q&A | `ibm/granite-13b-chat-v2` |
| Analysis, Summaries | `ibm/granite-13b-instruct-v2` |

### IBM Orchestrate Agent Workflow
```
User Query
    ↓
NewsAgent (fetch/simulate news)
    ↓
SocialMediaAgent (Reddit + Twitter)
    ↓
SentimentAgent (IBM Granite sentiment analysis)
    ↓
CompetitorAgent (competitive intelligence)
    ↓
ForecastAgent (demand/growth/risk predictions)
    ↓
ReportAgent (compile executive report)
    ↓
Final Response
```

### RAG Pipeline (ChromaDB)
```
Upload (PDF/CSV/DOCX/Image)
    ↓ Text extraction (PyMuPDF / OCR / pandas)
Chunking (512 tokens, 64 overlap)
    ↓ HuggingFace embeddings (all-MiniLM-L6-v2)
ChromaDB Vector Store
    ↓ Semantic search (cosine similarity)
Top-k chunks → IBM Granite → Answer
```

---

## 🔌 API Endpoints

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/register` | Register new user |
| POST | `/api/v1/auth/login` | Login (JWT) |
| GET | `/api/v1/auth/me` | Current user |
| PUT | `/api/v1/auth/me` | Update profile |

### Dashboard
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/dashboard/overview` | KPIs & stats |
| GET | `/api/v1/dashboard/trending-topics` | Top keywords |
| GET | `/api/v1/dashboard/sentiment-graph` | Sentiment over time |

### AI Analysis
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/analysis/full` | Full AI analysis |
| POST | `/api/v1/analysis/sentiment` | Batch sentiment analysis |
| GET | `/api/v1/analysis/competitor-analysis` | Competitor compare |

### Predictions
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/predictions/generate` | Generate AI forecasts |
| GET | `/api/v1/predictions` | List predictions |

### Chat (RAG)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/chat/message` | Send message (RAG + Granite) |
| GET | `/api/v1/chat/history/{session_id}` | Chat history |
| GET | `/api/v1/chat/sessions` | List sessions |

### Reports
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/reports` | List reports |
| GET | `/api/v1/reports/{id}` | Get report |
| GET | `/api/v1/reports/{id}/download/{format}` | Download PDF/DOCX/PPTX |
| DELETE | `/api/v1/reports/{id}` | Delete report |

### IBM Orchestrate
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/orchestrate/workflow` | Run multi-agent workflow |

---

## 🔑 Demo Credentials

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@marketresearch.ai | Admin@123 |
| Analyst | analyst@marketresearch.ai | Analyst@123 |

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| AI/LLM | IBM watsonx.ai + Granite 13B |
| Agents | LangChain + IBM Orchestrate workflow |
| Vector DB | ChromaDB |
| Embeddings | HuggingFace (all-MiniLM-L6-v2) |
| Backend | FastAPI + SQLAlchemy (async) |
| Database | PostgreSQL 16 |
| Cache | Redis |
| File Storage | IBM Cloud Object Storage / Local |
| Frontend | React 18 + TypeScript + Vite |
| Styling | Tailwind CSS |
| Charts | Recharts |
| State | Zustand |
| Auth | JWT (python-jose + bcrypt) |
| Reports | ReportLab (PDF) + python-docx + python-pptx |
| Containers | Docker + Docker Compose |

---

## 📦 Sample Data

Run `python database/seed.py` to populate:
- 2 demo users (admin + analyst)
- 60 market data points across industries
- 4 competitor profiles (Apple, Microsoft, Google, Samsung)
- 5 AI prediction records per type
- 5 AI-generated alerts
- 1 sample Q1 2024 technology report

---

Built with ❤️ using IBM watsonx.ai + IBM Granite
