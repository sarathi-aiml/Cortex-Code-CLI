# Cortex Code Skill: ML Data Quality Validation & Reporting

## Step-by-Step Guide for Creating Your Own Skill

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Prerequisites](#2-prerequisites)
3. [Understanding Cortex Code Skills](#3-understanding-cortex-code-skills)
4. [Architecture & Scope](#4-architecture--scope)
5. [Step-by-Step: Creating the Skill](#5-step-by-step-creating-the-skill)
6. [Testing Your Skill](#6-testing-your-skill)
7. [Sharing with Your Organization](#7-sharing-with-your-organization)
8. [Troubleshooting](#8-troubleshooting)
9. [Appendix](#9-appendix)

---

## 1. Introduction

### What You Will Build

This guide walks you through creating a **Cortex Code (CoCo) skill** named `ml-data-quality-validation` that:

1. **Asks the user** what type of model they are building (classification, regression, tree/ensemble, or other)
2. **Discovers Snowflake source tables** (using 3-part naming: `DATABASE.SCHEMA.TABLE`)
3. **Runs deep, model-aware data quality analysis** (schema, nulls, distributions, leakage risk, imbalance, outliers, drift, etc.)
4. **Generates a comprehensive markdown report** with:
   - **TLDR** at the top: `READY` / `NEEDS ATTENTION` / `BLOCKED` verdict
   - **Pros/Cons** by table and column
   - **Recommended remediation actions** before feature engineering and training

### Why This Matters

Running data quality checks **before** feature engineering and model training helps you:

- Catch data issues early (before wasting compute on bad data)
- Identify target leakage that would invalidate your model
- Document data quality for compliance and reproducibility
- Create a standard "DQ gate" for all ML projects

### Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│              ML Data Quality Validation Skill - End-to-End Flow                  │
└─────────────────────────────────────────────────────────────────────────────────┘

    ┌────────────┐         ┌────────────┐         ┌────────────┐         ┌────────────┐
    │   USER     │         │  COLLECT   │         │    RUN     │         │  GENERATE  │
    │  INVOKES   │────────▶│   INPUTS   │────────▶│   CHECKS   │────────▶│   REPORT   │
    │   SKILL    │         │            │         │            │         │            │
    └────────────┘         └────────────┘         └────────────┘         └────────────┘
          │                      │                      │                      │
          ▼                      ▼                      ▼                      ▼
    "$ml-data-           • Model type            • Schema checks         • TLDR verdict
     quality-            • Target column         • Null analysis         • Pros/Cons
     validation"         • Source tables         • Distribution          • Detailed findings
                         • Time column           • Model-specific        • Recommendations
                         • Join keys             • Freshness/drift       • Action checklist
```

---

## 2. Prerequisites

### Required Software

| Requirement | How to Verify |
|-------------|---------------|
| **Cortex Code CLI** installed | `cortex --version` |
| **Snowflake connection** configured | Check `~/.snowflake/connections.toml` exists |
| **Text editor** | VS Code, vim, nano, or any editor |
| **Terminal access** | bash, zsh, or PowerShell |

### Required Permissions

Your Snowflake user must have:
- `SELECT` on source tables you want to analyze
- `SELECT` on `INFORMATION_SCHEMA` views
- Access to the warehouse specified in your connection

### Verify Your Setup

Open your terminal and run:

```bash
# Check Cortex Code is installed
cortex --version

# Expected output: cortex version X.X.X

# Start Cortex Code to verify connection
cortex -c your_connection_name

# You should see the Cortex Code prompt
# Type /exit to quit
```

---

## 3. Understanding Cortex Code Skills

### What is a Skill?

A **skill** is a markdown file (`SKILL.md`) that teaches Cortex Code how to handle specific tasks. When you invoke a skill, its instructions are injected into the conversation, guiding the AI to perform specialized workflows.

**Skills can:**
- Define step-by-step workflows
- Specify which tools to use (SQL execution, file writing, etc.)
- Provide domain-specific knowledge
- Include examples and best practices

### Skill Storage Locations

CoCo auto-discovers skills from these locations:

| Location | Path | Scope | Priority |
|----------|------|-------|----------|
| **Project** | `.cortex/skills/` or `.claude/skills/` | Current project only | Highest |
| **Global (Personal)** | `~/.snowflake/cortex/skills/` | All your projects | Medium |
| **User** | `~/.claude/skills/` | Alternative personal | Medium |
| **Bundled** | Built into CoCo | System-wide | Lowest |

**Priority Rule:** Project skills override personal skills if names match.

### Skill File Structure

```
skill-name/
├── SKILL.md              # Main skill file (REQUIRED)
├── config.yml            # Optional: Configurable thresholds
├── templates/            # Optional: Report templates
└── README.md             # Optional: Documentation
```

---

## 4. Architecture & Scope

### 4.1 When to Use This Skill

Use **before** any of these activities:

| Activity | Why Run DQ Check First |
|----------|------------------------|
| Creating feature sets | Catch null/type issues before they propagate |
| Feature engineering | Identify columns with no predictive value |
| Model training | Detect leakage, imbalance, data staleness |
| Production deployment | Document data quality evidence |

**Typical user prompt:**
```
"Run ML data quality validation for a logistic regression model 
using tables X, Y, Z in Snowflake and give me a TLDR + pros/cons."
```

### 4.2 Inputs

The skill collects these inputs from the user:

| Input | Required | Description |
|-------|----------|-------------|
| **Model type** | Yes | `classification`, `regression`, `tree_ensemble`, or `other` |
| **Target column** | Recommended | The column being predicted (for target-specific checks) |
| **Source tables** | Yes | Snowflake 3-part names: `DATABASE.SCHEMA.TABLE` |
| **Time column** | Optional | For freshness and drift checks |
| **Join keys** | Optional | For multi-table analysis |

### 4.3 Outputs

The skill produces a markdown report:

```
# ML Data Quality Report (TLDR)

- Verdict: READY / NEEDS ATTENTION / BLOCKED
- Model type: classification / regression / tree_ensemble / other
- Key risks: ...
- Recommended next actions (top 3): ...

## 1. Overall Assessment
...

## 2. Table-Level Summary
- DB.SCHEMA.TABLE_A: Pros / Cons
- DB.SCHEMA.TABLE_B: Pros / Cons

## 3. Detailed Checks
### 3.1 Schema & Types
### 3.2 Missingness
### 3.3 Distributions, Outliers, Imbalance
### 3.4 Target & Leakage Risk
### 3.5 Time & Freshness

## 4. Recommended Fixes
...
```

### 4.4 Checks by Model Type

| Check Category | All Models | Classification | Regression | Tree/Ensemble |
|----------------|------------|----------------|------------|---------------|
| Table existence | ✅ | ✅ | ✅ | ✅ |
| Row counts | ✅ | ✅ | ✅ | ✅ |
| Column types | ✅ | ✅ | ✅ | ✅ |
| Null rates | ✅ | ✅ | ✅ | ✅ |
| Cardinality | ✅ | ✅ | ✅ | ✅ |
| Distributions | ✅ | ✅ | ✅ | ✅ |
| Class balance | | ✅ | | |
| Leakage detection | | ✅ | | |
| Target outliers | | | ✅ | |
| Target skewness | | | ✅ | |
| High cardinality cats | | | | ✅ |
| Wide table warnings | | | | ✅ |
| Time freshness | ✅ (if time col) | ✅ | ✅ | ✅ |
| Drift detection | ✅ (if time col) | ✅ | ✅ | ✅ |

---

## 5. Step-by-Step: Creating the Skill

### Step 1: Create the Skill Directory

Choose your preferred location:

**Option A: Global (Personal) - Available in all your projects**
```bash
# Create the skill directory
mkdir -p ~/.snowflake/cortex/skills/ml-data-quality-validation

# Navigate to it
cd ~/.snowflake/cortex/skills/ml-data-quality-validation
```

**Option B: Project-Scoped - Shared and versioned with your ML project**
```bash
# Navigate to your project root first
cd /path/to/your/ml-project

# Create the skill directory
mkdir -p .cortex/skills/ml-data-quality-validation

# Navigate to it
cd .cortex/skills/ml-data-quality-validation
```

**Verify the directory was created:**
```bash
pwd
# Should show: ~/.snowflake/cortex/skills/ml-data-quality-validation
# Or: /path/to/your/ml-project/.cortex/skills/ml-data-quality-validation
```

---

### Step 2: Create the SKILL.md File

Create an empty SKILL.md file:

```bash
touch SKILL.md
```

Open it in your editor:

```bash
# Using VS Code
code SKILL.md

# Or using vim
vim SKILL.md

# Or using nano
nano SKILL.md
```

---

### Step 3: Add the YAML Front Matter

The front matter defines the skill's metadata. Add this at the top of `SKILL.md`:

```yaml
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
```

**Explanation of fields:**

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique identifier for the skill |
| `description` | Yes | What the skill does; shown in `/skill list` |
| `allowed-tools` | No | `"*"` means all tools; or list specific tools |

---

### Step 4: Add the Skill Title and Trigger Information

After the front matter, add:

```markdown
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
```

---

### Step 5: Define the AI's Role

```markdown
---

## Your Role

You are a **Senior ML Data Quality Engineer**. Your job is to:
1. Analyze Snowflake tables that will feed feature engineering and model training
2. Perform model-aware data quality checks tailored to the user's ML objective
3. Produce a concise TLDR verdict plus detailed report with pros/cons and remediation steps
```

---

### Step 6: Define Input Collection Workflow

This section tells CoCo what to ask the user:

```markdown
---

## Step 1: Collect User Inputs

Use the `ask_user_question` tool to gather the following information:

### Question 1: Model Type

Ask the user to select their model type:

```
What type of Machine Learning model are you planning to build?

Options:
1. Classification (Logistic Regression, Random Forest Classifier, XGBoost, SVM)
2. Regression (Linear Regression, Ridge, Lasso, Random Forest Regressor, XGBoost)
3. Tree/Ensemble (Random Forest, Gradient Boosting, XGBoost, LightGBM)
4. Other / Not Sure (will run generic checks only)
```

Store the response as `MODEL_TYPE`.

### Question 2: Target Variable

```
What is the name of your TARGET/LABEL column?
(The column you are trying to predict. Leave blank if unsupervised or unknown.)
```

Store as `TARGET_COLUMN`.

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
```

---

### Step 7: Add Schema Discovery Checks

```markdown
---

## Step 2: Schema Discovery & Basic Profiling

For each table in `SOURCE_TABLES`, execute:

### 2.1 Verify Table Exists

```sql
SELECT COUNT(*) AS TABLE_EXISTS
FROM {DATABASE}.INFORMATION_SCHEMA.TABLES
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}';
```

**If TABLE_EXISTS = 0:** Mark as BLOCKED.

### 2.2 Get Row Count

```sql
SELECT COUNT(*) AS ROW_COUNT FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flagging:**
- ROW_COUNT = 0 → BLOCKED
- ROW_COUNT < 100 → NEEDS ATTENTION
- ROW_COUNT < 1000 → Note

### 2.3 Get Column Metadata

```sql
SELECT 
    COLUMN_NAME,
    DATA_TYPE,
    IS_NULLABLE
FROM {DATABASE}.INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}'
ORDER BY ORDINAL_POSITION;
```
```

---

### Step 8: Add Missingness and Cardinality Checks

```markdown
---

## Step 3: Missingness & Cardinality Analysis

### 3.1 Column Profiling

For numeric columns:
```sql
SELECT
    '{COLUMN_NAME}' AS COLUMN_NAME,
    COUNT(*) AS TOTAL_ROWS,
    COUNT({COLUMN_NAME}) AS NON_NULL_COUNT,
    ROUND((COUNT(*) - COUNT({COLUMN_NAME})) * 100.0 / COUNT(*), 2) AS NULL_PERCENT,
    COUNT(DISTINCT {COLUMN_NAME}) AS DISTINCT_COUNT,
    MIN({COLUMN_NAME}) AS MIN_VAL,
    MAX({COLUMN_NAME}) AS MAX_VAL,
    AVG({COLUMN_NAME}) AS MEAN_VAL,
    STDDEV({COLUMN_NAME}) AS STD_DEV
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

### 3.2 Flagging Rules

| Condition | Verdict | Action |
|-----------|---------|--------|
| NULL_PERCENT > 70% | BLOCKED | Unusable without major imputation |
| NULL_PERCENT > 30% | NEEDS ATTENTION | Requires imputation strategy |
| DISTINCT_COUNT = 1 | NEEDS ATTENTION | Constant column - no value |
| DISTINCT_COUNT ≈ ROW_COUNT | NEEDS ATTENTION | Potential ID column |
```

---

### Step 9: Add Model-Specific Checks

```markdown
---

## Step 4: Model-Specific Checks

### A. Classification (if MODEL_TYPE = 'classification')

#### Class Balance Check
```sql
SELECT
    {TARGET_COLUMN} AS TARGET_CLASS,
    COUNT(*) AS CLASS_COUNT,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS CLASS_PERCENT
FROM {DATABASE}.{SCHEMA}.{TABLE}
GROUP BY {TARGET_COLUMN}
ORDER BY CLASS_COUNT DESC;
```

**Flagging:**
- Only 1 class → BLOCKED
- Majority class > 97% → BLOCKED  
- Majority class > 90% → NEEDS ATTENTION

#### Leakage Detection
Check for columns highly correlated with target (potential leakage).

### B. Regression (if MODEL_TYPE = 'regression')

#### Target Distribution
```sql
SELECT
    MIN({TARGET_COLUMN}) AS MIN_VAL,
    MAX({TARGET_COLUMN}) AS MAX_VAL,
    AVG({TARGET_COLUMN}) AS MEAN_VAL,
    STDDEV({TARGET_COLUMN}) AS STD_DEV,
    (AVG({TARGET_COLUMN}) - MEDIAN({TARGET_COLUMN})) / NULLIF(STDDEV({TARGET_COLUMN}), 0) AS SKEWNESS
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flagging:**
- Skewness > 2 → NEEDS ATTENTION (consider log transform)

### C. Tree/Ensemble (if MODEL_TYPE = 'tree_ensemble')

- Flag very high cardinality categoricals (> 1000 values)
- Flag very wide tables (> 500 columns)
- Note: Trees handle non-linearities but may overfit on sparse features
```

---

### Step 10: Add Time and Freshness Checks

```markdown
---

## Step 5: Time & Freshness Analysis

**Only run if TIME_COLUMN is provided.**

### Time Coverage
```sql
SELECT
    MIN({TIME_COLUMN}) AS EARLIEST_RECORD,
    MAX({TIME_COLUMN}) AS LATEST_RECORD,
    DATEDIFF('day', MAX({TIME_COLUMN}), CURRENT_TIMESTAMP()) AS DAYS_SINCE_LAST
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

**Flagging:**
- DAYS_SINCE_LAST > 30 → BLOCKED (data too stale)
- DAYS_SINCE_LAST > 7 → NEEDS ATTENTION
```

---

### Step 11: Define the Report Structure

```markdown
---

## Step 6: Generate the Report

Create a markdown report with this structure:

```markdown
# ML Data Quality Report

**Generated:** {TIMESTAMP}  
**Model Type:** {MODEL_TYPE}  
**Target Column:** {TARGET_COLUMN}  
**Tables Analyzed:** {SOURCE_TABLES}

---

## TLDR (Executive Summary)

| Attribute | Value |
|-----------|-------|
| **Verdict** | READY / NEEDS ATTENTION / BLOCKED |
| **Critical Issues** | {count} |
| **Warnings** | {count} |

### Key Findings
- Finding 1
- Finding 2
- Finding 3

### Top 3 Recommended Actions
1. Action 1
2. Action 2
3. Action 3

---

## 1. Overall Assessment
{Narrative summary}

## 2. Table-Level Summary

### {TABLE_NAME}
**Pros:**
- ✅ Strength 1
- ✅ Strength 2

**Cons:**
- ⚠️ Issue 1
- ⚠️ Issue 2

## 3. Detailed Checks
[All detailed findings]

## 4. Recommended Fixes
[Specific remediation steps]
```
```

---

### Step 12: Add Verdict Decision Logic

```markdown
---

## Verdict Decision Logic

```
IF any BLOCKED condition found:
    Verdict = "BLOCKED"
    
ELSE IF any NEEDS ATTENTION condition found:
    Verdict = "NEEDS ATTENTION"  
    
ELSE:
    Verdict = "READY"
```

### Blocking Conditions
- Table does not exist
- Table has 0 rows
- Target has only 1 class (classification)
- Target > 70% null
- Class imbalance > 97%
- Leakage correlation > 0.95
- Data > 30 days stale

### Warning Conditions  
- Any column > 30% null
- Class imbalance > 90%
- Target skewness > 2
- High cardinality > 1000
- Outliers > 5%
- Data > 7 days stale
```

---

### Step 13: Save the File

Save your `SKILL.md` file:

- **VS Code:** `Cmd+S` (Mac) or `Ctrl+S` (Windows)
- **vim:** Press `Esc`, then type `:wq` and press `Enter`
- **nano:** Press `Ctrl+O`, `Enter`, then `Ctrl+X`

---

### Step 14: Verify the Complete File

Your complete `SKILL.md` should look like this (abbreviated):

```markdown
---
name: ml-data-quality-validation
description: >
  Run deep, model-aware data quality checks on Snowflake tables...
allowed-tools: "*"
---

# ML Data Quality Validation Skill

**When to invoke:**
...

## Your Role
...

## Step 1: Collect User Inputs
...

## Step 2: Schema Discovery
...

## Step 3: Missingness & Cardinality
...

## Step 4: Model-Specific Checks
...

## Step 5: Time & Freshness
...

## Step 6: Generate Report
...

## Verdict Decision Logic
...
```

---

## 6. Testing Your Skill

### 6.1 Verify Skill is Loaded

Start Cortex Code and list skills:

```bash
# Start CoCo
cortex

# List all available skills
/skill list
```

You should see your skill in the list:
```
ml-data-quality-validation - Run deep, model-aware data quality checks...
```

### 6.2 Test with Explicit Invocation

```
> $ml-data-quality-validation
```

CoCo should start asking you the questions defined in Step 1.

### 6.3 Test with Natural Language

```
> Check ML data quality for my classification model using table 
  ANALYTICS.ML.CUSTOMER_DATA with target column CHURN_FLAG
```

CoCo should recognize the intent and invoke your skill.

### 6.4 Full End-to-End Test

Try this complete example:

```
> I'm planning a logistic regression churn model.
> Use the ml-data-quality-validation skill to check data quality for
> tables TELCO_DB.RAW.CUSTOMERS and TELCO_DB.RAW.USAGE with target CHURN_FLAG
> and time column EVENT_DATE. Output a TLDR and detailed pros/cons.
```

**Expected behavior:**
1. Skill confirms model type: classification
2. Skill confirms target: CHURN_FLAG
3. Skill confirms tables
4. Runs all checks
5. Outputs markdown report with TLDR

---

## 7. Sharing with Your Organization

### Option 1: Git Repository (Recommended)

**Step 1: Create a skills repository**
```bash
# Create new repo
mkdir my-org-coco-skills
cd my-org-coco-skills
git init

# Copy your skill
cp -r ~/.snowflake/cortex/skills/ml-data-quality-validation .

# Commit
git add .
git commit -m "Add ML data quality validation skill"
```

**Step 2: Push to your org's Git**
```bash
git remote add origin https://github.com/your-org/coco-skills.git
git push -u origin main
```

**Step 3: Team members install**
```bash
# Using CLI
cortex skill add https://github.com/your-org/coco-skills.git

# Or inside CoCo
/skill add https://github.com/your-org/coco-skills.git
```

### Option 2: Project-Scoped (Version with Code)

Commit the skill with your ML project:

```bash
# In your ML project directory
git add .cortex/skills/ml-data-quality-validation
git commit -m "Add ML data quality validation CoCo skill"
git push
```

Anyone who clones the repo gets the skill automatically.

### Option 3: Sync to Global

If you created a project skill and want it globally:

```bash
# Inside CoCo
/skill sync ml-data-quality-validation
```

This copies the skill to `~/.snowflake/cortex/skills/`.

---

## 8. Troubleshooting

### Skill Not Appearing in List

**Check 1: Verify file location**
```bash
ls -la ~/.snowflake/cortex/skills/ml-data-quality-validation/
# Should show SKILL.md
```

**Check 2: Verify file name is exactly `SKILL.md`**
```bash
# Case matters! These are wrong:
# skill.md, Skill.md, SKILL.MD
```

**Check 3: Verify front matter syntax**
```bash
head -10 ~/.snowflake/cortex/skills/ml-data-quality-validation/SKILL.md
```
Should start with `---` and have valid YAML.

### Skill Not Triggering Automatically

**Solution 1:** Use explicit invocation:
```
$ml-data-quality-validation
```

**Solution 2:** Mention the skill name:
```
Use the ml-data-quality-validation skill to...
```

### SQL Queries Failing

**Check 1:** Verify 3-part table names
```
DATABASE.SCHEMA.TABLE (correct)
SCHEMA.TABLE (wrong)
TABLE (wrong)
```

**Check 2:** Verify permissions
```sql
-- In Snowflake
SHOW GRANTS ON TABLE DATABASE.SCHEMA.TABLE;
```

### Report Not Being Saved

Ensure `allowed-tools: "*"` is in front matter, or explicitly list `write`:
```yaml
allowed-tools:
  - snowflake_sql_execute
  - ask_user_question  
  - write
```

---

## 9. Appendix

### A. Complete SKILL.md Template

A complete, production-ready `SKILL.md` has been installed at:
```
~/.snowflake/cortex/skills/ml-data-quality-validation/SKILL.md
```

### B. Optional Configuration File

Create `~/.snowflake/cortex/ml-data-quality-config.yml` to customize thresholds:

```yaml
# Null rate thresholds
null_rate_warning: 0.30
null_rate_block: 0.70

# Class imbalance thresholds (majority class share)
class_imbalance_warning: 0.90
class_imbalance_block: 0.97

# Outlier thresholds
outlier_percent_warning: 0.05
outlier_zscore_warning: 4.0

# Cardinality thresholds
cardinality_warning: 1000
cardinality_block: 10000

# Freshness thresholds (days since last record)
freshness_days_warning: 7
freshness_days_block: 30

# Leakage correlation thresholds
leakage_correlation_warning: 0.85
leakage_correlation_block: 0.95
```

### C. Quick Reference Commands

| Command | Description |
|---------|-------------|
| `cortex skill list` | List all skills |
| `cortex skill add <path>` | Add skill from path |
| `/skill list` | List skills (in CoCo) |
| `/skill sync <name>` | Sync to global |
| `$skill-name` | Invoke skill explicitly |

### D. Example Report Output

```markdown
# ML Data Quality Report

**Generated:** 2024-01-15 10:30:00 UTC  
**Model Type:** classification  
**Target Column:** CHURN_FLAG  
**Tables Analyzed:** TELCO_DB.RAW.CUSTOMERS, TELCO_DB.RAW.USAGE

---

## TLDR (Executive Summary)

| Attribute | Value |
|-----------|-------|
| **Verdict** | ⚠️ NEEDS ATTENTION |
| **Critical Issues** | 0 |
| **Warnings** | 3 |

### Key Findings
- Class imbalance detected: 87% non-churn vs 13% churn
- Column `LAST_CALL_DURATION` has 42% null values
- Data is 3 days fresh (acceptable)

### Top 3 Recommended Actions
1. Apply SMOTE or class weights to handle imbalance
2. Impute or drop `LAST_CALL_DURATION` column
3. Review `ACCOUNT_ID` for potential leakage (correlation: 0.78)

---

## 1. Overall Assessment

The data is **suitable for a PoC model** but requires preprocessing before production training. The main concerns are class imbalance and one high-null column.

...
```

---

## Summary

You have learned how to:

✅ Create a Cortex Code skill directory  
✅ Write the SKILL.md file with proper YAML front matter  
✅ Define user input collection workflow  
✅ Add comprehensive data quality checks  
✅ Implement model-specific validations  
✅ Generate detailed reports with TLDR verdicts  
✅ Test and troubleshoot your skill  
✅ Share the skill with your organization  

Your `ml-data-quality-validation` skill is now ready to serve as a **standard pre-training data quality gate** for all ML projects!

---

**Questions or Feedback?**  
Use `/feedback` in Cortex Code to submit feedback about this skill or guide.
