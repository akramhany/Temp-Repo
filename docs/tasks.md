# GradeLens — Person B Task List (Phases 1–5)

Checkboxes track progress. Each phase maps to one notebook. Tasks are ordered to be executed top-to-bottom within each phase.

---

## Phase 1 — Data Ingestion and Analysis Spec
`notebooks/01_data_ingestion_and_spec.ipynb`

### Setup
- [x] Create the folder structure: `data/raw/`, `data/processed/`, `figures/`, `notebooks/`
- [x] Download the dataset (College Students Habits & Performance) and save it unchanged in `data/raw/`
- [x] Pin the dataset source URL in a markdown cell at the top of the notebook

### Data dictionary
- [x] Load the raw CSV/Excel and print `.shape`, `.dtypes`, `.head()`
- [x] For every column, document in a markdown table: column name, data type (original), meaning, and expected value range or categories
- [x] Flag any columns that look redundant, anonymized, or low-signal at first glance

### Analysis spec (markdown cell)
- [x] Define the **primary outcome**: choose GPA (continuous) as the main target
- [x] Define **performance bands** for association rules (e.g. quartile-based: low / medium-low / medium-high / high) and document the exact cut points
- [x] Assign a **role** to every column: `target`, `numeric_feature`, `categorical_feature`, or `drop`
- [x] List which numeric columns are **clustering inputs** for Person A (all `numeric_feature` columns after cleaning)

### Processed schema contract
- [x] Write out the expected processed schema as a table: column name → final dtype → encoding plan (e.g. one-hot, ordinal, passthrough, drop)
- [x] Save this schema as a standalone markdown section titled "Processed schema — team contract" so Person A can find it immediately

### Deliverable check
- [x] `data/raw/` contains the unmodified source file(s)
- [x] Data dictionary covers all columns
- [x] Analysis spec is complete (outcome, bands, roles, clustering inputs)
- [x] Processed schema contract is written and clearly labelled
- [x] Document Phase 1 data anomalies and findings in `docs/notebooks_results/phase1_findings.md`

---

## Phase 2 — Preprocessing
`notebooks/02_preprocessing.ipynb`

### Missing values
- [x] Compute missing-value counts and percentages per column; display as a sorted table
- [x] Decide and document strategy per column:
  - Drop column if > 40% missing (or other justified threshold — state it)
  - Drop row if isolated and < 1% of data
  - Impute numeric with **median** (state why median over mean for skewed data)
  - Impute categorical with **mode** or a dedicated `"Unknown"` category (state choice)
- [x] Apply the strategy; verify no nulls remain in retained columns
- [x] Write a markdown cell summarising every imputation decision made

### Categorical encoding
- [x] Identify all `categorical_feature` columns from the Phase 1 schema
- [x] Apply **one-hot encoding** to nominal columns (no inherent order); drop the first dummy to avoid multicollinearity
- [x] Apply **ordinal encoding** to ordered columns only (e.g. education level, frequency scales); document the explicit order used
- [x] Verify no original string columns remain in the output

### Scaling
- [x] Apply **StandardScaler** to all numeric clustering inputs (zero mean, unit variance) and save these as a separate column set (e.g. suffix `_scaled`) or a separate file so that unscaled values remain available for EDA plots and prose interpretation
- [x] Do **not** scale the target (GPA) or the performance bands

### Feature review
- [x] Drop any column marked `drop` in the Phase 1 schema; document why each was dropped
- [x] Drop the `performance_level` column since it only contains a single unique value ("Low") across the entire dataset
- [x] Check for near-zero variance columns (std < 0.01 or similar threshold); drop or flag them
- [x] Check for duplicate rows; drop if found; log the count

### Export
- [x] Export the cleaned, encoded, unscaled dataset to `data/processed/students_clean.csv`
- [x] Export the cleaned, encoded, scaled (clustering inputs only) dataset to `data/processed/students_scaled.csv`
- [x] Print final `.shape` and confirm no nulls in either export

### Deliverable check
- [x] Every missing-value decision has a markdown explanation
- [x] Every encoding choice has a markdown explanation
- [x] Both processed files are exported and verified null-free
- [x] A summary markdown cell at the end lists: rows before → after, columns before → after, changes made
- [x] Document Phase 2 data anomalies and preprocessing notes in `docs/notebooks_results/phase2_findings.md`

---

## Phase 3 — Deep-Dive EDA
`notebooks/03_eda.ipynb`

### Univariate analysis
- [x] Plot distribution of the target (GPA): histogram + KDE; note skew
- [x] Plot distribution of every numeric feature: histograms in a grid layout
- [x] Plot value counts for every categorical feature: bar charts in a grid layout
- [x] Summarise: `.describe()` for numerics; mode and frequency table for categoricals

### Bivariate analysis — habit vs. performance
Answer each as a **numbered research question** with one primary figure and a one-paragraph takeaway:

- [x] **Q1:** Does more study time correlate with higher GPA? — scatter plot + regression line + Pearson r
- [x] **Q2:** Does sleep duration affect GPA? — box plots of GPA by sleep band; median comparison table
- [x] **Q3:** Does class attendance rate affect GPA? — scatter + regression line; note ceiling effect if any
- [x] **Q4:** Do students with part-time jobs perform differently? — side-by-side box plots; note sample sizes
- [x] **Q5:** Does social media / screen time relate to lower GPA? — scatter or box plot; annotate any non-linear trend
- [x] **Q6:** Does physical activity relate to GPA? — box plots by activity level
- [x] **Q7:** Does diet or nutrition quality relate to GPA? (if column exists) — bar chart of median GPA by diet category
- [x] **Q8:** Any demographic differences in GPA? (gender, age group, etc. if available) — grouped box plots; include sample sizes

> Add or remove questions based on the actual columns present. Every question must have a figure + takeaway paragraph.

### Multivariate analysis
- [x] **Correlation heatmap:** full Pearson correlation matrix of all numeric features + GPA; annotate top 5 positive and top 5 negative correlations with GPA
- [x] **Interaction plot:** study hours × sleep hours coloured by GPA band — scatter with colour encoding
- [x] **Pairplot** (subset): top 4–5 features most correlated with GPA; include GPA on diagonal
- [x] **Faceted comparison:** pick 2 categorical habits (e.g. job status × sleep band); show GPA distribution per combination

### Group comparison
- [x] Split students into **high performers** (top 25% GPA) vs. **low performers** (bottom 25%); compute mean/median of every feature for each group; display as a side-by-side summary table
- [x] Highlight the features where the two groups differ most (absolute difference > 0.5 std or similar justified threshold)

### Dead-ends log (mandatory section at bottom of notebook)
- [x] Create a markdown cell titled `## Dead ends` listing every visualisation or comparison attempted that revealed nothing meaningful, with a one-line note on why it was discarded — this is the raw material for the "Unsuccessful trials" section of the final report

### Export figures
- [x] Save every primary figure to `figures/eda_q1_study_vs_gpa.png`, `figures/eda_corr_heatmap.png`, etc. (consistent naming: `eda_<short_description>.png`)

### Deliverable check
- [x] At least 8 numbered research questions answered with figure + takeaway
- [x] Correlation heatmap present
- [x] Interaction / multivariate plots present
- [x] High vs. low performer comparison table present
- [x] Dead-ends log written
- [x] All key figures exported to `figures/`
- [x] Document Phase 3 data anomalies and EDA notes in `docs/notebooks_results/phase3_findings.md`

---

## Phase 4 — Association Rules
`notebooks/04_association_rules.ipynb`

### Discretisation
- [ ] Load `data/processed/students_clean.csv` (unscaled)
- [ ] **Drop** non-habit outcome columns (`previous_gpa`, `midterm_score`, `final_score`, `project_score`) before building the transaction matrix to prevent data leakage and trivial rules
- [ ] For each continuous numeric feature, create a binned version:
  - Use the **performance bands** defined in Phase 1 (quartile-based) for GPA
  - Use **equal-frequency binning (quantiles)** or **domain-justified cut points** for habit columns (e.g. sleep: < 6h = low, 6–8h = medium, > 8h = high); document cut points for every column
- [ ] Rename binned columns to human-readable item labels (e.g. `sleep_low`, `sleep_medium`, `sleep_high`)
- [ ] One-hot encode the binned columns into a **transaction matrix** (rows = students, columns = items, values = 0/1)
- [ ] Verify: each row should have exactly one active bin per original feature (mutual exclusivity check)

### Mining
- [ ] Run `mlxtend` **Apriori** with initial thresholds: `min_support = 0.15`, `min_confidence = 0.6`
- [ ] Generate association rules; compute support, confidence, and **lift** for all rules
- [ ] Record total number of rules generated at these thresholds

### Threshold experimentation (log for unsuccessful trials)
- [ ] Try at least 2 other support thresholds (e.g. 0.10 and 0.20); record rule counts and quality observations
- [ ] Log each attempt in a markdown cell: thresholds tried → number of rules → why accepted or rejected
- [ ] Choose and document the **final thresholds** with justification

### Filtering and selection
- [ ] Filter rules to **lift > 1.2** (or justified minimum) to exclude near-random patterns
- [ ] Filter rules where the **consequent is a GPA band** (performance-outcome rules) — these are the most actionable
- [ ] Sort the filtered rules by lift descending; select **top 10–15** for detailed interpretation
- [ ] Also extract top 5 rules where the **antecedent is a GPA band** (what habits do high/low performers share) — for cross-check

### Interpretation
- [ ] For each selected rule, write a plain-English sentence: "Students who [habits] tend to [outcome] — support X%, confidence Y%, lift Z"
- [ ] Group rules into themes (e.g. "Sleep + study combinations", "Risk factors for low GPA", "Protective habits")
- [ ] Note any rules that **contradict EDA findings** and explain the discrepancy
- [ ] Note any **surprising or counterintuitive rules** — these are high-value report material

### Deliverable check
- [ ] Discretisation documented with explicit cut points for every column
- [ ] Final thresholds chosen and justified
- [ ] Threshold experimentation log written (for unsuccessful trials section)
- [ ] Top 10–15 filtered, interpreted rules with plain-English explanations
- [ ] Rules grouped into themes
- [ ] Any contradictions or surprises with EDA noted

---

## Phase 5 — Insights v1 (EDA + Rules)
`notebooks/05_insights.ipynb`

### Structure
- [ ] Open the notebook with a markdown cell stating: "This is insights v1 — grounded in EDA and association rules only. Cluster-based insights (v2) will be added after Person A delivers K-Means labels."

### Student recommendations
Write one recommendation per finding; each must cite the **specific figure or rule** that supports it:

- [ ] **Study time:** recommended minimum hours based on Q1 findings and rules; note diminishing returns if visible
- [ ] **Sleep:** recommended range and risk of both extremes (too little and too much if detectable)
- [ ] **Attendance:** quantify the attendance level above which GPA stabilises (from Q3 scatter)
- [ ] **Part-time work:** flag if holding a job is correlated with lower GPA; acknowledge confounders (financial need, time management)
- [ ] **Screen time / social media:** flag if high screen time appears in low-GPA rules; suggest practical limits
- [ ] **Physical activity:** include if the EDA showed a meaningful signal
- [ ] Add or remove recommendations based on what the data actually supports — do not invent recommendations with no evidence

### Faculty / advisor recommendations
- [ ] Identify the **top 2–3 early-warning signals** (habit combinations most associated with low GPA from rules) and recommend monitoring these
- [ ] Suggest which student segments to proactively engage (based on rule antecedents)
- [ ] Note limitations: observational data, no causal claims

### Cross-check
- [ ] Verify that every recommendation is supported by **at least one** of: an EDA figure, a research question takeaway, or an association rule
- [ ] Flag any EDA finding that did **not** produce a clear actionable recommendation (acknowledge the gap)

### Placeholder for v2
- [ ] Add a markdown section titled `## Cluster-based segmentation (pending Person A)` with the following subsection headings left blank:
  - `### Cluster profiles`
  - `### Segment-level recommendations`
  - `### Consistency check: clusters vs. rules`
  - `### Executive summary update`

### v2 revision list
- [ ] Add a final markdown cell titled `## Items to revisit in v2` listing every insight that may change once cluster labels are available (e.g. "Check if the 'low sleep + low study' rule maps to a single cluster or spans multiple")

### Deliverable check
- [ ] At least 5 student recommendations, each citing a specific figure or rule
- [ ] At least 2 faculty/advisor recommendations with early-warning signals named
- [ ] Limitations section written
- [ ] Every recommendation traceable to evidence
- [ ] v2 placeholder section with all four subsection headings present
- [ ] v2 revision list written

---

*Phases 6 and 7 are tracked separately; Phase 6 is blocked on Person A's K-Means output.*
