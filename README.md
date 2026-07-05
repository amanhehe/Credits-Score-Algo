# Credit Risk Scorecard (WOE/IV + Logistic Regression)

A from-scratch implementation of a bank-style credit scorecard — the same
methodology used by CIBIL/FICO bureau scores and Indian NBFC-P2P lenders
(LenDenClub, Faircent, KreditBee) — built end-to-end in Python.

**[Open the notebook →](./credit_scorecard.ipynb)**

## What this is

Given a portfolio of borrowers and whether each one defaulted, this project:

1. Engineers features using **Weight of Evidence (WOE) / Information Value (IV)**
   — the industry-standard technique for selecting and transforming variables
   in credit risk modeling
2. Trains a **logistic regression** model on the WOE-transformed features
3. Converts the model into a **300–900 points-based scorecard** using
   log-odds ("points to double the odds") scaling — the same scaling FICO
   and CIBIL-style scores use
4. Validates the model using the metrics actual credit risk teams use:
   **AUC, KS statistic, Gini coefficient**, and a calibration check
5. Buckets borrowers into **risk grades (A–E)** for pricing/approval decisions

## Results (on the synthetic portfolio used here)

| Metric | Value |
|---|---|
| Portfolio size | 3,000 borrowers |
| Default rate | 13.9% |
| AUC | 0.858 |
| KS statistic | 0.577 |
| Gini coefficient | 0.716 |

| Risk grade | Borrowers | Avg. score | Default rate |
|---|---|---|---|
| A | 484 | 792 | 0.6% |
| B | 679 | 711 | 2.5% |
| C | 683 | 651 | 6.0% |
| D | 685 | 587 | 18.7% |
| E | 469 | 505 | 48.4% |

Default rate rises monotonically from Grade A to Grade E — a ~80x gap in
observed default rate between the safest and riskiest grade — and predicted
default rates track actual default rates closely across score deciles, i.e.,
the model is both **discriminating** and **calibrated**.

## Why WOE/IV + logistic regression, not a more "accurate" ML model

This project deliberately uses the same simple, auditable method banks
actually deploy, rather than a black-box model that scores higher on paper.
Regulators require lenders to explain *why* a specific applicant got a
specific score — logistic regression on WOE-transformed variables makes that
traceable coefficient-by-coefficient. This trade-off (accuracy vs.
explainability/auditability) is discussed in the notebook and in the
accompanying research report.

## Data

No India-specific loan-level default dataset is publicly available, so this
project uses a **synthetic portfolio** with realistic feature correlations
(bureau score, income, utilization, late payments, tenure, employment type)
and a target default rate calibrated to the stressed-segment NPA range cited
in the accompanying report. This is stated explicitly — results should be
read as a demonstration of methodology, not a real-world performance claim.

## Background / context

This model was built as a practical companion to a research report,
*"Understanding Credit Score Algorithms"* (FinSearch, Finance Club), which
covers:
- How Indian NBFC-P2P lenders (LenDenClub, Faircent, KreditBee) underwrite
  borrowers
- India's 2021–2025 credit cycle and its effect on default rates
- RBI's Digital Lending Directions and the regulatory push for explainability
- Bias and fairness in credit scoring models

## Run it yourself

```bash
pip install numpy pandas matplotlib scikit-learn
jupyter notebook credit_scorecard.ipynb
```

## Structure

```
credit_scorecard/
├── credit_scorecard.ipynb   # full analysis, pre-run with outputs
├── requirements.txt
└── README.md
```
