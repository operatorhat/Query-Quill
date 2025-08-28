cat > README.md <<'EOF'
# Query Quill (SQL Query Buddy)

Natural language → safe, schema‑aware SQL + concise explanation. Focus: a dependable baseline by 2025‑09‑06 (contest submission).

Status: MVP baseline in progress (Day -9)

## Problem → Outcome
Analysts lose time mapping intent to correct tables, fixing syntax, and explaining results. Query Quill compresses that loop: NL question → validated SQL → execution → explanation + basic quality heuristics in one call.

## Baseline Scope (IN by Sept 6)
- NL → SQL over synthetic ecommerce schema (customers, orders, order_items, products, payments)
- Table-level retrieval (embedding similarity) with confidence score
- Guardrails: block destructive verbs, force LIMIT when absent & large, parse + simple repair loop (SQLGlot)
- Execution: DuckDB sandbox (read-only synthetic data)
- Explanation (≤120 words); basic optimization hints (e.g., SELECT * warning)
- Metrics logging (JSONL): nl_query, tables, confidence, parse_success, exec_success, latency_ms, repaired
- Evaluation harness: run test_cases.json and print pass stats
- FastAPI endpoints: /query /schema /health

## Non-Goals (Deferred)
Multi-turn conversation, advanced EXPLAIN-driven optimization, multi-DB adapters, auth/rate limiting, semantic cache, history diff, hybrid retrieval weighting, multi-turn clarifiers beyond a single flag.

## Quick Start
1. Clone & install
   git clone https://github.com/operatorhat/Query-Quill.git
   cd Query-Quill
   python -m venv .venv && source .venv/bin/activate
   pip install -r requirements.txt
2. Generate synthetic schema + data
   python app/schema/generate_schema.py
3. Build vector store
   python app/retrieval/vector_store.py
4. Run API
   uvicorn main:app --reload
5. Test query
   curl -X POST http://127.0.0.1:8000/query \
        -H "Content-Type: application/json" \
        -d '{"question":"Top 5 customers by total revenue in 2024 excluding refunds"}'

## Current Capabilities
- [x] Synthetic schema + data
- [ ] Retrieval embeddings + vector store
- [ ] NL → SQL generation
- [ ] Guardrails + repair
- [ ] Execution sandbox
- [ ] Explanation
- [ ] Metrics logging
- [ ] Evaluation harness

## Architecture (Baseline Flow)
User Question → Retrieve candidate tables (embeddings) → Assemble prompt → LLM SQL draft → Parse & Guardrails → (Repair if needed) → Execute (DuckDB) → Explanation + hints → JSON response + metrics log.

## Usage (Endpoint Sketch)
POST /query
Request:
{
  "question": "Top 5 customers by total revenue in 2024 excluding refunds"
}
Response (fields may expand minimally):
{
  "sql": "...",
  "results": [...],
  "explanation": "...",
  "optimizations": ["SELECT * replaced with explicit columns"],
  "retrieval": { "tables": ["customers","orders","payments"], "confidence": 0.82 },
  "metrics": { "latency_ms": 412, "parse_repaired": false }
}

## Evaluation
Populate test_cases.json (nl, expected_sql_fragment?). Run:
python app/eval/evaluate.py
Output: pass_count / total + list of failures (retrieval miss | parse fail | execution fail).

## Limitations
- No multi-turn refinement yet
- Limited optimization hints (heuristic only; no EXPLAIN integration)
- Retrieval at table granularity (column-level recall later)
- No auth or rate limiting
- Single local model path (Ollama) primary; OpenAI fallback optional

## Roadmap (Locked to Baseline)
1. Schema + synthetic data
2. Retrieval + confidence
3. NL → SQL + guardrails + repair
4. Execution + explanation + hints
5. Metrics logging + evaluation harness
6. Polish & submission packaging

(Future phases moved to FUTURE.md)

## Design Principles (Trimmed)
- Vertical slice first, refine later
- Measurable iteration (eval harness governs changes)
- Safety > feature breadth

## License
MIT (tentative)

## Attribution
DuckDB • SQLGlot • LangChain • Chroma • Related research: PICARD, RAT-SQL (conceptual)
