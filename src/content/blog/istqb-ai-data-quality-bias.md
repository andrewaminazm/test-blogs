---
title: ISTQB AI Testing — Data Quality and Bias Testing
description: "How to test training data, test datasets, and model fairness. ISTQB CT-AI concepts applied: data quality criteria, balance, labeling, and bias detection."
pubDate: 2025-02-20
tags: [ai, istqb, data-quality, bias, ml-testing]
aiTwist: tutorial
---

Data is the foundation of ML models: bad data leads to bad models. The ISTQB AI Testing syllabus devotes significant attention to **data quality** and **bias** because these are where many AI failures originate. Here's how to test both in practice.

## 1. Data quality criteria (ISTQB)

ISTQB defines these data quality characteristics that testers should verify:

| Characteristic | What to check |
|----------------|---------------|
| **Completeness** | Missing values, null fields, incomplete records |
| **Correctness** | Labels accurate, values in valid range, no corruption |
| **Consistency** | Same entity described consistently across sources |
| **Timeliness** | Data is current; no stale records that don't reflect reality |
| **Balance** | Class distribution not heavily skewed toward one outcome |
| **Uniqueness** | No duplicate records that could leak between train/test |
| **Relevance** | Features used actually help predict the target |

A tester's job is to **define checks** for each, run them on datasets, and flag issues **before** they affect the model.

## 2. Practical data testing approach

**Step 1 — Profile the dataset**

Before running any model, run data profiling:
- Count rows, count nulls per column, value ranges.
- Distribution of the target label (balanced? 90/10? 99/1?).
- Unique values per categorical column (unexpectedly high cardinality?).

Ask an LLM: "Given this dataset schema and sample: [paste]. What data quality issues should I check for before using this data to train a model? Output as a prioritized checklist."

**Step 2 — Check for train/test leakage**

Leakage means the model accidentally sees test data during training, giving artificially high accuracy:
- Timestamps: train data must be earlier than test data for time-series models.
- Duplicate rows: ensure the same record doesn't appear in both splits.
- Feature leakage: check that no feature column contains information derived from the target.

**Step 3 — Validate labels**

For supervised models, labels are ground truth:
- Sample and manually review labels (e.g. 50–100 items). Are they correct?
- Check inter-annotator agreement if humans labeled the data.
- Flag any label categories with very few examples (< 20–30) — model may not learn them reliably.

## 3. Bias testing

Bias in AI is when the model performs differently (usually worse) for certain groups. ISTQB CT-AI defines bias types:

- **Training data bias** — The data doesn't represent all groups equally (e.g. mostly male faces in a facial recognition dataset).
- **Label bias** — Human annotators applied labels inconsistently across groups.
- **Measurement bias** — The feature used as a proxy correlates with a protected attribute (e.g. ZIP code as proxy for race).
- **Aggregation bias** — A single model is used for groups that have different patterns.

**How to test for bias:**

1. **Slice the test set by group** — e.g. by gender, age group, region, or any protected attribute. Calculate accuracy, precision, recall for each slice.
2. **Compare performance across slices** — Is accuracy 95% for group A and 72% for group B? That's a bias signal.
3. **Use AI fairness metrics** — Demographic parity, equalized odds, individual fairness (similar inputs get similar outputs). ISTQB expects testers to know these exist and when to apply them.
4. **Test with representative inputs** — If you're testing a chatbot, include inputs from different dialects, languages, or cultural contexts.

**Practical prompt:**

```
We have a classification model: [describe task, e.g. loan approval, sentiment analysis]. Our test set has [N] samples. We want to test for bias.

Suggest:
1. Which groups or slices to evaluate (based on likely protected attributes for this use case)
2. Which fairness metrics to calculate (demographic parity, equalized odds, etc.) and what "acceptable" gap looks like
3. How to structure a bias test report for stakeholders (what to include, what a "flag" vs "pass" looks like)
4. One concrete test: which inputs to construct or data slice to compare

Output as a short guide. No code—strategy only.
```

## 4. Dataset testing in CI/CD

Data quality shouldn't be a one-time check. Automate it:

- **Schema validation** — Check column names, types, value ranges on every new data batch (tools: Great Expectations, dbt tests, Pandera).
- **Distribution drift** — Alert when incoming data distribution shifts significantly from training data (tools: Evidently AI, Whylogs, Alibi Detect).
- **Label coverage** — Ensure all expected classes appear in new data before retraining.

Run these checks in your ML pipeline (e.g. before or after the training step in CI) and fail the pipeline or alert if thresholds are crossed.

## 5. Quick win: data quality checklist prompt

Before any model goes to testing or production:

> "We're about to use this dataset for [task]. Schema: [paste]. Provide a data quality checklist with 8–10 checks covering completeness, correctness, balance, leakage, and label quality. For each check: what to verify, how (query/tool), and what result would cause us to flag the data as not ready. Output as a table."

For the next step — testing the model itself and adversarial inputs — see [ISTQB AI Testing — Model Testing and Adversarial Inputs](/blog/istqb-ai-model-testing).
