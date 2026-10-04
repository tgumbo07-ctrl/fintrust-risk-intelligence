# FinTrust Risk Intelligence

> **Synthetic-data / non-fraud disclaimer:** This project uses entirely synthetic educational data. `Risk_Review_Flag` is a synthetic review-prioritisation label and must not be described or interpreted as real fraud detection.

## Current result — Week 3

| Candidate | CV ROC-AUC | Validation ROC-AUC | Recall at tuned threshold | Status |
|---|---:|---:|---:|---|
| Logistic Regression (refined) | 0.663 ± 0.021 | 0.683 | 0.703 | **Retained for Week 4** |
| Gradient Boosting | 0.664 | 0.672 | 0.708 | Did not clear replacement rule |
| HistGradientBoosting | 0.658 | 0.667 | 0.703 | Did not clear replacement rule |

The Week 3 experiments support the earlier performance-ceiling hypothesis: stronger nonlinear learners did not produce a material ROC-AUC improvement. The refined logistic model remains the candidate because it is interpretable, meets the recall constraint after threshold tuning, and no challenger beat the incumbent by more than the incumbent cross-fold standard deviation. The 0.75 ROC-AUC target remains unmet.

## Weekly progress
| Week | Status | Main output |
|---|---|---|
| 1 | Complete | Problem framing, target assessment, hypotheses and modelling plan |
| 2 | Complete | EDA, feature engineering, hypothesis tests and baseline models |
| 3 | Complete | Feature refinement, additional models, validation and Week 4 candidate |
| 4 | Pending | Single test-set scoring, final refinement/documentation/presentation |

## Known limitations
The synthetic label has no supplied operational definition. Transaction-level features dominate; customer attributes add little signal. Important behavioural categories such as velocity, device fingerprint, counterparty/network context and meaningful geolocation are absent. Validation ROC-AUC remains below the pre-registered 0.75 target, and segment-level errors remain uneven. The test set remains untouched for Week 4.

## Repository
Weeks 1–3 preserve the fixed 70/15/15 stratified split (seed 42), training-only fitted statistics and scikit-learn pipelines. Accuracy is reported but excluded from model selection because the positive class is imbalanced and the operational constraint is recall ≥ 0.70.

**Synthetic-data / non-fraud disclaimer:** This project uses entirely synthetic educational data. `Risk_Review_Flag` is a synthetic review-prioritisation label and is not real fraud detection.
