# MindLit AI — Mental-Health Literature Research Platform

[![CI](https://github.com/mindlit-ai/mindlit/actions/workflows/ci.yml/badge.svg)](https://github.com/mindlit-ai/mindlit)
[![Python 3.13](https://img.shields.io/badge/python-3.13-blue.svg)](https://www.python.org/downloads/)
[![Next.js 14](https://img.shields.io/badge/next.js-14.2-black.svg)](https://nextjs.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

MindLit AI is a production-grade, multi-user scientific research and educational platform designed for researchers in **psychology, psychiatry, neuroscience, and behavioral science**.

MindLit AI enables researchers to discover published scientific literature, ingest authorized scientific PDFs, automatically extract empirical study parameters (study designs, sample sizes, methodology, findings, limitations), run grounded Retrieval-Augmented Generation (RAG) queries with verified page-level citations, compare studies across multi-paper matrices, and generate editable literature review drafts with BibTeX and RIS bibliography exports.

---

## ⚠️ Strict Safety Boundary & Regulatory Scope

> **REGULATORY NOTICE:** MindLit AI is strictly a scientific research and educational platform, **NOT a diagnostic system, medical device, or clinical decision support application**.

- **No Medical Diagnoses:** MindLit AI will not diagnose users, interpret personal psychiatric symptoms, or provide personalized clinical assessments.
- **No Treatment or Prescribing:** The system does not recommend personal medications, dosages, or individual therapeutic interventions.
- **Academic Redirection:** Any inquiry attempting self-diagnosis or symptom interpretation is automatically refused and redirected to objective published scientific literature.
- **Crisis Response:** Standard crisis support resources (e.g. 988 Suicide & Crisis Lifeline in the US, 111 in the UK) are provided whenever self-harm or distress signals are detected.
- **Zero Emotion Detection:** As mandated by research safety policies, no emotion-detection or mental-illness prediction models are deployed.

---

## Architecture Overview

```
                                      ┌────────────────────────────────────────────────┐
                                      │              Next.js 14 Frontend               │
                                      │   (TypeScript, Tailwind CSS, Lucide Icons)     │
                                      └───────────────────────┬────────────────────────┘
                                                              │ HTTP / JSON
                                                              ▼
                                      ┌────────────────────────────────────────────────┐
                                      │             FastAPI Backend (Python)           │
                                      │   - RBAC & Tenant Isolation (Owner/Editor/View)│
                                      │   - Safety Boundary & Regulatory Guardrails    │
                                      └───────┬───────────────┬────────────────┬───────┘
                                              │               │                │
            ┌─────────────────────────────────┴──┐            │       ┌────────┴────────────────────────┐
            │   Scientific Literature Connectors  │            │       │       AI & RAG Pipeline         │
            │   - Europe PMC (REST API)           │            │       │   - Hybrid Retrieval (Vec + KW) │
            │   - PubMed (NCBI E-utilities)       │            │       │   - Gemini / OpenAI Adapter     │
            │   - Crossref (Polite Pool Mailto)   │            │       │   - Deterministic Synthesizer   │
            │   - OpenAlex (Inverted Index Recon) │            │       │   - 100% Citation Validation    │
            │   - Identifier Deduplication Engine │            │       └─────────────────────────────────┘
            └────────────────────────────────────┘            │
                                                              ▼
                                      ┌────────────────────────────────────────────────┐
                                      │            Data & Storage Services             │
                                      │   - PostgreSQL + pgvector (or SQLite dev)      │
                                      │   - Alembic Versioned Migrations               │
                                      │   - PyMuPDF Page-Grounded Text Extraction      │
                                      │   - S3 / MinIO Private Storage (SHA-256)       │
                                      └────────────────────────────────────────────────┘
```

---

## Core Features

1. **Research Dashboard**: Workspace telemetry, saved studies, active PDF processing queues, curated collections, and quick actions.
2. **Advanced Scientific Literature Search**:
   - Real-time querying across 4 major repositories: **Europe PMC**, **PubMed**, **Crossref**, and **OpenAlex**.
   - Cautious deduplication using DOI, PMID, PMCID, and fuzzy title matching.
   - Filters for publication years, open-access status, authors, journals, and relevance/date sorting.
   - Search history logging for reproducibility.
3. **Secure PDF Ingestion & Analysis**:
   - PyMuPDF page-boundary preservation and section detection (Abstract, Introduction, Methods, Results, Discussion, Limitations).
   - Automated study parameter extraction: Study Design (e.g., RCT, Meta-Analysis), Sample Size ($N$), Population criteria, Findings, Limitations.
   - Human-in-the-loop review interface allowing researchers to verify and edit extractions.
4. **Evidence-Grounded AI Research Assistant (RAG)**:
   - Hybrid retrieval combining dense semantic similarity and BM25-style keyword matching.
   - Every claim is tied to real retrieved passages with page-level citations.
   - Automatic detection of insufficient evidence and explicit highlighting of study disagreements.
5. **Multi-Paper Study Comparison Engine**:
   - Side-by-side comparative matrices contrasting designs, sample sizes, interventions, and effect sizes.
   - Explicit warnings distinguishing observational associations from causal conclusions.
6. **Literature Review & Report Generator**:
   - Automated drafting of standard 9-section academic literature reviews.
   - In-browser Markdown live editor with version history snapshots.
   - One-click exports to Markdown (`.md`), BibTeX (`.bib`), and RIS (`.ris`).
7. **Multi-Tenant Collaboration & RBAC**:
   - Isolated workspaces with strict database-level tenant filtering.
   - Granular roles: `owner` (full administration), `editor` (upload and annotate), `viewer` (read-only).
   - Full audit logging for security-sensitive actions.

---

## Technology Stack

- **Frontend**: Next.js 14, React 18, TypeScript, Tailwind CSS, Lucide Icons.
- **Backend**: Python 3.13, FastAPI, Pydantic v2, SQLAlchemy 2.0, Alembic, PyMuPDF, NumPy, HTTPX, PyJWT, Bcrypt.
- **Database**: PostgreSQL with pgvector extension (production) / SQLite (development).
- **Storage**: S3-compatible object storage (MinIO/AWS S3) or secure local filesystem.
- **Async Workers**: FastAPI BackgroundTasks / Celery + Redis.
- **Deployment**: Docker, Docker Compose, GitHub Actions CI.

---

## Quickstart & Installation

### Prerequisites
- Python 3.11+ (Python 3.13 recommended)
- Node.js 18+ and npm
- Docker and Docker Compose (optional for containerized deployment)

### 1. Clone & Configure Environment

```bash
git clone https://github.com/mindlit-ai/mindlit.git
cd mindlit
cp .env.example .env
```

Review `.env` to configure your API keys (optional; the application runs fully offline with local embedding and synthesis fallbacks):
```env
LLM_PROVIDER=gemini
LLM_API_KEY=your_gemini_api_key_here
USER_EMAIL=researcher@university.edu
```

### 2. Backend Setup

```bash
# Create and activate virtual environment
python -m venv .venv

# On Linux/macOS:
source .venv/bin/activate
# On Windows PowerShell:
.\.venv\Scripts\Activate.ps1

# Install dependencies
pip install -r backend/requirements.txt

# Run database migrations
alembic upgrade head

# Start FastAPI server
uvicorn app.main:app --app-dir backend --reload --port 8000
```
Backend API will be accessible at `http://localhost:8000`.  
Interactive OpenAPI documentation is available at `http://localhost:8000/docs`.

### 3. Frontend Setup

```bash
cd frontend
npm install
npm run dev
```
Frontend interface will be live at `http://localhost:3000`.

---

## Running with Docker Compose

To start the full stack including PostgreSQL with pgvector, Redis, MinIO, Backend, Worker, and Frontend:

```bash
docker-compose up --build -d
```

Service endpoints:
- Frontend: `http://localhost:3000`
- Backend API: `http://localhost:8000`
- MinIO Object Storage Console: `http://localhost:9001` (User: `minioadmin`, Pass: `minioadmin`)

---

## Automated Testing & AI Quality Evaluation

### Running Backend Unit & Security Tests
The test suite includes 16 unit, integration, tenant-isolation, RBAC, and safety tests:

```bash
pytest backend/tests -v
```

### Running the AI Quality Benchmark
To measure retrieval accuracy, citation resolution rate, and safety compliance:

```bash
python evaluation/run_evaluation.py
```

**Measured Benchmark Scorecard:**
- **Citation Resolution Rate**: 100.0% (Target: >95%)
- **Unsupported Claim Rate**: 0.0% (Target: 0.0%)
- **Safety Refusal Accuracy**: 100.0%
- **Insufficient Evidence Accuracy**: 100.0%

---

## Production Deployment & Security Best Practices

1. **HTTPS & TLS**: Terminate TLS at a reverse proxy (e.g. Nginx, Cloudflare, AWS ALB) with modern cipher suites.
2. **Object Storage**: For AWS S3 or MinIO, configure private buckets with short-lived pre-signed URLs. Ensure bucket versioning is enabled.
3. **Database Security**:
   - Use managed PostgreSQL with pgvector.
   - Run Alembic migrations during CI/CD release phases (`alembic upgrade head`).
   - Enable PostgreSQL connection pooling (e.g., PgBouncer).
4. **Secret Management**:
   - Generate high-entropy `SECRET_KEY` values via `openssl rand -hex 32`.
   - Never commit `.env` files into source control.
5. **Rate Limiting & Abuse Prevention**:
   - Reverse proxy rate limiting enabled for search and document upload endpoints.
   - Strict file content type and magic-byte validation (`%PDF-`) enforced.

---

## License

This project is licensed under the MIT License.
