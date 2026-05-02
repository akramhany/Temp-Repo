# Phase 3 Exploratory Data Analysis (EDA) Findings & Notes

During the deep-dive EDA phase (`notebooks/03_eda.ipynb`), the following observations and methodological notes were recorded based on visual and statistical analysis.

## 1. Synthetic Dataset Artifacts
The EDA process revealed distinct signatures indicating that the dataset is synthetically generated via simple linear equations:
- **Sleep Duration (Q2)**: In a real-world scenario, the relationship between sleep and GPA typically follows a parabolic bell curve (where optimal sleep is 6-8 hours, and excessive sleep correlates with lower performance). However, the EDA revealed a strict, infinite positive linear correlation (i.e., students with >8 hours of sleep have an even higher median GPA of ~1.21).
- **Action / Recommendation**: This is not an error and the column should **NOT** be dropped. It simply means the dataset was generated using a formula like `gpa = w1*study_hours + w2*sleep_hours ...`. The `sleep_hours` feature is a core mathematical driver of the target variable in this dataset and must be retained for both Association Rules and K-Means clustering.

## 2. Strong Linear Predictors
The correlation heatmap and bivariate scatter plots confirmed several features have very strong, clean linear correlations with GPA. 
**Top 5 Positive Predictors:**
1. `attendance` (r = 0.82)
2. `study_hours` (r = 0.81)
3. `class_participation` (r = 0.81)
4. `library_visits` (r = 0.78)
5. `previous_gpa` (r = 0.78)

**Top 5 Negative Predictors:**
1. `backlogs` (r = -0.79)
2. `part_time_hours` (r = -0.69)
3. `procrastination_score` (r = -0.57)
4. `caffeine_mg` (r = -0.40)
5. `junk_food_frequency` (r = -0.34)

These variables will heavily dominate the resulting clusters and association rules.

## 3. High Performers vs Low Performers
The group comparison table (top 25% vs bottom 25% GPA) effectively isolated the exact habits separating struggling students from excelling students. Unsurprisingly, the features with the largest absolute differences precisely mirrored our strongest correlations (e.g., High Performers had significantly higher attendance and study hours, while Low Performers carried heavier backlogs and more part-time hours). These isolated features will be the primary focus when designing actionable recommendations in Phase 5.

## 4. Feature Utility Confirmation
All retained features from Phase 2 showed variance and unique patterns during EDA. No further columns require dropping before moving to Association Rules mining.
