# UCEC Tumor Grade Classification from Transcriptomic Data

Binary classification of endometrial tumor grade (Grade II- vs Grade III+) using high-dimensional RNA-seq gene expression features from the TCGA UCEC cohort.

---

## Problem

Uterine Corpus Endometrial Carcinoma (UCEC) tumor grading has direct clinical implications for treatment decisions. Grade II- tumors are considered lower risk, while Grade III+ tumors are more aggressive. This project asks: can we predict tumor grade from transcriptome-wide gene expression alone?

- **444 training samples** x ~18,000 Ensembl gene-level features
- **110 held-out test samples** (no labels)
- Primary metric: **F1-score** (class imbalance makes accuracy insufficient)

---

## Approach

| Step | What was done |
|---|---|
| Data integrity | Shape checks, ID alignment, missing value confirmation, class balance |
| Model selection | Stratified 5-fold CV comparison: Linear, Logistic, Ridge, LASSO |
| Hyperparameter tuning | Alpha grid search for Ridge and LASSO (7 log-spaced values) |
| Feature selection | SelectKBest (ANOVA F-test) sweep over k in {100, 500, 1k, 2k, 5k, 10k} |
| Leakage prevention | All preprocessing fit inside CV folds via `Pipeline` |
| Sanity check | Y-randomization (100 trials) to confirm learned signal is real |
| Interpretation | Gene ranking by logistic regression coefficient magnitude |
| Visualization | PCA and t-SNE 2D projections colored by tumor grade |

**Best model:** Logistic Regression with StandardScaler, trained on all 444 samples for final predictions.

---

## Results

| Model | CV Accuracy | CV F1-Score |
|---|---|---|
| Logistic Regression | *see notebook* | *see notebook* |
| Ridge Regression | *see notebook* | *see notebook* |
| Linear Regression | *see notebook* | *see notebook* |
| LASSO | *see notebook* | *see notebook* |
| Y-randomization baseline | ~chance | ~chance |

> Full numerical results are in the notebook outputs.

---

## Key Findings

- Logistic Regression consistently outperformed regression-based models on
