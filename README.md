# UCEC Tumor Grade Classification from Transcriptomic Data

Binary classification of endometrial tumor grade (Grade II− vs Grade III+) using high-dimensional RNA-seq gene expression features from the TCGA UCEC cohort.

---

## Problem

Uterine Corpus Endometrial Carcinoma (UCEC) tumor grading — Grade II− (low-risk) vs Grade III+ (high-risk) — has direct clinical implications for treatment decisions. This project asks: **can we predict tumor grade from transcriptome-wide gene expression alone?**

- **444 training samples** × ~18,000 Ensembl gene-level features
- **110 held-out test samples** (no labels)
- Primary metric: **F1-score** (class imbalance makes accuracy insufficient)

---

## Approach

| Step | What was done |
|---|---|
| Data integrity | Shape checks, ID alignment, missing value confirmation, class balance |
| Model selection | Stratified 5-fold CV comparison: Linear, Logistic, Ridge, LASSO |
| Hyperparameter tuning | Alpha grid search for Ridge and LASSO (7 log-spaced values) |
| Feature selection | SelectKBest (ANOVA F-test) sweep over k ∈ {100, 500, 1k, 2k, 5k, 10k} |
| Leakage prevention | All preprocessing (scaling, feature selection) fit inside CV folds via `Pipeline` |
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

- Logistic Regression consistently outperformed regression-based models on F1
- Y-randomization confirmed the model is learning real transcriptomic signal, not statistical artifacts
- Top genes by coefficient magnitude include **PRLH** (prolactin-related, known endometrial cancer role) and **NTSR1/NTS** (neurotensin axis, reported in hormone-sensitive cancers) — partial biological recapitulation of known UCEC biology

---

## Repository Structure

```
ucec-tumor-grade-classification/
├── notebooks/
│   └── ucec_tumor_grade_classification.ipynb   # Main analysis notebook
├── data/
│   ├── train_X.csv        # Training features (not tracked — see note below)
│   ├── train_y.csv        # Training labels (not tracked)
│   └── test_X.csv         # Test features (not tracked)
├── results/
│   ├── gene_importance.csv          # Ranked gene coefficients from final model
│   ├── submission_final.csv         # Final test set predictions
│   └── patient_visualization.png   # PCA + t-SNE visualization
├── requirements.txt
├── .gitignore
└── README.md
```

> **Note on data:** The raw TCGA feature matrices (`train_X.csv`, `test_X.csv`, `train_y.csv`) are not tracked in this repo due to size. They can be obtained from the [TCGA data portal](https://portal.gdc.cancer.gov/) or the original course competition.

---

## Visualizations

### PCA & t-SNE of Training Samples by Tumor Grade
![Patient Visualization](results/patient_visualization.png)

Classes overlap considerably in 2D projections — consistent with the high-order nature of tumor grade as a phenotype. The linear classifier finds a useful boundary in the full ~18K-dimensional space despite the apparent lack of 2D separation.

---

## Setup & Reproduction

```bash
git clone https://github.com/<your-username>/ucec-tumor-grade-classification.git
cd ucec-tumor-grade-classification
pip install -r requirements.txt
```

Place `train_X.csv`, `train_y.csv`, and `test_X.csv` in the `data/` directory, then open the notebook:

```bash
jupyter notebook notebooks/ucec_tumor_grade_classification.ipynb
```

---

## Dependencies

See `requirements.txt`. Core stack: `scikit-learn`, `pandas`, `numpy`, `matplotlib`.

---

## Review

- End-to-end ML pipeline design in scikit-learn
- Stratified cross-validation and Pipeline-based leakage prevention
- Feature selection in high-dimensional (n << p) biological data
- Model interpretability via coefficient analysis
- Dimensionality reduction (PCA, t-SNE) for exploratory data analysis
- Biological validation of model outputs against domain literature
