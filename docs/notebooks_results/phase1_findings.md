# Phase 1 Data Ingestion & Spec Findings

During the initial ingestion and data specification phase (`notebooks/01_data_ingestion_and_spec.ipynb`), several critical anomalies and data quality issues were discovered. These findings dictate our preprocessing strategy in Phase 2.

## 1. Single-Value Columns (Zero Variance)
- **`performance_level`**: Contains exactly one unique value (`"Low"`: 998,442 rows) and missing values (`""`: 1,558 rows). Since it lacks variance ("Medium" and "High" are completely missing), it has been discarded. 
  - *Action:* Marked as `drop` in the schema contract.

## 2. Perfectly Correlated Columns (Multicollinearity)
Several features were found to have a Pearson correlation of exactly `1.000000`, meaning they contain identical data. Keeping them would skew the clustering and association rules.
- **`self_discipline` & `time_management`**: Perfectly correlated.
  - *Action:* Retained `time_management`, marked `self_discipline` to `drop`.
- **`gaming_hours`, `netflix_hours`, & `late_night_frequency`**: All three are perfectly correlated.
  - *Action:* Retained `gaming_hours`, marked `netflix_hours` and `late_night_frequency` to `drop`.

## 3. Derived Columns
- **`screen_time`**: Confirmed to be exactly the sum of `social_media_hours`, `gaming_hours`, and `netflix_hours` (Max difference from sum: `~2.4e-06`).
  - *Action:* Marked to `drop` to avoid multicollinearity, as its underlying components provide more granular insight.

## 4. Data Leakage Risks
- **`previous_gpa`**: While chronologically valid, including this feature in habit-mining or predictive models acts as a massive confounder and overshadows actual lifestyle habits.
  - *Action:* Retained in the dataset but flagged to be explicitly **dropped during Phase 4 (Association Rules)** to prevent trivial rules like "High previous GPA implies High current GPA."
- **`midterm_score`, `final_score`, `project_score`**: These directly make up the `gpa` target variable.
  - *Action:* Retained in dataset but flagged to be explicitly **dropped during Phase 4**.

## 5. Scale Inconsistencies
- **`gpa` vs `previous_gpa`**: `gpa` ranges from roughly 0.0-2.0, while `previous_gpa` ranges from 0.0-10.0.
  - *Action:* `previous_gpa` will be normalized to a 0-1 scale during Phase 2 preprocessing to align with the rest of the dataset.

---
*These findings have been codified into the Team Schema Contract to ensure consistency across Person A and Person B's workflows.*
