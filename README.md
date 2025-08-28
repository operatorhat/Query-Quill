<<<<<<< HEAD
# Query-Quill
=======
# Query Quill (SQL Query Buddy)

Natural language → trustworthy, optimized SQL + explanation + optimization hints — via a FastAPI backend with a local (Ollama) LLM core.

Built for the GenAI & Agents Bootcamp Contest (Submission: Sept 6, 2025). Focus: pragmatic Retrieval‑Augmented Generation (RAG) over database schema metadata, safe generation, validation & iterative improvement.

---
## 🚀 Elevator Pitch
Analysts and stakeholders lose time crafting repetitive SQL and debugging joins. Query Quill converts:

“Top 5 customers by total revenue in 2024 excluding refunds”

into:
- Executable SQL (validated; auto repair if broken)
- Plain-English explanation
- Optimization suggestions (heuristics + optional EXPLAIN)
- Clarifying question when intent / table mapping is ambiguous

Served via clean REST endpoints so any UI (web, CLI, notebook) can integrate.

---
## ✅ Core Features (MVP)
- Natural language → multi-table SQL (schema aware)
- Embedding-based schema retrieval (Chroma + local embeddings)
- Confidence heuristic + clarifying question flow
- SQL validation + repair loop (SQLGlot)
- Sandbox execution (DuckDB; read-only synthetic data)
- Explanation generation
- Optimization heuristics (AST + rules)
- Guardrails (block destructive statements, inject LIMIT)

### 🎯 Stretch (Post-MVP)
- Query history + diff
- Metrics dashboard (latency, success, backend choice)
- Token / cost tracking (if OpenAI fallback used)
- Conversational refinement (multi-turn)
- Semantic caching (question → SQL)
- Multi‑DB adapters (Postgres, MySQL)
- Authentication & rate limiting
- Hybrid retrieval (keyword + embeddings)

---
## 🧱 Architecture Overview
```
Client (CLI / simple JS / future UI)
      |
      v
FastAPI (main.py)
      |
      +-- Retrieval (Chroma + embeddings)
      +-- Prompt Assembly
      +-- LLM (Ollama local model; optional OpenAI fallback)
      +-- Validation & Repair (SQLGlot + guardrails)
      +-- Execution (DuckDB sandbox)
      +-- Explanation & Optimization
      +-- Metrics Logger (JSONL / future SQLite)
```

Mermaid Diagram:
```mermaid
flowchart LR
  A[User Question] --> B[Retrieval (Chroma)]
  B --> C[Prompt Assembly]
  C --> D[LLM SQL Generation]
  D --> E[Validation & Repair]
  E --> F[Execution (DuckDB)]
  F --> G[Explain & Optimize]
  G --> H[API Response JSON]
  E -->|Parse Error| C
```

---
## 🛠 Tech Stack
- Backend: FastAPI
- Language: Python 3.11+
- Vector Store: Chroma
- Embeddings (default): sentence-transformers (all-MiniLM-L6-v2) or Ollama nomic-embed-text
- LLM (default): Ollama (e.g., llama3:8b / mistral:7b)
- Optional Fallback: OpenAI GPT model (USE_OPENAI=1)
- DB Sandbox: DuckDB
- SQL Parsing & AST: SQLGlot
- Synthetic Data: Faker
- Config: pydantic BaseSettings + .env

LLM Backends:
- Local Primary: Ollama (privacy, zero marginal cost)
- Remote Optional: OpenAI (accuracy benchmark / fallback)

Embeddings:
- Local: sentence-transformers all-MiniLM-L6-v2 (fast CPU)
- Alt: bge-small-en / nomic-embed-text (better semantic fidelity)

---
## 📂 Repository Structure (Planned / In Progress)
```
query-quill/
  ├─ main.py
  ├─ app/
  │   ├─ core/
  │   │   ├─ config.py
  │   │   └─ logging.py
  │   ├─ schema/
  │   │   ├─ generate_schema.py
  │   │   ├─ schema.json
  │   │   └─ synthetic_data.py
  │   ├─ retrieval/
  │   │   ├─ embeddings.py
  │   │   ├─ vector_store.py
  │   │   └─ retriever.py
  │   ├─ generation/
  │   │   ├─ prompts.py
  │   │   ├─ llm.py
  │   │   ├─ sql_generator.py
  │   │   └─ repair.py
  │   ├─ execution/
  │   │   ├─ sandbox.py
  │   │   └─ validator.py
  │   ├─ optimization/
  │   │   ├─ explain.py
  │   │   └─ heuristics.py
  │   ├─ eval/
  │   │   ├─ test_cases.json
  │   │   ├─ evaluate.py
  │   │   └─ metrics.py
  │   └─ api/
  │       ├─ models.py
  │       ├─ routes_query.py
  │       └─ routes_meta.py
  ├─ TASKS.md
  ├─ FUTURE.md
  ├─ CHANGELOG.md
  ├─ README.md
  ├─ requirements.txt
  └─ .env.example
```

---
## 📘 Problem Definition
Non-expert users struggle to:
- Map intent → correct tables & joins
- Avoid syntax / semantic errors
- Interpret what a query actually returns
- Optimize performance (avoid wasteful scans)
- Iterate rapidly

Query Quill compresses that cycle with retrieval + validation + explanation in one request.

---
## 👥 Target Users
- Product / business analysts
- PMs / support leads needing one-off insights
- Engineers onboarding to new schemas
- Internal tooling / enablement teams embedding NL → SQL

---
## 🗄 Synthetic Schema (Initial Domain)
Tables:
- customers(id, name, region, created_at)
- products(id, name, category, price)
- orders(id, customer_id, order_date, status, total_amount)
- order_items(id, order_id, product_id, quantity, unit_price)
- payments(id, order_id, method, amount, paid_at, refunded)

Relationships:
- customers 1—* orders
- orders 1—* order_items
- products 1—* order_items
- orders 1—* payments

schema.json includes: table, description, columns (name, type, samples), foreign_keys.

---
## 🔍 Retrieval Strategy
- One document per table (optionally column-level later)
- Text content: table name, description, columns, relationships
- Embed documents → store in Chroma
- Query → cosine similarity → top_k candidate tables
- Confidence heuristic (avg similarity); below threshold → clarification path
- (Later) Hybrid score: 0.7 * embedding + 0.3 * keyword overlap

---
## 🧪 SQL Generation Flow
1. Retrieve candidate tables
2. Assemble prompt (schema snippet + strict rules)
3. Generate SQL (Ollama model)
4. Validate parse (SQLGlot)
   - On failure → repair prompt with exact parse error
   - Block destructive statements
   - Inject LIMIT (if large potential result and absent)
5. Execute (DuckDB sandbox)
6. Explanation + optimization tips
7. Return structured JSON

---
## 🛡 Guardrails
- Blocklist: UPDATE / DELETE / DROP / ALTER / TRUNCATE / INSERT
- LIMIT injection
- Execution timeout
- Sanitized synthetic dataset (no PII)
- (Later) Rate limiting + auth token

---
## 🧠 Explanation & Optimization
Explanation: ≤120 words, plain language.

Heuristics (initial):
- SELECT * → suggest explicit columns
- Unused joined tables
- Missing filters implied by query (time windows, “top N”)
- Candidate index suggestions (JOIN + WHERE columns)
- Encourage explicit LIMIT for exploratory queries

(Later) EXPLAIN analysis → highlight full scans & join order.

---
## 📏 Metrics & Evaluation
Logged per request:
- retrieval_tables
- retrieval_confidence
- parse_success
- execution_success
- backend_used (ollama | openai)
- latency_ms
- clarification_triggered

Test harness (eval/):
- test_cases.json with natural_language + (optional) expected_sql
- Structural comparison via SQLGlot normalization

Stretch:
- Ragas / TruLens
- Result set correctness validators
- Token usage (if OpenAI fallback)

---
## 🔄 API Endpoints (Planned)
```
POST /query
  Request: { "question": "Top 5 customers by revenue in 2024" }
  Response:
    {
      "sql": "...",
      "results": [...],
      "explanation": "...",
      "optimizations": ["..."],
      "clarification_needed": false,
      "retrieval": { "tables": ["customers","orders","payments"], "confidence": 0.82 },
      "metrics": { "latency_ms": 412, "parse_repaired": false, "backend": "ollama" }
    }

POST /clarify
  Request: { "question": "...", "clarification_answer": "Use payment amounts" }

GET /schema
  -> schema.json

GET /health
  -> { status: "ok" }
```

---
## 🧪 Example (Placeholder)
User:
Top 5 customers by total revenue in 2024 excluding refunded payments.

Generated SQL (v0 baseline):
```
SELECT c.name, SUM(p.amount) AS total_revenue
FROM customers c
JOIN orders o ON o.customer_id = c.id
JOIN payments p ON p.order_id = o.id
WHERE strftime('%Y', o.order_date) = '2024'
  AND (p.refunded = 0 OR p.refunded IS NULL)
GROUP BY c.name
ORDER BY total_revenue DESC
LIMIT 5;
```

---
## 🗺 Roadmap & Checkpoints
- [ ] Phase 0: Scope lock
- [ ] Phase 1: Schema + synthetic data + schema.json
- [ ] Phase 2: Retrieval (Chroma) + confidence + clarifier
- [ ] Phase 3: SQL generation + repair loop
- [ ] Phase 4: Explanation + optimization heuristics
- [ ] Phase 5: Evaluation harness + metrics logging
- [ ] Phase 6: API polish & minimal docs
- [ ] Phase 7: Guardrails hardening
- [ ] Phase 8: Demo assets + LinkedIn post
- [ ] Phase 9: Stretch features

---
## 🔧 Quick Start

1. Clone & install
```
git clone https://github.com/operatorhat/Query-Quill.git
cd Query-Quill
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

2. Install & pull Ollama models
```
# Install Ollama (macOS/Linux)
curl -fsSL https://ollama.com/install.sh | sh

# Pull default generation model
ollama pull llama3:8b

# (Optional) Embedding model
ollama pull nomic-embed-text
```

3. Environment (.env)
```
MODEL_NAME=llama3:8b
EMBEDDING_BACKEND=sentence-transformers
EMBEDDING_MODEL=all-MiniLM-L6-v2
OLLAMA_HOST=http://localhost:11434
USE_OPENAI=0
# If using OpenAI fallback:
# OPENAI_API_KEY=sk-...
# OPENAI_MODEL=gpt-4o-mini
```

4. Generate synthetic DB + schema
```
python app/schema/generate_schema.py
```

5. Build vector store
```
python app/retrieval/vector_store.py
```

6. Run API
```
uvicorn main:app --reload
```

7. Test query (HTTP)
```
curl -X POST http://127.0.0.1:8000/query \
     -H "Content-Type: application/json" \
     -d '{"question":"Top 5 customers by total revenue in 2024 excluding refunds"}'
```

---
## 📦 requirements.txt (Initial)
```
fastapi
uvicorn
duckdb
chromadb
sqlglot
faker
python-dotenv
langchain
pydantic
sentence-transformers
requests
tiktoken          # optional; remove later if not using OpenAI
# openai           # only if fallback enabled
```

---
## 🗂 Sample main.py Skeleton
```
from fastapi import FastAPI
from app.api.routes_query import router as query_router
from app.api.routes_meta import router as meta_router

app = FastAPI(title="Query Quill")

@app.get("/health")
def health():
    return {"status": "ok"}

app.include_router(query_router)
app.include_router(meta_router)
```

---
## 🔄 Design Principles
- Vertical slices early (end-to-end before broad abstraction)
- Instrument from day one (latency, backend, parse success)
- Deterministic improvements (small measurable changes)
- Strict schema adherence (no invented columns)
- Modular swap: retrieval / LLM / embeddings replaceable

---
## 🧩 Future Enhancements
- Multi-turn conversational memory
- Adaptive few-shot examples
- Hybrid retrieval (keyword + vector)
- Index usage analysis
- Local fallback ensemble (model voting)

---
## 🤝 Contributing
Contest-phase: focused solo development. Issues / PRs welcome post-MVP.

---
## 📜 License
MIT (tentative)

---
## 🙌 Attribution
- LangChain & Chroma community
- SQLGlot project
- DuckDB team
- Research inspiration: PICARD, RAT-SQL (reference only)

---
## 🔄 Status
MVP Build In Progress — see CHANGELOG.md for iteration notes.

---
## 📝 Immediate TODO 
- [ ] generate_schema.py
- [ ] schema.json produced
- [ ] embeddings + vector_store pipeline
- [ ] retrieval confidence heuristic
- [ ] sql_generator + strict prompt
- [ ] validator + guardrails
- [ ] repair loop
- [ ] /query endpoint returns structured JSON
- [ ] first successful end-to-end query
- [ ] metrics logging (JSONL)

---
(c) 2025 Query Quill

>>>>>>> 2b2a760 (docs: update README)
