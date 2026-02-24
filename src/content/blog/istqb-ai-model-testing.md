---
title: ISTQB AI Testing — Model Testing, Metrics, and Adversarial Inputs
description: "How to evaluate ML model performance, choose the right metrics, write model-level tests, and design adversarial and robustness test cases."
pubDate: 2025-02-22
tags: [ai, istqb, model-testing, adversarial, ml-testing]
aiTwist: tutorial
---

Once data quality is verified, the next layer of ISTQB AI testing is **model testing**: evaluating whether the model actually works — correctly, robustly, and safely. This covers performance metrics, behavioral testing, and adversarial inputs.

## 1. Model performance metrics

ISTQB expects testers to understand and apply ML metrics — not just "accuracy." Choose based on the problem type:

**Classification:**

| Metric | When to use |
|--------|------------|
| **Accuracy** | Balanced classes, equal cost of errors |
| **Precision** | False positives are costly (e.g. spam filter blocking real emails) |
| **Recall** | False negatives are costly (e.g. fraud detection missing fraud) |
| **F1 score** | Imbalanced classes, want balance of precision and recall |
| **AUC-ROC** | Ranking quality, threshold-independent comparison |
| **Confusion matrix** | Full picture of TP, FP, TN, FN per class |

**Regression / ranking / generative:**
- **MAE, RMSE** for regression (prediction error magnitude)
- **NDCG, MRR** for ranking (search or recommendation)
- **BLEU, ROUGE** for text generation quality
- **Perplexity** for language models

**Tester's role:** Define **acceptance thresholds** before seeing results. E.g. "Recall must be ≥ 0.90 for fraud class; precision ≥ 0.70 acceptable." Don't adjust thresholds after seeing the model output.

## 2. Model-level test cases

ISTQB describes model testing as analogous to unit testing for code. Examples:

- **Happy path** — Representative input from each class gets the correct prediction.
- **Boundary inputs** — Inputs near the decision boundary (e.g. low-confidence predictions) behave as expected.
- **Minimum capability** — Model achieves defined metrics on the holdout test set.
- **Regression** — After retraining, model doesn't perform worse than the previous version on key slices.
- **Latency** — Inference time within budget for the use case (e.g. &lt;100ms for real-time).

Write these as **executable tests** in your ML pipeline (e.g. pytest + model artifact) so they run on every training run.

## 3. Behavioral testing (black-box ML testing)

Inspired by **CheckList** (Microsoft Research) and ISTQB CT-AI, behavioral tests define **capabilities** the model must have, tested with many inputs:

- **Minimum functionality** — Basic inputs always get correct output (e.g. "Hello" → positive sentiment).
- **Invariance** — Changing irrelevant parts of input doesn't change output (e.g. changing name in a review shouldn't change sentiment).
- **Directional expectations** — Changing one attribute should change output in a predictable direction (e.g. "I love this" → more positive than "It's okay").

**Prompt to generate behavioral tests:**

```
We have a [e.g. sentiment classifier / loan approval model / chatbot]. Core capability: [describe].

Generate 5 behavioral test cases:
1. Minimum functionality (basic expected inputs → expected outputs)
2. Invariance (what we can change without changing the output)
3. Directional (what changes should predictably change the output, and how)
4. Edge case (empty input, very long input, ambiguous)
5. Regression (one specific input that must always work after retraining)

For each: input example | expected output or property | how to verify. Table.
```

## 4. Adversarial and robustness testing

ISTQB CT-AI includes adversarial testing — deliberately crafted inputs that cause models to fail or behave unexpectedly:

**For ML classifiers:**
- **Adversarial examples** — Small, human-imperceptible perturbations that flip the model's prediction (classic: pixel noise on images).
- **Out-of-distribution** — Inputs the model was never trained on (e.g. an image classifier shown a drawing instead of a photo).
- **Edge cases** — Unusual but valid inputs (e.g. very short text for NLP, missing optional fields for tabular).

**For LLM-powered features:**
- **Prompt injection** — User input overrides system instructions.
- **Jailbreaks** — Input designed to bypass safety guidelines.
- **Hallucination triggers** — Ask for facts the model doesn't know; check if it fabricates or admits uncertainty.
- **Context overflow** — Very long inputs that exceed context window or degrade quality.

**Adversarial test prompt:**

```
We're testing a [describe AI feature: model type, input type, use case].

Generate 8 adversarial/robustness test cases:
- 2 out-of-distribution (inputs the model likely wasn't trained on)
- 2 boundary/edge (minimum valid, maximum valid, or ambiguous)
- 2 adversarial (slight change to input that might flip output)
- 2 safety (inputs that might trigger harmful or incorrect output)

For each: input description | expected behavior | failure signal. No harmful content.
```

## 5. Model monitoring as testing

ISTQB emphasizes that testing doesn't stop at deployment. Key monitoring tests:

- **Prediction drift** — Distribution of model outputs shifts over time (e.g. more "reject" than before). Alert and investigate.
- **Data drift** — Incoming feature distributions shift from training data. Trigger retraining evaluation.
- **Performance monitoring** — Track accuracy/precision/recall on labeled production samples (if feedback available).
- **Latency and error rate** — Model serving health.

Define **alert thresholds** for each and treat threshold breaches as test failures: investigate, fix, re-evaluate.

## 6. Summary checklist

| Phase | Key tests |
|-------|-----------|
| Before training | Data quality, balance, leakage, label check |
| After training | Performance metrics vs thresholds, behavioral tests, bias by slice |
| In system testing | Integration with app, API contract, latency, adversarial |
| Post-deployment | Drift detection, performance monitoring, alert thresholds |

For the full picture of AI testing quality characteristics, see [ISTQB AI Testing Fundamentals](/blog/istqb-ai-testing-fundamentals). For data quality and bias, see [ISTQB AI Testing — Data Quality and Bias](/blog/istqb-ai-data-quality-bias). For prompts to use today, see the [Prompt library](/prompts/).
