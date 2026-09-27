# FinTrust Digital Bank — Predictive Risk Intelligence

**AnalystLab Africa Experience Lab Internship Programme — Batch E**
**Track:** Data Science | **Intern:** Takudzwa Jeffrey Gumbo (TJ)
**Project:** FinTrust Financial Intelligence & Digital Banking Support Solution

---

> ⚠️ **Synthetic data — educational use only.** FinTrust Digital Bank is a fictional organisation.
> Every customer, transaction and label in this repository is synthetic and created for a learning
> exercise. `Risk_Review_Flag` is a **synthetic educational label, not a real fraud determination**.
> Nothing here may be presented as a finding about real customers or used to make real financial
> decisions.

---

## Project overview

FinTrust reviews transaction risk reactively, which does not scale with volume. This track builds a
binary classification model that estimates the probability a transaction carries a risk-review flag,
turning an unordered backlog into a ranked review queue.

| Item | Value |
|---|---|
| Problem type | Binary classification |
| Target | `Risk_Review_Flag` (1 = flagged, 0 = not flagged) |
| Class balance | 2,352 / 9,648 — 19.6% positive (≈ 1:4.1) |
| Primary metric | ROC-AUC (target ≥ 0.75) |
| Binding constraint | Recall ≥ 0.70 on the positive class |
| Validation | Stratified 5-fold CV + grouped-split sensitivity check |
| **Current best (Week 2)** | **Logistic regression — CV ROC-AUC 0.665 ± 0.017** |

## Current results — Week 2 baseline

| Model | CV ROC-AUC | Val ROC-AUC | Precision | Recall | F1 |
|---|---|---|---|---|---|
| **Logistic Regression (selected)** | **0.665 ± 0.017** | 0.674 | 0.290 | 0.671 | 0.405 |
| Decision Tree | 0.652 ± 0.023 | 0.625 | 0.276 | 0.640 | 0.385 |
| Random Forest | 0.670 ± 0.016 | 0.677 | 0.432 | 0.252 | 0.318 |

At a tuned threshold of 0.488 the baseline reaches **recall 0.703** (meeting the Week 1 constraint) at
a precision of 0.283, and delivers a **top-decile lift of 1.96×** over random allocation of review effort.

**The 0.75 ROC-AUC target was not met.** This was anticipated in Week 1, before any modelling: the
descriptive profile showed customer-level attributes carrying almost no signal. Permutation importance
now confirms it — every customer-level feature contributes under 0.004 ROC-AUC while `Transaction_Type`
alone contributes 0.071. The shortfall is reported as a finding rather than tuned away.

## Hypothesis testing results

| ID | Hypothesis | Verdict | Effect |
|---|---|---|---|
| H1 | Amount vs customer's own mean | Supported | Cramér's V = 0.092; top quintile 26.8% |
| H2 | Shorter tenure → higher flag rate | **Rejected** | p = 0.123, V = 0.026 |
| H3 | Digital channels → higher flag rate | **Rejected** | p = 0.285, V = 0.024 |
| H4 | International and high-value | Supported | International +17.0pp; top amount quintile 27.0% |
| H5 | Overnight 00:00–05:59 | Supported | +12.4pp, z = 12.47 |

H2 and H3 were both **predicted to fail in Week 1** on the basis of descriptive profiling. They are
reported as results, and they are the justification for not spending Week 3 capacity on tenure- or
channel-derived features.

## Data

| Dataset | Rows | Columns |
|---|---|---|
| `FinTrust_Customer_Data.csv` | 1,500 | 12 |
| `FinTrust_Transaction_Data.csv` | 12,000 | 11 |
| `fintrust_modelling_dataset.csv` (generated) | 12,000 | 36 |

Clean one-to-many join on `Customer_ID` with full referential integrity in both directions.

## Repository structure

```
.
├── data/
│   ├── raw/                     # Supplied CSV files (not committed)
│   └── processed/               # Prepared modelling dataset (generated)
├── notebooks/
│   ├── 01_week1_data_profiling.ipynb
│   └── 02_week2_eda_and_baseline_model.ipynb
├── src/                         # Reusable helpers
├── models/                      # Model artefacts (from Week 3)
├── reports/
│   ├── week1/                   # Week 1 submission
│   ├── week2/                   # Week 2 submission
│   └── figures/                 # All saved figures
├── requirements.txt
└── README.md
```

## Getting started

```bash
git clone https://github.com/tgumbo07-ctrl/fintrust-risk-intelligence.git
cd fintrust-risk-intelligence

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

jupyter notebook notebooks/02_week2_eda_and_baseline_model.ipynb
```

Place the supplied CSV files in `data/raw/` as `FinTrust_Customer_Data.csv` and
`FinTrust_Transaction_Data.csv`. Both notebooks run top to bottom from a clean kernel with no manual
intervention. Random seed fixed at 42 throughout.

## Weekly progress

| Week | Theme | Status | Key output |
|---|---|---|---|
| 1 | Understand & Plan | ✅ Complete | Problem statement, target assessment, 12 candidate features, 5 hypotheses, 9-stage modelling plan, 10 data-quality observations |
| 2 | Analyse & Prepare | ✅ Complete | Prepared dataset, 13 figures, 5 hypotheses tested, 13 engineered features, baseline model, evaluation, interpretation |
| 3 | Model & Evaluate | ⏳ Planned | Hyperparameter tuning, XGBoost, redundancy removal, interaction terms, coefficient stability |
| 4 | Refine & Present | ⏳ Planned | Single test-set scoring, final report, recorded presentation |

## Methodological commitments

- The train/validation/test split is created **before** any exploratory analysis or feature engineering.
- All EDA and all fitted statistics use the **training split only**.
- The **test set has not been touched** and is scored once, in Week 4.
- All preprocessing sits inside scikit-learn `Pipeline` objects, so cross-validation refits per fold.
- `Transaction_Status` is **excluded** — the leakage test showed it adds only +0.007 ROC-AUC, which does not justify the exposure.
- `Gender` is excluded from the feature set on fairness grounds.
- Rejected hypotheses are reported as results, not omitted.

## Known limitations

- ROC-AUC of 0.665 falls short of the 0.75 target.
- Recall varies from 26.9% (bill payments) to 92.3% (cash withdrawals) — aggregate metrics conceal substantial segment variation.
- Three redundant amount encodings destabilise the logistic regression coefficient signs; interpretation uses permutation importance instead. Scheduled for removal in Week 3.
- The synthetic label has no supplied definition, so its meaning remains inferred.

## Licence and attribution

Produced for the AnalystLab Africa Experience Lab Internship Programme. All project data and business
context are fictional and supplied by AnalystLab Africa for educational use.

`#AnalystLabAfrica`
