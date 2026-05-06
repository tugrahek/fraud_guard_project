# FraudGuard — Credit Card Fraud Detection

Academic project developed for a university Machine Learning course.

## Problem Statement

Detecting fraudulent transactions in credit card data from European banks.
The dataset is highly imbalanced: only 492 out of 284,807 transactions (0.17%) are fraud.

## Dataset

- **Source:** [Kaggle — Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- **File:** `data/raw/creditcard.csv`
- **Features:** V1–V28 (anonymized via PCA), `Amount`, `Time`
- **Target:** `Class` (0 = legitimate, 1 = fraud)

## Methodology

1. **EDA** — Class distribution, statistical summaries, correlation analysis (train set only)
2. **Preprocessing** — StandardScaler (fit on train, transform on test), 80/20 stratified split
3. **Imbalance Handling** — SMOTE applied to training set only
4. **Models:** Logistic Regression (baseline) → Random Forest → XGBoost
5. **Evaluation:** ROC-AUC, PR-AUC, F1, Precision, Recall, Confusion Matrix

## Data Leakage Prevention

- Test set is never opened or analyzed during exploration
- SMOTE is applied only to the training set
- Scaler is fit on training data; only `transform` is applied to the test set

## Project Structure

```
fraud_guard_project/
├── config/
│   └── model_params.yaml
├── data/
│   ├── raw/          # raw data (not committed)
│   └── processed/    # processed data (not committed)
├── models/           # trained models (not committed)
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_baseline.ipynb
│   ├── 04_smote.ipynb
│   ├── 05_random_forest.ipynb
│   ├── 06_xgboost.ipynb
│   └── 07_comparison.ipynb
├── results/
│   ├── figures/      # PNG plots (dpi >= 150)
│   └── metrics/      # CSV / JSON metrics
├── README.md
└── requirements.txt
```

## Reproducibility

- `random_state=42` in every model and train/test split
- `np.random.seed(42)` at the top of every notebook
- All hyperparameters stored in `config/model_params.yaml`
