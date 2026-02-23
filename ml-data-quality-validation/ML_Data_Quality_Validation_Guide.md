---
title: "Cortex Code Skill: ML Data Quality Validation & Reporting"
subtitle: "Step-by-Step Guide for Creating Your Own Skill"
author: "Snowflake Cortex Code Team"
date: "2024"
geometry: margin=1in
fontsize: 11pt
linkcolor: blue
urlcolor: blue
toccolor: black
toc: true
toc-depth: 3
header-includes:
  - \usepackage{fancyhdr}
  - \pagestyle{fancy}
  - \fancyhead[L]{ML Data Quality Validation Skill}
  - \fancyhead[R]{Cortex Code Guide}
  - \usepackage{xcolor}
  - \definecolor{snowblue}{RGB}{41, 128, 185}
  - \definecolor{blockcolor}{RGB}{255, 240, 240}
  - \definecolor{warncolor}{RGB}{255, 250, 230}
  - \definecolor{readycolor}{RGB}{240, 255, 240}
---

\newpage

# Executive Summary

## What This Skill Does

The **ml-data-quality-validation** skill is a Cortex Code (CoCo) skill that performs comprehensive, model-aware data quality checks on Snowflake tables **before** feature engineering and model training.

### Key Capabilities

| Capability | Description |
|------------|-------------|
| **Interactive Input Collection** | Asks user for model type, target column, source tables |
| **Deep Data Profiling** | Analyzes nulls, distributions, cardinality, outliers |
| **Model-Specific Checks** | Tailored validations for classification, regression, tree models |
| **Automated Reporting** | Generates markdown report with TLDR verdict |
| **Actionable Recommendations** | Provides specific remediation steps |

### Workflow Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│              ML Data Quality Validation - End-to-End Flow               │
└─────────────────────────────────────────────────────────────────────────┘

   STEP 1            STEP 2            STEP 3            STEP 4
┌──────────┐    ┌──────────────┐   ┌───────────────┐  ┌──────────────┐
│ COLLECT  │───▶│   SCHEMA &   │──▶│MODEL-SPECIFIC │─▶│   GENERATE   │
│  INPUTS  │    │  PROFILING   │   │    CHECKS     │  │   REPORT     │
└──────────┘    └──────────────┘   └───────────────┘  └──────────────┘
     │                │                   │                  │
     ▼                ▼                   ▼                  ▼
 • Model type     • Table exists     • Classification:  • TLDR verdict
 • Target col     • Row counts         class balance    • Pros/Cons
 • Source tables  • Column types       leakage risk     • Findings
 • Time column    • Null rates       • Regression:      • Fixes
 • Join keys      • Cardinality        outliers, skew
                  • Distributions    • Tree/Ensemble:
                                       cardinality
```

### TLDR Verdicts

The skill produces one of three verdicts:

| Verdict | Meaning | Action Required |
|---------|---------|-----------------|
| **READY** | No blocking DQ issues | Proceed with feature engineering |
| **NEEDS ATTENTION** | Non-blocking issues found | Address before production |
| **BLOCKED** | Severe issues detected | Must fix before any modeling |

\newpage

## How to Use the Skill

### Quick Start (After Installation)

**Option 1: Explicit Invocation**
```
$ml-data-quality-validation
```

**Option 2: Natural Language**
```
Check ML data quality for my classification model using table 
ANALYTICS.ML.CUSTOMER_DATA with target column CHURN_FLAG
```

**Option 3: Full Command**
```
I'm planning a logistic regression churn model.
Use the ml-data-quality-validation skill to check data quality for
tables TELCO_DB.RAW.CUSTOMERS and TELCO_DB.RAW.USAGE 
with target CHURN_FLAG and time column EVENT_DATE.
Output a TLDR and detailed pros/cons.
```

### Example Session

```
User: $ml-data-quality-validation

CoCo: I'll help you validate data quality for your ML project. 
      Let me ask a few questions:

      What type of ML model are you building?
      1. Classification (Logistic Regression, Random Forest, XGBoost)
      2. Regression (Linear, Ridge, Lasso, Neural Net)
      3. Tree/Ensemble (Random Forest, Gradient Boosting)
      4. Other / Not Sure

User: Classification - building a customer churn predictor

CoCo: What is your target/label column name?

User: CHURN_FLAG

CoCo: Please provide your Snowflake source tables (3-part names):

User: ANALYTICS.ML.CUSTOMERS, ANALYTICS.ML.USAGE_METRICS

CoCo: Do you have a timestamp column for freshness checks?

User: EVENT_DATE

CoCo: Running comprehensive data quality analysis...
      [Executes SQL checks]
      
      Here is your ML Data Quality Report:
      
      # ML Data Quality Report
      
      ## TLDR
      | Attribute | Value |
      |-----------|-------|
      | **Verdict** | ⚠️ NEEDS ATTENTION |
      | **Critical Issues** | 0 |
      | **Warnings** | 2 |
      
      ### Key Findings
      - Class imbalance: 85% non-churn vs 15% churn
      - Column LAST_CONTACT has 35% null values
      ...
```

\newpage

# Part 1: Introduction

## 1.1 Purpose of This Guide

This guide provides step-by-step instructions for creating a Cortex Code skill that performs ML data quality validation. By the end of this guide, you will have:

- A working `ml-data-quality-validation` skill
- Understanding of CoCo skill architecture
- Ability to customize and extend the skill
- Knowledge to share the skill with your organization

## 1.2 Why Data Quality Matters for ML

Running data quality checks **before** feature engineering and model training helps you:

| Benefit | Description |
|---------|-------------|
| **Early Issue Detection** | Catch problems before wasting compute on bad data |
| **Leakage Prevention** | Identify target leakage that would invalidate models |
| **Documentation** | Create DQ evidence for compliance and reproducibility |
| **Standardization** | Establish a consistent "DQ gate" for all ML projects |

## 1.3 When to Use This Skill

Use this skill **before** any of these activities:

- Creating feature sets or feature stores
- Running feature engineering pipelines
- Training ML models (Snowpark ML, external, etc.)
- Deploying models to production

\newpage

# Part 2: Prerequisites

## 2.1 Required Software

| Requirement | How to Verify | Installation |
|-------------|---------------|--------------|
| Cortex Code CLI | `cortex --version` | See Snowflake docs |
| Snowflake connection | `cat ~/.snowflake/connections.toml` | Configure connection |
| Text editor | Any (VS Code, vim, nano) | - |
| Terminal access | bash, zsh, or PowerShell | - |

## 2.2 Required Permissions

Your Snowflake user must have:

- `SELECT` permission on source tables
- `SELECT` on `INFORMATION_SCHEMA` views
- Access to a warehouse

## 2.3 Verify Your Setup

```bash
# Step 1: Check Cortex Code version
cortex --version
# Expected: cortex version X.X.X

# Step 2: Start CoCo with your connection
cortex -c your_connection_name

# Step 3: Verify connection works
/sql SELECT CURRENT_USER(), CURRENT_ROLE();

# Step 4: Exit
/exit
```

\newpage

# Part 3: Understanding Cortex Code Skills

## 3.1 What is a Skill?

A **skill** is a markdown file (`SKILL.md`) that teaches Cortex Code how to handle specific tasks. When invoked, the skill's instructions guide the AI to perform specialized workflows.

**Skills can:**

- Define step-by-step workflows
- Specify which tools to use
- Provide domain-specific knowledge
- Include examples and best practices

## 3.2 Skill Storage Locations

CoCo auto-discovers skills from these locations:

| Location | Path | Scope | Priority |
|----------|------|-------|----------|
| Project | `.cortex/skills/` | Current project | Highest |
| Global | `~/.snowflake/cortex/skills/` | All projects | Medium |
| User | `~/.claude/skills/` | Alternative | Medium |
| Bundled | Built into CoCo | System-wide | Lowest |

**Priority Rule:** Project skills override personal skills if names match.

## 3.3 Skill File Structure

```
skill-name/
├── SKILL.md              # Main skill file (REQUIRED)
├── config.yml            # Optional: Configurable thresholds
├── templates/            # Optional: Report templates
└── README.md             # Optional: Documentation
```

## 3.4 SKILL.md Anatomy

```markdown
---
name: skill-name                    # Unique identifier
description: What the skill does    # Shown in /skill list
allowed-tools: "*"                  # Tools the skill can use
---

# Skill Title

**When to invoke:** Description of triggers

## Your Role
Instructions for the AI

## Workflow Steps
Step-by-step process

## Output Format
Expected output structure
```

\newpage

# Part 4: Architecture & Design

## 4.1 Inputs Collected

| Input | Required | Description | Example |
|-------|----------|-------------|---------|
| Model type | Yes | Type of ML model | `classification` |
| Target column | Recommended | Column being predicted | `CHURN_FLAG` |
| Source tables | Yes | 3-part Snowflake names | `DB.SCHEMA.TABLE` |
| Time column | Optional | For freshness checks | `EVENT_DATE` |
| Join keys | Optional | For multi-table analysis | `CUSTOMER_ID` |

## 4.2 Checks by Model Type

### Common Checks (All Models)

- Table existence and row counts
- Column data types and nullable flags
- Missingness and constant columns
- Cardinality for categorical columns
- Distribution stats (min/max/mean/std)
- Time coverage and freshness

### Classification-Specific

- Number of classes
- Class imbalance ratios
- Invalid labels (nulls, nonsensical values)
- Leakage risk (high correlation with target)

### Regression-Specific

- Target outliers and skew
- Non-finite values (NaN, inf)
- Feature-target correlations

### Tree/Ensemble-Specific

- Very high cardinality categoricals
- Wide tables (many columns)
- Sparse indicator features

## 4.3 Verdict Logic

```
IF any BLOCKED condition:
    Verdict = "BLOCKED"
ELSE IF any WARNING condition:
    Verdict = "NEEDS ATTENTION"
ELSE:
    Verdict = "READY"
```

### Blocking Conditions

| Condition | Threshold |
|-----------|-----------|
| Table does not exist | - |
| Table has 0 rows | - |
| Target has only 1 class | Classification |
| Target > 70% null | - |
| Class imbalance > 97% | Classification |
| Leakage correlation > 0.95 | - |
| Data > 30 days stale | If time column |

### Warning Conditions

| Condition | Threshold |
|-----------|-----------|
| Any column > 30% null | - |
| Class imbalance > 90% | Classification |
| Target skewness > 2 | Regression |
| Cardinality > 1000 | Categorical |
| Outliers > 5% | Numeric |
| Data > 7 days stale | If time column |

\newpage

# Part 5: Step-by-Step Creation

## Step 1: Create the Skill Directory

Choose your preferred location:

### Option A: Global (Personal)

```bash
# Create directory
mkdir -p ~/.snowflake/cortex/skills/ml-data-quality-validation

# Navigate to it
cd ~/.snowflake/cortex/skills/ml-data-quality-validation
```

### Option B: Project-Scoped

```bash
# Navigate to your ML project
cd /path/to/your/ml-project

# Create skill directory
mkdir -p .cortex/skills/ml-data-quality-validation

# Navigate to it
cd .cortex/skills/ml-data-quality-validation
```

### Verify

```bash
pwd
# Should show the skill directory path
```

---

## Step 2: Create SKILL.md

```bash
# Create empty file
touch SKILL.md

# Open in editor
code SKILL.md    # VS Code
# or
vim SKILL.md     # vim
# or
nano SKILL.md    # nano
```

---

## Step 3: Add YAML Front Matter

Add this at the top of `SKILL.md`:

```yaml
---
name: ml-data-quality-validation
description: >
  Run deep, model-aware data quality checks on Snowflake tables 
  before feature engineering and ML training. Produces a report 
  with TLDR verdict (READY/NEEDS ATTENTION/BLOCKED), pros/cons, 
  and recommended fixes.
  Trigger phrases: "check ML data quality", "validate data before 
  model training", "run pre-training data quality checks"
allowed-tools: "*"
---
```

**Field Explanations:**

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique skill identifier |
| `description` | Yes | Shown in `/skill list` |
| `allowed-tools` | No | `"*"` = all tools allowed |

---

## Step 4: Add Skill Header

```markdown
# ML Data Quality Validation Skill

**When to invoke:**  
Use this skill **BEFORE** any of the following:
- Creating feature sets or feature stores
- Running feature engineering pipelines  
- Training ML models (Snowpark ML, external, etc.)

**Trigger phrases:**
- "check ML data quality"
- "validate data before model training"
- "run pre-training data quality checks"
- "ML DQ gate"
- "$ml-data-quality-validation"
```

---

## Step 5: Define AI Role

```markdown
---

## Your Role

You are a **Senior ML Data Quality Engineer**. Your job is to:
1. Analyze Snowflake tables that will feed feature engineering
2. Perform model-aware data quality checks
3. Produce a TLDR verdict plus detailed report with pros/cons
```

---

## Step 6: Add Input Collection

```markdown
---

## Step 1: Collect User Inputs

Use the `ask_user_question` tool to gather:

### Question 1: Model Type
```
What type of ML model are you building?
1. Classification (Logistic Regression, Random Forest, XGBoost)
2. Regression (Linear, Ridge, Lasso, Neural Net)
3. Tree/Ensemble (Random Forest, Gradient Boosting)
4. Other / Not Sure
```

### Question 2: Target Variable
```
What is your TARGET/LABEL column name?
(Leave blank if unsupervised)
```

### Question 3: Source Tables
```
Provide Snowflake source tables (3-part names):
DATABASE.SCHEMA.TABLE_NAME

Multiple tables: separate with commas
```

### Question 4: Time Column (Optional)
```
Timestamp column for freshness checks?
(Type "none" if not applicable)
```
```

---

## Step 7: Add Schema Discovery

```markdown
---

## Step 2: Schema Discovery

For each table, execute:

### 2.1 Verify Table Exists
```sql
SELECT COUNT(*) AS TABLE_EXISTS
FROM {DATABASE}.INFORMATION_SCHEMA.TABLES
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}';
```

### 2.2 Get Row Count
```sql
SELECT COUNT(*) AS ROW_COUNT 
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

### 2.3 Get Column Metadata
```sql
SELECT COLUMN_NAME, DATA_TYPE, IS_NULLABLE
FROM {DATABASE}.INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_CATALOG = '{DATABASE}'
  AND TABLE_SCHEMA = '{SCHEMA}'
  AND TABLE_NAME = '{TABLE}'
ORDER BY ORDINAL_POSITION;
```
```

---

## Step 8: Add Profiling Checks

```markdown
---

## Step 3: Data Profiling

### 3.1 Numeric Column Profile
```sql
SELECT
    '{COLUMN}' AS COLUMN_NAME,
    COUNT(*) AS TOTAL_ROWS,
    COUNT({COLUMN}) AS NON_NULL,
    ROUND((COUNT(*) - COUNT({COLUMN})) * 100.0 / COUNT(*), 2) AS NULL_PCT,
    COUNT(DISTINCT {COLUMN}) AS DISTINCT_COUNT,
    MIN({COLUMN}) AS MIN_VAL,
    MAX({COLUMN}) AS MAX_VAL,
    AVG({COLUMN}) AS MEAN_VAL,
    STDDEV({COLUMN}) AS STD_DEV
FROM {DATABASE}.{SCHEMA}.{TABLE};
```

### 3.2 Flagging Rules

| Condition | Verdict |
|-----------|---------|
| NULL_PCT > 70% | BLOCKED |
| NULL_PCT > 30% | NEEDS ATTENTION |
| DISTINCT_COUNT = 1 | NEEDS ATTENTION (constant) |
```

---

## Step 9: Add Model-Specific Checks

```markdown
---

## Step 4: Model-Specific Checks

### A. Classification
```sql
SELECT
    {TARGET} AS CLASS,
    COUNT(*) AS CNT,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS PCT
FROM {TABLE}
GROUP BY {TARGET}
ORDER BY CNT DESC;
```

**Flags:**
- 1 class only → BLOCKED
- Majority > 97% → BLOCKED
- Majority > 90% → NEEDS ATTENTION

### B. Regression
Check target for outliers, skewness, non-finite values.

### C. Tree/Ensemble
Flag high cardinality (>1000) and wide tables (>500 columns).
```

---

## Step 10: Add Report Template

```markdown
---

## Step 5: Generate Report

Output this structure:

```markdown
# ML Data Quality Report

**Generated:** {TIMESTAMP}
**Model Type:** {MODEL_TYPE}
**Target:** {TARGET_COLUMN}
**Tables:** {SOURCE_TABLES}

## TLDR

| Attribute | Value |
|-----------|-------|
| **Verdict** | READY / NEEDS ATTENTION / BLOCKED |
| **Critical Issues** | {count} |
| **Warnings** | {count} |

### Key Findings
- Finding 1
- Finding 2

### Top 3 Actions
1. Action 1
2. Action 2
3. Action 3

## Table Summary

### {TABLE_NAME}
**Pros:** ✅ ...
**Cons:** ⚠️ ...

## Detailed Checks
[All findings]

## Recommended Fixes
[Remediation steps]
```
```

---

## Step 11: Save the File

- **VS Code:** `Cmd+S` (Mac) / `Ctrl+S` (Windows)
- **vim:** `Esc` → `:wq` → `Enter`
- **nano:** `Ctrl+O` → `Enter` → `Ctrl+X`

\newpage

# Part 6: Testing Your Skill

## 6.1 Verify Skill is Loaded

```bash
# Start CoCo
cortex

# List skills
/skill list
```

You should see:
```
ml-data-quality-validation - Run deep, model-aware data quality...
```

## 6.2 Test Explicit Invocation

```
> $ml-data-quality-validation
```

CoCo should start asking questions.

## 6.3 Test Natural Language

```
> Check ML data quality for my classification model 
  using table ANALYTICS.ML.CUSTOMERS with target CHURN_FLAG
```

## 6.4 Full End-to-End Test

```
> I'm building a logistic regression churn model.
> Validate data quality for tables:
> - TELCO_DB.RAW.CUSTOMERS
> - TELCO_DB.RAW.USAGE
> Target: CHURN_FLAG
> Time column: EVENT_DATE
> Give me TLDR and pros/cons.
```

\newpage

# Part 7: Sharing with Your Organization

## 7.1 Option 1: Git Repository (Recommended)

### Create Skills Repo

```bash
mkdir my-org-coco-skills
cd my-org-coco-skills
git init

# Copy skill
cp -r ~/.snowflake/cortex/skills/ml-data-quality-validation .

# Commit and push
git add .
git commit -m "Add ML data quality validation skill"
git remote add origin https://github.com/your-org/coco-skills.git
git push -u origin main
```

### Team Members Install

```bash
cortex skill add https://github.com/your-org/coco-skills.git
```

Or in CoCo:
```
/skill add https://github.com/your-org/coco-skills.git
```

## 7.2 Option 2: Project-Scoped

Commit skill with your ML project:

```bash
git add .cortex/skills/ml-data-quality-validation
git commit -m "Add ML DQ validation skill"
git push
```

Anyone cloning the repo gets the skill automatically.

## 7.3 Option 3: Sync to Global

```
/skill sync ml-data-quality-validation
```

\newpage

# Part 8: Troubleshooting

## 8.1 Skill Not Appearing

**Check file location:**
```bash
ls -la ~/.snowflake/cortex/skills/ml-data-quality-validation/
```

**Check file name:** Must be exactly `SKILL.md` (case-sensitive)

**Check front matter:**
```bash
head -10 ~/.snowflake/cortex/skills/ml-data-quality-validation/SKILL.md
```

## 8.2 Skill Not Triggering

Use explicit invocation:
```
$ml-data-quality-validation
```

Or mention skill name:
```
Use the ml-data-quality-validation skill to...
```

## 8.3 SQL Queries Failing

**Verify 3-part names:**
```
DATABASE.SCHEMA.TABLE  ✓ Correct
SCHEMA.TABLE           ✗ Wrong
TABLE                  ✗ Wrong
```

**Verify permissions:**
```sql
SHOW GRANTS ON TABLE DATABASE.SCHEMA.TABLE;
```

## 8.4 Report Not Saving

Ensure front matter has:
```yaml
allowed-tools: "*"
```

Or explicitly:
```yaml
allowed-tools:
  - snowflake_sql_execute
  - ask_user_question
  - write
```

\newpage

# Appendix A: Quick Reference

## Commands

| Command | Description |
|---------|-------------|
| `cortex skill list` | List all skills |
| `cortex skill add <path>` | Add skill from path |
| `/skill list` | List skills (in CoCo) |
| `/skill sync <name>` | Sync to global |
| `$skill-name` | Invoke skill |

## File Locations

| Type | Path |
|------|------|
| Global skills | `~/.snowflake/cortex/skills/` |
| Project skills | `.cortex/skills/` |
| Config | `~/.snowflake/cortex/settings.json` |

## Trigger Phrases

- "check ML data quality"
- "validate data before model training"
- "run pre-training data quality checks"
- "ML DQ gate"
- "$ml-data-quality-validation"

\newpage

# Appendix B: Configuration Template

Create `~/.snowflake/cortex/ml-data-quality-config.yml`:

```yaml
# Null rate thresholds
null_rate_warning: 0.30
null_rate_block: 0.70

# Class imbalance (majority class share)
class_imbalance_warning: 0.90
class_imbalance_block: 0.97

# Outlier thresholds
outlier_percent_warning: 0.05
outlier_zscore_warning: 4.0

# Cardinality thresholds
cardinality_warning: 1000
cardinality_block: 10000

# Freshness (days since last record)
freshness_days_warning: 7
freshness_days_block: 30

# Leakage correlation
leakage_correlation_warning: 0.85
leakage_correlation_block: 0.95
```

\newpage

# Appendix C: Sample Report Output

```markdown
# ML Data Quality Report

**Generated:** 2024-01-15 10:30:00 UTC
**Model Type:** classification
**Target Column:** CHURN_FLAG
**Tables:** TELCO_DB.RAW.CUSTOMERS, TELCO_DB.RAW.USAGE

---

## TLDR (Executive Summary)

| Attribute | Value |
|-----------|-------|
| **Verdict** | ⚠️ NEEDS ATTENTION |
| **Critical Issues** | 0 |
| **Warnings** | 3 |

### Key Findings
- Class imbalance: 85% non-churn vs 15% churn
- Column LAST_CONTACT has 42% null values
- Data is 3 days fresh (acceptable)
- No obvious target leakage detected

### Top 3 Recommended Actions
1. Apply SMOTE or class weights to handle imbalance
2. Impute or drop LAST_CONTACT column
3. Consider feature engineering on USAGE patterns

---

## 1. Overall Assessment

The data is **suitable for a PoC model** but requires 
preprocessing before production. Main concerns are class 
imbalance and one high-null column.

**Readiness:** Needs preprocessing
**Caveats:** Address imbalance before production deployment

---

## 2. Table-Level Summary

### TELCO_DB.RAW.CUSTOMERS
| Metric | Value |
|--------|-------|
| Rows | 50,000 |
| Columns | 15 |
| High-Null Columns | 1 |

**Pros:**
- ✅ Good data volume (50K rows)
- ✅ All expected columns present
- ✅ No duplicate customer IDs

**Cons:**
- ⚠️ LAST_CONTACT: 42% null
- ⚠️ Class imbalance in CHURN_FLAG

### TELCO_DB.RAW.USAGE
**Pros:**
- ✅ Complete usage records
- ✅ Good date coverage (2 years)

**Cons:**
- ⚠️ Some extreme outliers in CALL_DURATION

---

## 3. Recommended Fixes

| Priority | Action | Columns | Rationale |
|----------|--------|---------|-----------|
| 1 | Handle imbalance | CHURN_FLAG | 85/15 split |
| 2 | Impute nulls | LAST_CONTACT | 42% missing |
| 3 | Cap outliers | CALL_DURATION | Extreme values |
```

---

**End of Guide**

*For questions or feedback, use `/feedback` in Cortex Code.*
