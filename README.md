# FinTrust Digital Bank — Predictive Risk Intelligence

**AnalystLab Africa Experience Lab Internship Programme — Batch E**
**Track:** Data Science | **Intern:** Takudzwa Jeffrey Gumbo (TJ)
**Project:** FinTrust Financial Intelligence & Digital Banking Support Solution

---

> ⚠️ **Synthetic data — educational use only.** FinTrust Digital Bank is a fictional organisation.
> Every customer, transaction and label in this repository is synthetic and created for a learning
> exercise. `Risk_Review_Flag` is a **synthetic educational label, not a real fraud determination**.
> Nothing in this repository may be presented as a finding about real customers, real transactions
> or real financial crime, and nothing here constitutes financial, investment or legal advice.

---

## Project overview

FinTrust Digital Bank reviews transaction risk reactively, which does not scale with transaction
volume. This track builds a **binary classification model** that estimates the probability that a
transaction carries a risk-review flag, converting an unordered review backlog into a ranked queue
so that finite review capacity is spent where concern is most likely.

| Item | Value |
|---|---|
| Problem type | Binary classification |
| Target | `Risk_Review_Flag` (1 = flagged, 0 = not flagged) |
| Class balance | 2,352 positive / 9,648 negative — 19.6% positive (≈ 1:4.1) |
| Primary metric | ROC-AUC (target ≥ 0.75) |
| Binding constraint | Recall on the positive class ≥ 0.70 |
| Secondary metrics | PR-AUC, precision, F1, confusion matrix |
| Validation | Stratified 5-fold cross-validation + grouped split sensitivity check |
| Candidate models | Logistic regression (baseline), decision tree, random forest, XGBoost |

## Data

| Dataset | Rows | Columns | Notes |
|---|---|---|---|
| `FinTrust_Customer_Data.csv` | 1,500 | 12 | No missing values; one row per customer |
| `FinTrust_Transaction_Data.csv` | 12,000 | 11 | 1 Jan – 31 Mar 2026; contains the target |

The datasets join one-to-many on `Customer_ID` with full referential integrity in both directions
(no orphan transactions, no customers without transactions).

## Repository structure

```
.
├── data/
│   ├── raw/                     # Supplied CSV files, unmodified
│   └── processed/               # Generated analytical tables (not committed)
├── notebooks/
│   └── 01_week1_data_profiling.ipynb
├── src/                         # Reusable loading, feature and evaluation helpers
├── models/                      # Serialised model artefacts (from Week 3)
├── reports/
│   ├── week1/                   # Week 1 submission document (DOCX + PDF)
│   └── figures/                 # All saved figures
├── requirements.txt
└── README.md
```

## Getting started

```bash
git clone <repository-url>
cd fintrust-risk-intelligence

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

jupyter notebook notebooks/01_week1_data_profiling.ipynb
```

Place the supplied CSV files in `data/raw/` as `FinTrust_Customer_Data.csv` and
`FinTrust_Transaction_Data.csv`. The notebook runs top to bottom from a clean kernel with no manual
intervention. The random seed is fixed at 42 throughout.

## Weekly progress

| Week | Theme | Status | Deliverables |
|---|---|---|---|
| 1 | Understand & Plan | ✅ Complete | Problem statement, target assessment, 12 candidate features, 5 hypotheses, 9-stage modelling plan, data-quality observations, risk register |
| 2 | Explore & Prepare | ⏳ Planned | Full EDA, hypothesis testing, feature pipeline, logistic regression baseline, imbalance comparison, leakage test |
| 3 | Model & Evaluate | ⏳ Planned | Tree-based models, tuning, ROC/PR analysis, threshold selection, error analysis, feature importance |
| 4 | Refine & Present | ⏳ Planned | Single test-set scoring, final report, recorded presentation |

## Week 1 key findings

Week 1 is a planning week — no model has been trained. Profiling established that:

1. **The target is usable.** Binary, fully populated across 12,000 records, moderately imbalanced at
   19.6% positive. Accuracy is excluded as a metric; `class_weight="balanced"` is the first response.
2. **The join is clean.** One-to-many on `Customer_ID`, no orphans, 1–20 transactions per customer.
3. **Signal appears to sit at the transaction level.** Flag rates vary materially across amount, hour,
   international status and transaction type, but are close to flat across customer segment, account
   type, tenure and income band. A modest performance ceiling is expected and will be reported
   honestly rather than tuned away.
4. **The amount relationship is non-linear** — flat across the lower four quintiles, then rising to
   26.8% in the top quintile and 40.5% above ₦200,000.
5. **Ten data-quality issues are documented**, including uniform temporal generation (exactly 500
   transactions per hour), internally inconsistent `Channel`/`Device_Type` pairings, and a possible
   leakage path through `Transaction_Status`.

## Methodological commitments

- The train/validation/test split is created **before** exploratory analysis informs feature choice.
- The test set is scored **once**, in Week 4.
- All engineered features are computed inside pipelines fitted on training folds only.
- Resampling, if used, is applied **inside** cross-validation folds — never before the split.
- `Gender` is excluded from the feature set on fairness grounds.
- Rejected hypotheses are reported as results, not omitted.

## Licence and attribution

Produced for the AnalystLab Africa Experience Lab Internship Programme. All project data and
business context are fictional and supplied by AnalystLab Africa for educational use.

`#AnalystLabAfrica`
