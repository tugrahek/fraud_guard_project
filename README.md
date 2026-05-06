# FraudGuard

> Credit card fraud detection on highly imbalanced transaction data using
> comparative supervised and unsupervised machine learning models.

[![Python](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.4+-orange.svg)](https://scikit-learn.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-2.0+-green.svg)](https://xgboost.readthedocs.io/)
[![License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](LICENSE)

---

## Overview

This project develops a machine learning system for detecting fraudulent
credit card transactions on the public *Credit Card Fraud Detection*
dataset, where positive examples represent only **0.172%** of all records.

The work compares four different classification approaches under
explicit handling of class imbalance, and evaluates them using
metrics appropriate for skewed distributions (PR-AUC, F1, recall)
rather than accuracy alone. A business cost analysis translates
model performance into a financial perspective.

---

## Dataset

| Property | Value |
|---|---|
| Source | [Kaggle — `mlg-ulb/creditcardfraud`](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) |
| Provider | Université Libre de Bruxelles |
| License | Open Data Commons (ODbL) |
| Size | 284,807 transactions |
| Positives | 492 (0.172%) |
| Features | 30 (V1–V28 PCA + Time + Amount) |
| Missing values | None |

The `V1`–`V28` features are anonymized via PCA for privacy reasons,
which limits domain-specific feature engineering but preserves the
mathematical structure of the data.

---

## Methodology

### Preprocessing
- `RobustScaler` applied to `Time` and `Amount` (V-features are already standardized)
- Stratified split: 70% train / 15% validation / 15% test, `random_state=42`
- **SMOTE applied to training data only** to prevent data leakage

### Models compared
| Model | Type | Imbalance handling |
|---|---|---|
| Logistic Regression | Linear, supervised | `class_weight='balanced'`, baseline |
| Random Forest | Ensemble, supervised | `class_weight='balanced'` |
| XGBoost | Gradient boosting, supervised | `scale_pos_weight` |
| Isolation Forest | Tree-based, unsupervised | `contamination=0.00172` |

### Evaluation
Accuracy is **not used** as a primary metric due to class imbalance.
Reported metrics for each model:

- Precision, Recall, F1-score
- ROC-AUC alongside **PR-AUC** (more informative for imbalanced data)
- Confusion matrix
- Feature importance (for tree-based models)

A **threshold tuning** step replaces the default 0.5 cutoff with an
F1-optimal threshold derived from the precision-recall curve.
A **business cost analysis** weighs false negatives (missed fraud)
against false positives (customer friction) to derive a cost-optimal
operating point.

---

## Project Structure

```
fraud-detection/
├── data/
│   ├── raw/                  # Original Kaggle CSV (gitignored)
│   └── processed/            # Train/val/test splits (gitignored)
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_baseline_logistic.ipynb
│   ├── 04_random_forest.ipynb
│   ├── 05_xgboost.ipynb
│   ├── 06_isolation_forest.ipynb
│   ├── 07_threshold_tuning.ipynb
│   └── 08_final_evaluation.ipynb
├── src/
│   ├── data/                 # Loading and splitting
│   ├── features/             # Scaling, transformations
│   ├── models/               # Model wrappers
│   └── evaluation/           # Metrics, plotting, cost analysis
├── models/                   # Trained .pkl files (gitignored)
├── results/
│   ├── figures/              # PNG figures for the report
│   └── metrics/              # CSV/JSON metric tables
├── config/
│   └── model_params.yaml
├── docs/
│   └── methodology.md
├── tests/
├── requirements.txt
├── LICENSE
└── README.md
```

---

## Setup

### Prerequisites
- Python 3.11+
- Git
- Kaggle account (for dataset download)

### Installation

```bash
git clone https://github.com/[USERNAME]/fraud-detection.git
cd fraud-detection

python -m venv fraud_env
source fraud_env/bin/activate          # Linux/macOS
# fraud_env\Scripts\activate            # Windows

pip install -r requirements.txt
```

### Dataset download

Using the Kaggle CLI (recommended):

```bash
pip install kaggle
# Place kaggle.json from kaggle.com → Account → API in ~/.kaggle/

kaggle datasets download -d mlg-ulb/creditcardfraud
unzip creditcardfraud.zip -d data/raw/
```

Or download manually from the
[dataset page](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
and place `creditcard.csv` under `data/raw/`.

### Verify
```bash
python -c "import pandas, numpy, sklearn, xgboost, imblearn; print('OK')"
```

---

## Usage

Run the notebooks sequentially:

```bash
jupyter notebook
```

| # | Notebook | Purpose |
|---|---|---|
| 1 | `01_eda.ipynb` | Class distribution, feature analysis |
| 2 | `02_preprocessing.ipynb` | Scaling, split, SMOTE |
| 3 | `03_baseline_logistic.ipynb` | Baseline model |
| 4 | `04_random_forest.ipynb` | Random Forest |
| 5 | `05_xgboost.ipynb` | XGBoost (main model) |
| 6 | `06_isolation_forest.ipynb` | Anomaly detection |
| 7 | `07_threshold_tuning.ipynb` | Threshold optimization |
| 8 | `08_final_evaluation.ipynb` | Cross-model comparison |

To run all at once:
```bash
jupyter nbconvert --to notebook --execute notebooks/*.ipynb
```

To run tests:
```bash
pytest tests/ -v
```

---

## Results

> Results will be populated upon project completion.

| Model | Precision | Recall | F1 | ROC-AUC | PR-AUC |
|---|---|---|---|---|---|
| Logistic Regression | — | — | — | — | — |
| Random Forest | — | — | — | — | — |
| XGBoost | — | — | — | — | — |
| Isolation Forest | — | — | — | — | — |

Generated figures and metric tables are stored under `results/`.

---

## Tech Stack

- **Python 3.11+**
- `pandas`, `numpy` — data handling
- `scikit-learn` — models and metrics
- `xgboost` — gradient boosting
- `imbalanced-learn` — SMOTE
- `matplotlib`, `seaborn` — visualization
- `jupyter` — notebook environment

---

## Limitations

- Features `V1`–`V28` are PCA-anonymized; domain interpretation is limited.
- The dataset spans only two days (September 2013, Europe);
  seasonal patterns cannot be modeled.
- A single train/val/test split is used instead of k-fold cross-validation
  due to compute cost.

---


## License

Released under the [MIT License](LICENSE).
The dataset is distributed under ODbL by Université Libre de Bruxelles.

---