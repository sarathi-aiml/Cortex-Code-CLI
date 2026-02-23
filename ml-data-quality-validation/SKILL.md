---
name: ml-data-quality-validation
description: >
  Run deep, model-aware data quality checks on Snowflake tables before feature 
  engineering and ML training. Produces a comprehensive report with TLDR verdict 
  (READY/NEEDS ATTENTION/BLOCKED), pros/cons by table, and recommended fixes.
  Trigger phrases: "check ML data quality", "validate data before model training",
  "run pre-training data quality checks", "ML DQ gate", "validate features before training"
allowed-tools: "*"
---

# ML Data Quality Validation Skill

**When to invoke:**  
Use this skill **BEFORE** any of the following:
- Creating feature sets or feature stores
- Running feature engineering pipelines  
- Training ML models (Snowpark ML, external training, etc.)

**Trigger phrases:**
- "check ML data quality"
- "validate data before model training"
- "run pre-training data quality checks"
- "ML DQ gate"
- "validate features before training"
- "$ml-data-quality-validation"

---

## Your Role

You are a **Senior ML Data Quality Engineer**. Your job is to:
1. Analyze Snowflake tables that will feed feature engineering and model training
2. Perform model-aware data quality checks tailored to the user's ML objective
3. Produce a concise TLDR verdict plus detailed report with pros/cons and remediation steps

---

## Workflow Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ML Data Quality Validation Workflow                       │
└─────────────────────────────────────────────────────────────────────────────┘

   Step 1              Step 2              Step 3              Step 4
┌──────────┐      ┌──────────────┐    ┌───────────────┐   ┌──────────────┐
│ Collect  │─────▶│   Schema &   │───▶│ Model-Specific│──▶│   Generate   │
│  Inputs  │      │  Profiling   │    │    Checks     │   │   Report     │
└──────────┘      └──────────────┘    └───────────────┘   └──────────────┘
     │                   │                    │                   │
     ▼                   ▼                    ▼                   ▼
 • Model type       • Table exists       • Classification:   • TLDR verdict
 • Target column    • Row counts           class balance     • Pros/Cons  
 • Source tables    • Column types         leakage risk      • Detailed findings
 • Time column      • Null rates        • Regression:        • Recommended fixes
 • Join keys        • Cardinality         outliers, skew
                    • Distributions     • Tree/Ensemble:
                                          cardinality
```

---

## Step 1: Collect User Inputs

Use the `ask_user_question` tool to gather the following information:

### Question 1: Model Type

Ask the user to select their model type:

```
What type of Machine Learning model are you planning to build?

Options:
1. Classification (Logistic Regression, Random Forest Classifier, XGBoost Classifier, SVM, Neural Net Classifier)
2. Regression (Linear Regression, Ridge, Lasso, Random Forest Regressor, XGBoost Regressor, Neural Net)
3. Tree/Ensemble (Random Forest, Gradient Boosting, XGBoost, LightGBM, CatBoost - when unsure if classification or regression)
4. Other / Not Sure (will run generic checks only)
```

Store the response as `MODEL_TYPE` with values: `classification`, `regression`, `tree_ensemble`, or `other`.

### Question 2: Target Variable

```
What is the name of your TARGET/LABEL column?
(The column you are trying to predict. Leave blank if unsupervised or unknown.)
```

Store as `TARGET_COLUMN`. If blank, note that target-specific checks will be skipped.

### Question 3: Source Tables

```
Please provide your Snowflake source table(s) using 3-part naming format:
DATABASE.SCHEMA.TABLE_NAME

You can provide multiple tables separated by commas.
Example: ANALYTICS.ML.CUSTOMER_FEATURES, ANALYTICS.ML.TRANSACTION_HISTORY
```

Store as `SOURCE_TABLES` (list).

### Question 4: Time Column (Optional)

```
Do you have a timestamp/date column for freshness and drift checks?
If yes, provide the column name. If no, type "none".
```

Store as `TIME_COLUMN` or null.

### Question 5: Join Keys (Optional)

```
If using multiple tables, what are the join key column(s)?
Example: CUSTOMER_ID, or CUSTOMER_ID + ORDER_ID
Type "none" if not applicable.
```

Store as `JOIN_KEYS` or null.

---

## Step 2: Schema Discovery & Basic Profiling

For each table in `SOURCE_TABLES`, execute the following checks:

### 2.1 Verify Table Exists

```sql
SELECT COUNT(*) AS TABLE_EXISTS
FROM {DATABASE}.INFORMATION_SCHEMA.TABLES
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}';
```

**If TABLE_EXISTS = 0:** Mark as BLOCKED and report "Table does not exist".

### 2.2 Get Row Count

```sql
SELECT COUNT(*) AS ROW_COUNT FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flag if:**
- ROW_COUNT = 0 → BLOCKED (no data)
- ROW_COUNT < 100 → NEEDS ATTENTION (very small dataset)
- ROW_COUNT < 1000 → Note: "Small dataset, may limit model complexity"

### 2.3 Get Column Metadata

```sql
SELECT 
    COLUMN_NAME,
    DATA_TYPE,
    IS_NULLABLE,
    CHARACTER_MAXIMUM_LENGTH,
    NUMERIC_PRECISION,
    NUMERIC_SCALE,
    COMMENT
FROM {DATABASE}.INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}'
ORDER BY ORDINAL_POSITION;
```

Record column count and data types for report.

### 2.4 Sample Data Preview

```sql
SELECT * FROM {DATABASE}.{SCHEMA}.{TABLE} LIMIT 5;
```

Use this to understand data patterns and identify obvious issues.

---

## Step 3: Missingness, Cardinality & Distribution Analysis

### 3.1 Comprehensive Column Profiling

For each table, run a profiling query that computes per-column statistics:

```sql
-- For NUMERIC columns
SELECT
    '{COLUMN_NAME}' AS COLUMN_NAME,
    'NUMERIC' AS COLUMN_TYPE,
    COUNT(*) AS TOTAL_ROWS,
    COUNT({COLUMN_NAME}) AS NON_NULL_COUNT,
    COUNT(*) - COUNT({COLUMN_NAME}) AS NULL_COUNT,
    ROUND((COUNT(*) - COUNT({COLUMN_NAME})) * 100.0 / NULLIF(COUNT(*), 0), 2) AS NULL_PERCENT,
    COUNT(DISTINCT {COLUMN_NAME}) AS DISTINCT_COUNT,
    MIN({COLUMN_NAME}) AS MIN_VAL,
    MAX({COLUMN_NAME}) AS MAX_VAL,
    AVG({COLUMN_NAME}) AS MEAN_VAL,
    MEDIAN({COLUMN_NAME}) AS MEDIAN_VAL,
    STDDEV({COLUMN_NAME}) AS STD_DEV,
    PERCENTILE_CONT(0.01) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS P1,
    PERCENTILE_CONT(0.05) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS P5,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS Q1,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS Q3,
    PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS P95,
    PERCENTILE_CONT(0.99) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS P99
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

```sql
-- For CATEGORICAL/STRING columns
SELECT
    '{COLUMN_NAME}' AS COLUMN_NAME,
    'CATEGORICAL' AS COLUMN_TYPE,
    COUNT(*) AS TOTAL_ROWS,
    COUNT({COLUMN_NAME}) AS NON_NULL_COUNT,
    COUNT(*) - COUNT({COLUMN_NAME}) AS NULL_COUNT,
    ROUND((COUNT(*) - COUNT({COLUMN_NAME})) * 100.0 / NULLIF(COUNT(*), 0), 2) AS NULL_PERCENT,
    COUNT(DISTINCT {COLUMN_NAME}) AS DISTINCT_COUNT,
    ROUND(COUNT(DISTINCT {COLUMN_NAME}) * 100.0 / NULLIF(COUNT({COLUMN_NAME}), 0), 2) AS CARDINALITY_RATIO,
    MIN(LENGTH({COLUMN_NAME})) AS MIN_LENGTH,
    MAX(LENGTH({COLUMN_NAME})) AS MAX_LENGTH,
    AVG(LENGTH({COLUMN_NAME})) AS AVG_LENGTH
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

### 3.2 Flagging Rules for Missingness

| Condition | Verdict | Action |
|-----------|---------|--------|
| NULL_PERCENT > 70% | BLOCKED | Column unusable without major imputation |
| NULL_PERCENT > 30% | NEEDS ATTENTION | Requires imputation strategy |
| NULL_PERCENT > 5% | Note | Document and monitor |
| NULL_PERCENT = 0% | Good | No action needed |

### 3.3 Flagging Rules for Cardinality

| Condition | Verdict | Note |
|-----------|---------|------|
| DISTINCT_COUNT = 1 | NEEDS ATTENTION | Constant column - no predictive value |
| DISTINCT_COUNT = 2 | Note | Binary feature - good for classification |
| DISTINCT_COUNT > 0.9 * ROW_COUNT | NEEDS ATTENTION | Very high cardinality - potential ID column |
| CATEGORICAL with DISTINCT > 1000 | NEEDS ATTENTION | May need encoding strategy for trees |

### 3.4 Outlier Detection (Numeric Columns)

```sql
SELECT
    '{COLUMN_NAME}' AS COLUMN_NAME,
    COUNT(CASE 
        WHEN {COLUMN_NAME} < (Q1 - 1.5 * (Q3 - Q1)) 
          OR {COLUMN_NAME} > (Q3 + 1.5 * (Q3 - Q1)) 
        THEN 1 
    END) AS IQR_OUTLIER_COUNT,
    COUNT(CASE 
        WHEN ABS({COLUMN_NAME} - AVG_VAL) / NULLIF(STD_VAL, 0) > 3 
        THEN 1 
    END) AS ZSCORE_OUTLIER_COUNT
FROM {DATABASE}.{SCHEMA}.{TABLE}
CROSS JOIN (
    SELECT 
        AVG({COLUMN_NAME}) AS AVG_VAL,
        STDDEV({COLUMN_NAME}) AS STD_VAL,
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS Q1,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY {COLUMN_NAME}) AS Q3
    FROM {DATABASE}.{SCHEMA}.{TABLE}
) stats;
```

**Flagging:**
- Z-score outliers > 5% of data → NEEDS ATTENTION
- Extreme outliers (Z > 6) present → NEEDS ATTENTION

---

## Step 4: Model-Specific Checks

### 4.A Classification Models

**Only run if MODEL_TYPE = 'classification' AND TARGET_COLUMN is provided.**

#### 4.A.1 Target Distribution & Class Balance

```sql
SELECT
    {TARGET_COLUMN} AS TARGET_CLASS,
    COUNT(*) AS CLASS_COUNT,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS CLASS_PERCENT
FROM {DATABASE}.{SCHEMA}.{TABLE}
WHERE {TARGET_COLUMN} IS NOT NULL
GROUP BY {TARGET_COLUMN}
ORDER BY CLASS_COUNT DESC;
```

**Flagging Rules:**

| Condition | Verdict | Recommendation |
|-----------|---------|----------------|
| Only 1 class present | BLOCKED | No classification possible |
| Majority class > 97% | BLOCKED | Extreme imbalance - model will be trivial |
| Majority class > 90% | NEEDS ATTENTION | Consider SMOTE, class weights, or undersampling |
| Majority class > 80% | Note | Monitor for imbalance effects |
| Balanced (40-60%) | Good | No action needed |

#### 4.A.2 Target Leakage Detection

Check for columns highly correlated with target (potential leakage):

```sql
-- For numeric columns vs binary target
SELECT
    '{COLUMN_NAME}' AS FEATURE,
    ABS(CORR({COLUMN_NAME}, 
        CASE WHEN {TARGET_COLUMN} = (SELECT MAX({TARGET_COLUMN}) FROM {TABLE}) THEN 1 ELSE 0 END
    )) AS TARGET_CORRELATION
FROM {DATABASE}.{SCHEMA}.{TABLE}
WHERE {COLUMN_NAME} IS NOT NULL;
```

**Flagging:**
- Correlation > 0.95 → BLOCKED (almost certain leakage)
- Correlation > 0.85 → NEEDS ATTENTION (investigate for leakage)
- Correlation > 0.70 → Note (strong predictor or potential leakage)

**Also flag these as potential leakage risks:**
- Columns with "ID", "KEY", "CODE" in name and high cardinality
- Timestamp columns that might be post-event
- Columns with names suggesting outcome (e.g., "RESULT", "STATUS", "FINAL")

#### 4.A.3 Invalid Target Values

```sql
SELECT
    COUNT(CASE WHEN {TARGET_COLUMN} IS NULL THEN 1 END) AS NULL_TARGETS,
    COUNT(DISTINCT {TARGET_COLUMN}) AS DISTINCT_CLASSES,
    LISTAGG(DISTINCT {TARGET_COLUMN}, ', ') AS CLASS_VALUES
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flag if:**
- NULL_TARGETS > 0 → NEEDS ATTENTION (rows with null target should be excluded)
- Unexpected class values (e.g., -1, 999, 'UNKNOWN') → Note and recommend filtering

---

### 4.B Regression Models

**Only run if MODEL_TYPE = 'regression' AND TARGET_COLUMN is provided.**

#### 4.B.1 Target Distribution Analysis

```sql
SELECT
    COUNT(*) AS TOTAL_ROWS,
    COUNT({TARGET_COLUMN}) AS NON_NULL_COUNT,
    MIN({TARGET_COLUMN}) AS MIN_VAL,
    MAX({TARGET_COLUMN}) AS MAX_VAL,
    AVG({TARGET_COLUMN}) AS MEAN_VAL,
    MEDIAN({TARGET_COLUMN}) AS MEDIAN_VAL,
    STDDEV({TARGET_COLUMN}) AS STD_DEV,
    (AVG({TARGET_COLUMN}) - MEDIAN({TARGET_COLUMN})) / NULLIF(STDDEV({TARGET_COLUMN}), 0) AS SKEWNESS_INDICATOR,
    PERCENTILE_CONT(0.01) WITHIN GROUP (ORDER BY {TARGET_COLUMN}) AS P1,
    PERCENTILE_CONT(0.99) WITHIN GROUP (ORDER BY {TARGET_COLUMN}) AS P99,
    COUNT(CASE WHEN {TARGET_COLUMN} = 0 THEN 1 END) AS ZERO_COUNT,
    COUNT(CASE WHEN {TARGET_COLUMN} < 0 THEN 1 END) AS NEGATIVE_COUNT
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flagging Rules:**

| Condition | Verdict | Recommendation |
|-----------|---------|----------------|
| All values same | BLOCKED | No regression possible |
| Skewness > 2 or < -2 | NEEDS ATTENTION | Consider log transform |
| P99/P1 ratio > 1000 | NEEDS ATTENTION | Heavy tails - consider winsorizing |
| > 50% zeros | NEEDS ATTENTION | Consider zero-inflated model or two-stage approach |
| Negative values present (if unexpected) | Note | Verify data correctness |

#### 4.B.2 Non-Finite Value Detection

```sql
SELECT
    COUNT(CASE WHEN {TARGET_COLUMN} IS NULL THEN 1 END) AS NULL_COUNT,
    COUNT(CASE WHEN TRY_TO_DOUBLE({TARGET_COLUMN}::VARCHAR) IS NULL 
               AND {TARGET_COLUMN} IS NOT NULL THEN 1 END) AS NON_NUMERIC_COUNT
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flag if any non-finite values found → NEEDS ATTENTION**

#### 4.B.3 Feature-Target Correlations

```sql
-- For each numeric feature
SELECT
    '{FEATURE_COLUMN}' AS FEATURE,
    CORR({FEATURE_COLUMN}, {TARGET_COLUMN}) AS CORRELATION
FROM {DATABASE}.{SCHEMA}.{TABLE}
WHERE {FEATURE_COLUMN} IS NOT NULL 
  AND {TARGET_COLUMN} IS NOT NULL;
```

Report top 10 most correlated features (positive and negative).

---

### 4.C Tree/Ensemble Models

**Run if MODEL_TYPE = 'tree_ensemble'.**

#### 4.C.1 High Cardinality Categorical Analysis

```sql
SELECT
    COLUMN_NAME,
    COUNT(DISTINCT COLUMN_VALUE) AS CARDINALITY
FROM (
    -- UNPIVOT categorical columns
) 
WHERE CARDINALITY > 100
ORDER BY CARDINALITY DESC;
```

**Flagging:**
- Cardinality > 10000 → NEEDS ATTENTION (may cause memory issues, slow training)
- Cardinality > 1000 → Note (consider target encoding or grouping rare categories)

#### 4.C.2 Feature Count Analysis

```sql
SELECT COUNT(*) AS COLUMN_COUNT
FROM {DATABASE}.INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}';
```

**Flagging:**
- > 500 columns → NEEDS ATTENTION (consider feature selection)
- > 1000 columns → NEEDS ATTENTION (high risk of overfitting, slow training)

#### 4.C.3 Sparse Feature Detection

```sql
-- Detect columns that are mostly NULL or zero
SELECT
    '{COLUMN_NAME}' AS COLUMN_NAME,
    ROUND((COUNT(*) - COUNT({COLUMN_NAME})) * 100.0 / COUNT(*), 2) AS NULL_PERCENT,
    ROUND(SUM(CASE WHEN {COLUMN_NAME} = 0 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS ZERO_PERCENT,
    ROUND((COUNT(*) - COUNT({COLUMN_NAME}) + SUM(CASE WHEN {COLUMN_NAME} = 0 THEN 1 ELSE 0 END)) * 100.0 / COUNT(*), 2) AS SPARSE_PERCENT
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flag if SPARSE_PERCENT > 95% → Note (very sparse, limited information)**

---

### 4.D Other / Not Sure

**Run if MODEL_TYPE = 'other'.**

- Execute only generic checks (Steps 2-3)
- In report, recommend user specify a model type for more targeted analysis
- Highlight that target-specific checks were skipped

---

## Step 5: Time & Freshness Analysis

**Only run if TIME_COLUMN is provided.**

### 5.1 Time Coverage

```sql
SELECT
    MIN({TIME_COLUMN}) AS EARLIEST_RECORD,
    MAX({TIME_COLUMN}) AS LATEST_RECORD,
    DATEDIFF('day', MIN({TIME_COLUMN}), MAX({TIME_COLUMN})) AS DAYS_SPAN,
    DATEDIFF('day', MAX({TIME_COLUMN}), CURRENT_TIMESTAMP()) AS DAYS_SINCE_LAST_RECORD,
    COUNT(DISTINCT DATE_TRUNC('day', {TIME_COLUMN})) AS UNIQUE_DAYS,
    COUNT(DISTINCT DATE_TRUNC('month', {TIME_COLUMN})) AS UNIQUE_MONTHS
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flagging Rules:**

| Condition | Verdict | Recommendation |
|-----------|---------|----------------|
| DAYS_SINCE_LAST_RECORD > 30 | BLOCKED | Data too stale for production model |
| DAYS_SINCE_LAST_RECORD > 7 | NEEDS ATTENTION | Data may be stale |
| DAYS_SPAN < 30 | NEEDS ATTENTION | Very short history - may not capture patterns |
| DAYS_SPAN < 90 | Note | Limited history for temporal patterns |

### 5.2 Time Gaps Detection

```sql
WITH date_spine AS (
    SELECT DATEADD('day', SEQ4(), 
           (SELECT MIN(DATE_TRUNC('day', {TIME_COLUMN})) FROM {DATABASE}.{SCHEMA}.{TABLE})) AS DT
    FROM TABLE(GENERATOR(ROWCOUNT => 1000))
    QUALIFY DT <= (SELECT MAX(DATE_TRUNC('day', {TIME_COLUMN})) FROM {DATABASE}.{SCHEMA}.{TABLE})
),
actual_dates AS (
    SELECT DISTINCT DATE_TRUNC('day', {TIME_COLUMN}) AS DT
    FROM {DATABASE}.{SCHEMA}.{TABLE}
)
SELECT 
    COUNT(*) AS MISSING_DAYS,
    LISTAGG(ds.DT::DATE, ', ') WITHIN GROUP (ORDER BY ds.DT) AS MISSING_DATES
FROM date_spine ds
LEFT JOIN actual_dates ad ON ds.DT = ad.DT
WHERE ad.DT IS NULL;
```

**Flag if MISSING_DAYS > 0 → Note (document gaps for time series models)**

### 5.3 Simple Drift Detection (Recent vs Historical)

```sql
-- Compare last 30 days vs prior period for key numeric features
WITH periods AS (
    SELECT 
        CASE 
            WHEN {TIME_COLUMN} >= DATEADD('day', -30, CURRENT_DATE()) THEN 'RECENT'
            ELSE 'HISTORICAL'
        END AS PERIOD,
        {NUMERIC_COLUMN} AS VAL
    FROM {DATABASE}.{SCHEMA}.{TABLE}
    WHERE {TIME_COLUMN} >= DATEADD('day', -180, CURRENT_DATE())
)
SELECT
    PERIOD,
    AVG(VAL) AS MEAN_VAL,
    STDDEV(VAL) AS STD_VAL,
    MEDIAN(VAL) AS MEDIAN_VAL
FROM periods
GROUP BY PERIOD;
```

**Flag if:**
- Mean differs by > 20% between periods → NEEDS ATTENTION (potential drift)
- Std differs by > 50% → NEEDS ATTENTION (distribution change)

---

## Step 6: Generate the Report

Create the final markdown report using this exact structure:

```markdown
# ML Data Quality Report

**Generated:** {CURRENT_TIMESTAMP}  
**Model Type:** {MODEL_TYPE}  
**Target Column:** {TARGET_COLUMN or "Not specified"}  
**Tables Analyzed:** {comma-separated SOURCE_TABLES}

---

## TLDR (Executive Summary)

| Attribute | Value |
|-----------|-------|
| **Verdict** | {READY / NEEDS ATTENTION / BLOCKED} |
| **Model Type** | {MODEL_TYPE} |
| **Total Tables** | {count} |
| **Total Rows** | {sum across tables} |
| **Critical Issues** | {count} |
| **Warnings** | {count} |

### Key Findings
- {FINDING_1}
- {FINDING_2}
- {FINDING_3}
- {FINDING_4}
- {FINDING_5}

### Top 3 Recommended Actions
1. {ACTION_1}
2. {ACTION_2}
3. {ACTION_3}

---

## 1. Overall Assessment

{Narrative paragraph summarizing data suitability for the requested model type}

**Readiness:** {READY for training / Needs preprocessing / Not ready - blocking issues}

**Caveats:** {Any limitations, e.g., "Suitable for PoC, production requires addressing X"}

---

## 2. Table-Level Summary

### {DATABASE.SCHEMA.TABLE_1}

| Metric | Value |
|--------|-------|
| Row Count | {X} |
| Column Count | {Y} |
| Columns with >30% Nulls | {Z} |

**Pros:**
- ✅ {strength_1}
- ✅ {strength_2}

**Cons:**
- ⚠️ {weakness_1}
- ⚠️ {weakness_2}

### {DATABASE.SCHEMA.TABLE_2}
[Repeat structure for each table]

---

## 3. Detailed Checks

### 3.1 Schema & Data Types

| Column | Data Type | Nullable | Notes |
|--------|-----------|----------|-------|
| {col1} | {type} | {yes/no} | {any issues} |
| {col2} | {type} | {yes/no} | {any issues} |

**Issues Found:**
- {issue_1 or "None"}

### 3.2 Missingness Analysis

| Column | Null % | Verdict | Recommendation |
|--------|--------|---------|----------------|
| {col1} | {X%} | {OK/WARN/BLOCK} | {action} |
| {col2} | {X%} | {OK/WARN/BLOCK} | {action} |

### 3.3 Cardinality Analysis

| Column | Type | Distinct Values | Cardinality Ratio | Verdict |
|--------|------|-----------------|-------------------|---------|
| {col1} | CAT | {X} | {Y%} | {OK/WARN} |
| {col2} | NUM | {X} | {Y%} | {OK/WARN} |

### 3.4 Distribution & Outliers

| Column | Min | Max | Mean | Median | Std Dev | Outliers | Skew |
|--------|-----|-----|------|--------|---------|----------|------|
| {col1} | {X} | {X} | {X} | {X} | {X} | {X%} | {X} |

**Outlier Issues:**
- {issue_1 or "None detected"}

### 3.5 Target Variable Analysis

**Target Column:** {TARGET_COLUMN}

[For Classification:]
| Class | Count | Percentage |
|-------|-------|------------|
| {class1} | {X} | {Y%} |
| {class2} | {X} | {Y%} |

**Class Imbalance Ratio:** {X:1}  
**Verdict:** {OK / NEEDS ATTENTION / BLOCKED}

[For Regression:]
| Metric | Value |
|--------|-------|
| Min | {X} |
| Max | {X} |
| Mean | {X} |
| Median | {X} |
| Skewness | {X} |

**Verdict:** {OK / NEEDS ATTENTION}

### 3.6 Leakage Risk Assessment

| Column | Correlation w/ Target | Risk Level | Reason |
|--------|----------------------|------------|--------|
| {col1} | {X} | {HIGH/MED/LOW} | {reason} |

**High-Risk Columns:** {list or "None identified"}

### 3.7 Time & Freshness

| Metric | Value |
|--------|-------|
| Earliest Record | {date} |
| Latest Record | {date} |
| Days Since Last Update | {X} |
| Time Span (days) | {X} |
| Missing Days | {X} |

**Freshness Verdict:** {OK / NEEDS ATTENTION / BLOCKED}

---

## 4. Model-Specific Recommendations for {MODEL_TYPE}

### Pre-processing Steps Required

| Priority | Step | Columns Affected | Rationale |
|----------|------|------------------|-----------|
| 1 | {step} | {cols} | {why} |
| 2 | {step} | {cols} | {why} |
| 3 | {step} | {cols} | {why} |

### Feature Engineering Suggestions

1. **{suggestion_1}** - {details}
2. **{suggestion_2}** - {details}
3. **{suggestion_3}** - {details}

### Handling Strategies

| Issue | Recommended Approach | Alternative |
|-------|---------------------|-------------|
| {issue} | {approach} | {alt} |

---

## 5. Data Quality Scorecard

| Dimension | Score | Weight | Weighted |
|-----------|-------|--------|----------|
| Completeness | {X}/100 | 25% | {Y} |
| Validity | {X}/100 | 20% | {Y} |
| Consistency | {X}/100 | 15% | {Y} |
| Uniqueness | {X}/100 | 15% | {Y} |
| Timeliness | {X}/100 | 15% | {Y} |
| Model Fit | {X}/100 | 10% | {Y} |
| **TOTAL** | | 100% | **{Z}/100** |

---

## 6. Next Steps Checklist

- [ ] {action_item_1}
- [ ] {action_item_2}
- [ ] {action_item_3}
- [ ] {action_item_4}
- [ ] {action_item_5}

---

## Appendix A: Column-Level Statistics

[Full statistics table for all columns]

## Appendix B: SQL Queries Used

[List of queries for reproducibility]
```

---

## Verdict Decision Logic

Apply this logic to determine the final TLDR verdict:

```
IF any BLOCKED condition found:
    Verdict = "BLOCKED"
    Reason = list all blocking issues
    
ELSE IF any NEEDS ATTENTION condition found:
    Verdict = "NEEDS ATTENTION"  
    Reason = list all warning issues
    
ELSE:
    Verdict = "READY"
    Reason = "No significant data quality issues detected"
```

### Blocking Conditions (BLOCKED)
- Table does not exist
- Table has 0 rows
- Target column has only 1 class (classification)
- Target column > 70% null
- Class imbalance > 97% (classification)
- Suspected leakage with correlation > 0.95
- Data > 30 days stale (if time column provided)

### Warning Conditions (NEEDS ATTENTION)
- Any column > 30% null
- Class imbalance > 90% (classification)
- Target skewness > 2 (regression)
- High cardinality categoricals > 1000 values
- Outliers > 5% of data
- Data > 7 days stale
- Suspected leakage with correlation > 0.85

---

## Configuration Overrides (Optional)

If a config file exists at `.cortex/ml-data-quality-config.yml` or `~/.snowflake/cortex/ml-data-quality-config.yml`, read and apply these thresholds:

```yaml
# Example configuration
null_rate_warning: 0.30
null_rate_block: 0.70

class_imbalance_warning: 0.90
class_imbalance_block: 0.97

outlier_percent_warning: 0.05
outlier_zscore_warning: 4.0
outlier_zscore_block: 6.0

cardinality_warning: 1000
cardinality_block: 10000

freshness_days_warning: 7
freshness_days_block: 30

leakage_correlation_warning: 0.85
leakage_correlation_block: 0.95
```

If no config file exists, use the default thresholds defined above.

---

## Important Guidelines

1. **Always use 3-part table names** - DATABASE.SCHEMA.TABLE
2. **Handle SQL errors gracefully** - If a query fails, note the error and continue
3. **Be thorough but efficient** - Use sampling for very large tables (>10M rows)
4. **Tailor recommendations** to the specific model type selected
5. **Provide actionable insights** - Not just statistics, but what to do about them
6. **Save the report** to a markdown file for documentation

---

## Example Interaction

```
User: Run ML data quality validation for a logistic regression churn model 
      using tables TELCO_DB.RAW.CUSTOMERS and TELCO_DB.RAW.USAGE 
      with target CHURN_FLAG and time column EVENT_DATE