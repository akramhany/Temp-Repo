# Phase 2 Preprocessing Findings & Notes

During the data preprocessing phase (`notebooks/02_preprocessing.ipynb`), the following observations and methodological decisions were implemented:

## 1. Scale Normalization Correction
- **`previous_gpa` vs `gpa`**: During execution, we verified that the maximum value of the target `gpa` column was approximately `2.009` (a 0-2 scale system). `previous_gpa` was on a 0-10 scale. 
  - *Action*: Instead of dividing by 10 (which was the initial assumption), `previous_gpa` was divided by `5.0` to map it accurately to the 0-2 range.

## 2. Inherent Encoding of Categoricals
- **Nominal & Ordinal Features**: The task list required one-hot encoding for nominal columns to avoid ordinal assumptions. However, upon reviewing the `categorical_feature` columns (`peer_study_group`, `relationship_status`, `hostel_student`), all were inherently binary (values `0` and `1`). Additionally, `parental_education_level` was already encoded linearly (`1` to `5`).
  - *Action*: No `pd.get_dummies()` transformations were necessary. We simply asserted the types as `int8` to optimize memory.

## 3. Imputation Strategy
- Missing continuous numeric features were successfully imputed with the **median**.
- Missing categorical features were imputed using the **mode**.
- Rows with a missing `gpa` target variable were dropped, as imputing the target variable invalidates predictive modeling.

## 4. Scaling (Z-Score Normalization)
- `StandardScaler` was applied to all numeric features destined for K-Means clustering. 
- *Observation*: The resulting scaled features contain negative values. This is statistically correct (Z-score normalization), representing values below the global mean, and centers the data symmetrically around `0.0` with a standard deviation of `1.0`.

## 5. Export Streams
Two parallel streams were produced:
1. `students_clean.csv`: Missing values handled, redundant columns dropped, and scales aligned. To be used for EDA and Association Rules.
2. `students_scaled.csv`: Identical to the clean dataset, but with continuous numeric features normalized using `StandardScaler`. To be handed off to Person A for MapReduce K-Means clustering.
