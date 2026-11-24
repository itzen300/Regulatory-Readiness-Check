# Regulatory Readiness Check

Blue print for the working mechanism will be like the following ;
```text
Regulatory-Readiness-Check/
├── .github/
│   └── workflows/
│       └── gitleaks.yml                 # [1] Secret scanning CI gate on every push/PR
├── .gitignore                           # [2] Excludes .env, venv, node_modules, __pycache__
├── .env.example                         # [3] Template for DB credentials (safe to commit)
├── docker-compose.yml                   # [4] Spins up PostgreSQL (with pgvector image) locally
│
├── regulatory-data/                     # Versioned primary source data & seed files
│   ├── act/
│   │   └── dpdp_act_2023.json           # [5] Structured sections, effective dates, source URLs
│   ├── rules/
│   │   └── dpdp_rules_2025.json         # [6] Notified rules mapped to Act sections & dates
│   └── control-mappings/
│       └── baseline_controls.json       # [7] DP-001, DP-002, DP-003 mapped to legal provisions
│
├── backend/                             # FastAPI + SQLAlchemy + Alembic
│   ├── requirements.txt                 # [8] fastapi, uvicorn, sqlalchemy, psycopg2-binary, alembic, pydantic-settings
│   ├── alembic.ini                      # [9] Database migration configuration
│   ├── alembic/
│   │   └── versions/
│   │       └── 001_init_knowledge.py    # [10] Migration creating regulatory_documents & compliance_controls tables
│   └── app/
│       ├── main.py                      # [11] FastAPI entry point, CORS middleware, router mounting
│       ├── core/
│       │   ├── config.py                # [12] Loads DATABASE_URL & app settings via Pydantic
│       │   └── database.py              # [13] SQLAlchemy engine & get_db session dependency
│       ├── models/
│       │   └── knowledge.py             # [14] SQLAlchemy ORM models (RegulatoryDocument, ComplianceControl)
│       ├── schemas/
│       │   └── knowledge.py             # [15] Pydantic request/response schemas (validation & serialization)
│       ├── api/
│       │   └── v1/
│       │       └── knowledge.py         # [16] GET /documents, GET /controls, GET /search endpoints
│       └── scripts/
│           └── seed_knowledge.py        # [17] Idempotent CLI script to load JSON files into PostgreSQL
│
└── frontend/                            # React + TypeScript (Vite)
    ├── package.json                     # [18] React, TypeScript, Tailwind CSS, Lucide icons, Axios
    └── src/
        ├── types/
        │   └── knowledge.ts             # [19] TypeScript interfaces matching backend Pydantic schemas
        ├── services/
        │   └── knowledgeApi.ts          # [20] API client functions calling FastAPI endpoints
        ├── components/
        │   └── DocumentCard.tsx         # [21] Displays section, plain-English summary, & effective date badge
        └── pages/
            └── KnowledgeCentre.tsx      # [22] Main searchable library with category tabs & legal filters
```
