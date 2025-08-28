cat > TASKS.md <<'EOF'
# Baseline Tasks (Lock until Sept 6)

## Today (Anchor)
- [ ] Slim README merged

## Core Sequence
1. Retrieval embeddings + vector store build script
2. Prompt template (few-shot) + generate_sql()
3. Guardrails (blocklist + LIMIT injection)
4. Parse + repair loop (SQLGlot)
5. Execution wrapper (DuckDB)
6. Explanation generator (baseline)
7. Metrics logger (JSONL)
8. Evaluation harness (test_cases.json + evaluate.py)
9. Final polish & submission packaging

## Notes
No new features added until baseline tasks 1–8 complete.
