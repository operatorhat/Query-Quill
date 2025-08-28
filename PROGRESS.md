# Project Progress Tracker

Target Delivery Date: Sept 6

This file tracks weighted completion toward the baseline deliverable.

## 1. Milestone Weights

| Category | Description | Weight |
|----------|-------------|--------|
| A | Foundation & Repo Hygiene | 8% |
| B | Data Generation Core | 25% |
| C | Persistence & Metadata | 15% |
| D | Validation & Tests | 12% |
| E | Retrieval Scaffold | 18% |
| F | Documentation Depth | 12% |
| G | Polish & Delivery | 10% |
| TOTAL |  | 100% |

### Category Detail (Subtasks)

**A. Foundation & Repo Hygiene (8%)**
- [ ] A1 README purpose + quickstart
- [ ] A2 Project goals & scope boundaries section
- [ ] A3 Repository structure explanation
- [ ] A4 Debt/risks noted (optional)

**B. Data Generation Core (25%)**
- [ ] B1 TableSpec / ColumnSpec / ForeignKey classes
- [ ] B2 get_table_specs() with ecommerce tables
- [ ] B3 generate_customers
- [ ] B4 generate_products
- [ ] B5 generate_orders
- [ ] B6 generate_order_items + compute totals
- [ ] B7 generate_payments + refund logic
- [ ] B8 Deterministic seeding (random + Faker)

**C. Persistence & Metadata (15%)**
- [ ] C1 DuckDB connection + table creation
- [ ] C2 Data insertion (all tables)
- [ ] C3 Optional CSV export (--export-csv)
- [ ] C4 schema.json basic (tables + columns + PK + FKs)
- [ ] C5 schema.json sample_values extraction
- [ ] C6 Add optional numeric stats (min/max/avg) (stretch)

**D. Validation & Tests (12%)**
- [ ] D1 FK integrity assertions
- [ ] D2 Order totals match sum(order_items)
- [ ] D3 Payments only on allowed statuses
- [ ] D4 Determinism test (same seed → same outputs aside from timestamps)
- [ ] D5 Lightweight test runner script (python tests/run_checks.py)

**E. Retrieval Scaffold (18%)**
- [ ] E1 Table doc builder (schema.json → per-table text block)
- [ ] E2 Embedding stub (returns fake vectors or placeholder)
- [ ] E3 Vector store interface (in-memory list initially)
- [ ] E4 Simple retrieval (cosine placeholder or ranking by keyword overlap)
- [ ] E5 CLI/entry script (python app/retrieval/query.py "question")

**F. Documentation Depth (12%)**
- [ ] F1 SCHEMA_DESIGN.md (tables, relationships, rationale)
- [ ] F2 ARCHITECTURE.md (problem → approach → tradeoffs → future work)
- [ ] F3 CHANGELOG.md initial + incremental updates
- [ ] F4 Inline docstrings / comments in major functions
- [ ] F5 QUESTIONS.md (open questions + answers as they resolve)

**G. Polish & Delivery (10%)**
- [ ] G1 Split generators into synthetic_data.py
- [ ] G2 CLI flags documented in README
- [ ] G3 Fresh-clone reproducibility test documented
- [ ] G4 Tag v0.1.0 release
- [ ] G5 Interview narrative cheat sheet (P-C-A-T-I-R)
- [ ] G6 Add PROGRESS.md % calculator snippet updated
- [ ] G7 Final pass for consistent commit messages

---

## 2. Current Status Summary

(Update this section daily)

Date: YYYY-MM-DD  
Completed Subtasks: (list codes)  
Blockers / Risks:  
Next Focus (top 3):  
% Complete (calculated):  

---

## 3. Percentage Calculation

Update the fraction for each category (0.0–1.0) based on the proportion of its subtasks checked.

Example snippet (save as tools/progress_calc.py or run ad-hoc):

```
weights = {"A":8,"B":25,"C":15,"D":12,"E":18,"F":12,"G":10}
# Fill with your real completion fractions
progress = {
    "A": 0.00,
    "B": 0.00,
    "C": 0.00,
    "D": 0.00,
    "E": 0.00,
    "F": 0.00,
    "G": 0.00
}
total = sum(weights[k]*progress[k] for k in weights)
print(f"Overall completion: {total:.2f}%")
```

To compute a category fraction:
\[category_fraction = completed_subtasks / total_subtasks_in_category\]

Example: If B has 8 subtasks and you finish 3: fraction = 3/8 = 0.375 → weighted = 0.375 * 25 = 9.375%.

---

## 4. Daily Reflection (Template)

Add one bullet per day; promotes retention and interview prep.

- YYYY-MM-DD:
  - What I added:
  - Key design choice & why:
  - One insight learned:
  - One question to clarify:
  - Next micro-step:

---

## 5. Risk & Mitigation Log

| Risk | Impact | Mitigation | Status |
|------|--------|------------|--------|
| Over-scoping retrieval | Delay core dataset | Lock baseline; extra features after schema.json | Open |
| Time compression near deadline | Quality dip | Daily micro-slice delivery | Open |
| Unclear explanation readiness | Weak interviews | Maintain narrative notes & daily reflection | Open |

---

## 6. Future (Post-Deadline) Ideas (Parking Lot)

- Realistic statistical distributions per category
- Parallel / vectorized generation
- Real embeddings + Chroma / FAISS integration
- Data quality report (profiling)
- API layer to query DuckDB with NL → SQL

---

## 7. Update Procedure (Checklist)

1. Implement a micro-slice (e.g., generate_products)
2. Run & verify (print sample rows / run assertions)
3. Check corresponding box(es) above
4. Recalculate % (optional script)
5. Add CHANGELOG entry
6. Add daily reflection bullet
7. Commit: feat: add products generator (refs B4)

---

## 8. Interview Narrative Cheat Sheet (Stub)

Problem: Need reproducible synthetic relational dataset + machine-readable schema for retrieval tasks.  
Approach: Declarative specs → seeded generators → DuckDB persistence → schema.json metadata → retrieval scaffold.  
Tradeoffs: Simplicity over hyper-optimization; embedded DB (DuckDB) vs external; light metadata now, extensible later.  
Improvements: Stats, constraints, richer retrieval, realistic distributions.  

(Expand when you reach G5.)

---

Keep this file close—incremental visible progress reduces anxiety and guides focus.
