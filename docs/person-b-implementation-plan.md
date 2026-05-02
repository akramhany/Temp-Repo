# Person B — Implementation Plan (Data Analyst & Insight Lead)

**Project:** GradeLens — habit-based academic performance analysis  
**Course alignment:** Phase 1 problem ("What combination of habits leads to the best academic performance?"), College Students Habits & Performance dataset, S26 BDA project deliverables (preprocessing, visualization, insights, descriptive + predictive pipeline in team doc).

**Assumptions for this plan:**

- Person A (K-Means / MapReduce) has **not** started yet.
- Person B **owns** dataset conventions, preprocessing, EDA, association rules, insights, final document assembly, and the **business** portion of the presentation.
- Work that **fully depends** on Person A is **cluster interpretation** (joining labels, profiling segments). **Business/academic insights** and **final delivery** are only **partially** dependent: you can complete strong first versions from EDA + association rules, then **revise** once clusters exist so the story covers all descriptive methods and stays consistent with Person A's technical results.

---

## Dependency note: insights and final delivery vs. Person A

| Deliverable | Without Person A | After Person A (revision pass) |
|-------------|------------------|--------------------------------|
| **Business / academic insights** | Recommendations grounded in **EDA and association rules** (habit combinations, risk patterns, "what the data suggests"). | Add **segment-level** insights (cluster personas, who is "at-risk" / "high performer" by grouping), reconcile clusters with rules/EDA, tighten the executive summary. |
| **Final document** | Draft all sections you own: preprocessing, visualization, rules, insights-from-EDA/rules; **placeholder** for clustering. | Fill clustering subsection; update insights and evaluation narrative if the team ties predictions to segments. |
| **Business presentation** | Slides on problem, data, findings from EDA/rules, preliminary recommendations. | Add **one coherent "segments" story** from K-Means; optional slide on alignment between rules and clusters. |

So: you are **not blocked** from starting insights or the doc/deck, but you should plan a **second pass** when Person A's outputs land so the "full" descriptive story and the business close match the final project.

---

## 1. Scope (what you implement)

| Area | Responsibility |
|------|----------------|
| Data pre-processing | Missing values, categorical encoding, scaling; **canonical processed dataset** for the team |
| Deep-dive EDA | Advanced visualizations, statistical summaries, "interesting questions" (e.g. sleep vs. study vs. performance) |
| Descriptive analysis | **Association rules**; **interpretation of K-Means clusters** (after Person A delivers labels) |
| Business / academic insights | Recommendations grounded in figures/rules; **extended with clusters** once Person A delivers |
| Final delivery | Lead **final document** and **business deck**; **finalize** after clustering (and team models) are in |

**Out of core scope (typically other teammates):** MapReduce K-Means implementation, predictive models, technical slides — unless your team assigns overlap.

---

## 2. Decisions you can make alone

- Dataset location, versioning (raw snapshot), and a short **data dictionary**
- **Target definition** for performance (e.g. GPA; optional bands for rule mining)
- Full **preprocessing** policy and **processed schema** (this becomes the standard Person A should follow)
- EDA questions, figure set, and naming/export conventions (`figures/`)
- Association rules: **discretization bins**, support / confidence / lift thresholds
- Final report outline for your sections and business deck structure

---

## 3. Implementation phases

Each phase has its own dedicated notebook:

| Phase | Notebook |
|-------|----------|
| 1 | `notebooks/01_data_ingestion_and_spec.ipynb` |
| 2 | `notebooks/02_preprocessing.ipynb` |
| 3 | `notebooks/03_eda.ipynb` |
| 4 | `notebooks/04_association_rules.ipynb` |
| 5 | `notebooks/05_insights.ipynb` |
| 6 | `notebooks/06_cluster_interpretation.ipynb` *(blocked on Person A)* |
| 7 | *(no notebook — document and deck assembly)* |

---

### Phase 1 — Data ingestion and analysis spec
`notebooks/01_data_ingestion_and_spec.ipynb`

1. Ingest data; keep an immutable **raw** copy.
2. Write a compact **analysis spec** as a markdown cell at the top: primary outcome, bands for basket analysis, and column roles (this doubles as a data dictionary).
3. Define the **processed schema**: final column list, dtypes, and which columns are **numeric inputs for clustering** (Person A will use this later).

**Deliverables:** `data/raw/`, `data/processed/` (or team-equivalent paths); data dictionary and analysis spec embedded in the notebook.

---

### Phase 2 — Preprocessing (team foundation)
`notebooks/02_preprocessing.ipynb`

- Handle missing values; encode categoricals; scale as needed for **distance-based** clustering later.
- Export **one canonical processed file** (e.g. CSV/Parquet).
- Document every decision as a markdown cell ("what we did and why") — this text goes directly into the final report.

**Deliverables:** Preprocessing notebook; canonical processed dataset; preprocessing narrative for the report.

---

### Phase 3 — Deep-dive EDA
`notebooks/03_eda.ipynb`

- Summary statistics, correlations, comparisons (e.g. high vs. low performance by your definition).
- Advanced plots (interactions, facets, heatmaps, habit–outcome relationships).
- **Numbered research questions**, each with a figure and a short takeaway.
- Keep a **"dead ends" cell** at the bottom logging any visualizations or comparisons that turned out uninteresting — this feeds the "Unsuccessful trials" section of the final report.

**Deliverables:** EDA notebook; exported figures (`figures/`); dead-ends log.

---

### Phase 4 — Association rules
`notebooks/04_association_rules.ipynb`

- Discretize features + outcome into **items** (e.g. sleep hours → low/medium/high); document bin edges.
- Run Apriori via `mlxtend` — no Spark needed here; the MapReduce requirement only applies to K-Means (Person A).
- Filter by support, confidence, lift; **interpret** a concise, actionable subset — not a raw dump.
- Log **thresholds you tried and rejected** for the "Unsuccessful trials" section.

**Deliverables:** Rules notebook; rules table and interpretation narrative for the report.

---

### Phase 5 — Insights (v1 now; v2 after Person A)
`notebooks/05_insights.ipynb`

- **v1:** Recommendations for **students** and **faculty/advisors**, each tied to **EDA and association rules** only.
- In the report draft, add a **placeholder**: "Cluster-based segmentation (pending Person A)."
- **v2 (after Phase 6):** Refresh the insight bullets and executive summary to include **cluster personas** and whether they agree with the strongest rules.

**Deliverables:** Insight draft (v1) in the notebook; report skeleton; short list of items that need v2 revision once clusters arrive.

---

### Phase 6 — Cluster interpretation (blocked on Person A)
`notebooks/06_cluster_interpretation.ipynb`

- Join `cluster_id` to the processed table.
- Profile clusters (central tendency, simple visuals); name segments from **data**; compare to **association rules** and EDA (agreement or tension).

**Deliverables:** Cluster interpretation notebook; short cluster narrative for doc/slides.

---

### Phase 7 — Final document and business presentation
*(no notebook — document and deck assembly)*

- **Draft early:** preprocessing, visualization, rules, insights v1, placeholders for clustering and team models (predictive analysis + evaluation sections are written by other teammates; you **assemble and format** them into the doc).
- **Course-required sections you own or lead** — see Section 7 below.
- **Finalize after:** Person A's cluster section (and team-wide evaluation numbers) so the doc and **business** slides tell one story: EDA → rules → segments → prediction, with consistent recommendations.
- Own **business** slides; coordinate handoff for technical / MapReduce content.

---

## 4. Deferred vs. parallel work

| Do now (no Person A) | After Person A delivers |
|----------------------|-------------------------|
| Pipeline, EDA, rules, **insights v1**, **doc/deck draft** | Cluster labels, **cluster interpretation**, **insights v2**, **final** doc & business slides |

---

## 5. Optional: unblock Person A without waiting

When `data/processed/` is stable, add a short **README for Person A**:

- Path to processed data
- Exact list of **feature columns** for K-Means
- Optional: suggested range for `k` from your own quick exploratory clustering (e.g. sklearn) — **for guidance only**; they still implement MapReduce.

---

## 6. Suggested defaults (change anytime)

- **Stack:** Python, pandas; association rules via `mlxtend` (no Spark needed — the course's MapReduce requirement is only for K-Means, which is Person A's job).
- **Outcome:** GPA as primary; **quartile or interpretable bands** for transactional rule mining.
- **Scaling:** StandardScaler on numeric clustering inputs; keep **interpretable** (unscaled or inverse-friendly) views for plots and prose.

---

## 7. Course deliverable checklist (final document)

The S26 project document requires these sections in the final delivery. As final doc lead, you own or coordinate every one of them:

| Section | Who writes it | Notes |
|---------|---------------|-------|
| Brief problem description | You | From Phase 1 spec |
| **Project pipeline diagram** | You | Full flow: raw data → preprocessing → EDA → rules → clustering → prediction → insights; draw this early and reuse across doc and deck |
| Data preprocessing | You | From Phase 2 notebook |
| Data visualization | You | From Phase 3 notebook |
| Extracting insights from data | You | From Phases 4 + 5 + 6 |
| Model / classifier training | Other teammates | You **assemble** into the doc |
| Results and evaluation | Other teammates | You **assemble** into the doc |
| **Unsuccessful trials** | You (EDA/rules dead-ends); others (models) | Collect dead-ends logs from every phase notebook |
| **Enhancements and future work** | You lead | Based on limitations found during EDA, rules, and interpretation |

---

## 8. Risks to manage in the write-up

- Small **n** can make association rules unstable — justify thresholds; optionally show sensitivity.
- Define **performance** clearly so rules are not circular (e.g. avoid leaking the same target twice under different names).

---

*Last updated: one notebook per phase; course deliverable checklist added; Spark ambiguity resolved.*
