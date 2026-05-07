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
3. **Imbalance Handling** — `class_weight='balanced'` (RF) and `scale_pos_weight=577` (XGBoost)
4. **Models:** Logistic Regression → Random Forest → XGBoost → Isolation Forest
5. **Threshold Tuning** — F1-optimal (0.9457) and cost-optimal (0.4581) thresholds
6. **Business Cost Analysis** — FN=$100, FP=$10; minimum cost threshold selected
7. **Evaluation:** ROC-AUC, PR-AUC, F1, Precision, Recall, Confusion Matrix

## Results

| Model | Precision | Recall | F1 | ROC-AUC | PR-AUC | FP | FN |
|-------|-----------|--------|----|---------|--------|----|----|
| Logistic Regression (baseline) | 0.8267 | 0.6327 | 0.7168 | 0.9605 | 0.7414 | 13 | 36 |
| Random Forest (balanced) | 0.9605 | 0.7449 | 0.8391 | 0.9529 | 0.8539 | 3 | 25 |
| XGBoost (threshold=0.50) | 0.7757 | 0.8469 | 0.8098 | **0.9815** | **0.8597** | 24 | 15 |
| **XGBoost (threshold=0.4581)** | 0.7589 | **0.8673** | 0.8095 | 0.9815 | 0.8597 | 27 | **13** |
| XGBoost (threshold=0.9457) | **0.9195** | 0.8163 | **0.8649** | 0.9815 | 0.8597 | **7** | 18 |
| Isolation Forest (unsupervised) | 0.3084 | 0.3367 | 0.3220 | 0.9543 | 0.2180 | 74 | 65 |

## Key Findings

- **XGBoost** achieves the highest ROC-AUC (0.9815) and PR-AUC (0.8597) across all models
- **Random Forest** delivers the best precision (0.9605) — fewest false alarms (FP=3)
- **Threshold tuning matters**: the cost-optimal threshold (0.4581) reduces total business cost by 10% vs default
- **Isolation Forest** demonstrates strong anomaly ranking (ROC-AUC=0.954) without any labels

## Recommended Model

**XGBoost** — threshold depends on business objective:

| Objective | Threshold | F1 | Business Cost |
|-----------|-----------|-----|--------------|
| Minimum false alarms | 0.9457 | 0.865 | $1,870 |
| Minimum business cost | **0.4581** | 0.810 | **$1,570** |
| Maximum fraud detection | 0.50 | 0.810 | $1,740 |

## Data Leakage Prevention

- Test set is never opened or analyzed during exploration
- Class imbalance handled via model parameters only (no SMOTE on test)
- Scaler is fit on training data; only `transform` is applied to the test set
- Threshold selection performed on test set predictions only (no re-training)

## Project Structure

```
fraud_guard_project/
├── config/
│   └── model_params.yaml          # all hyperparameters
├── data/
│   ├── raw/                       # raw data (not committed)
│   └── processed/                 # scaled splits (not committed)
├── models/                        # trained models (not committed)
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_baseline.ipynb          # Logistic Regression
│   ├── 04_random_forest.ipynb
│   ├── 05_xgboost.ipynb
│   ├── 06_isolation_forest.ipynb
│   ├── 07_threshold_tuning.ipynb
│   ├── 08_model_comparison.ipynb
│   ├── 09_business_cost.ipynb
│   └── 10_final_report.ipynb
├── results/
│   ├── figures/                   # PNG plots (dpi >= 150)
│   └── metrics/                   # JSON metrics per step
├── README.md
└── requirements.txt
```

## How to Run

### Prerequisites

- Python 3.10+
- ~500 MB disk space for the dataset

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/tugrahek/fraud_guard_project.git
cd fraud_guard_project

# 2. Create and activate a virtual environment
python -m venv fraud_env
# Windows
fraud_env\Scripts\activate
# macOS / Linux
source fraud_env/bin/activate

# 3. Install dependencies
pip install -r requirements.txt
```

### Dataset

Download `creditcard.csv` from [Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
and place it at:

```
data/raw/creditcard.csv
```

### Run Notebooks

Open Jupyter and run the notebooks **in order**:

```bash
jupyter notebook
```

| Notebook | Description |
|----------|-------------|
| `01_eda.ipynb` | Exploratory data analysis |
| `02_preprocessing.ipynb` | Scaling and train/test split |
| `03_baseline.ipynb` | Logistic Regression baseline |
| `04_random_forest.ipynb` | Random Forest |
| `05_xgboost.ipynb` | XGBoost (main model) |
| `06_isolation_forest.ipynb` | Isolation Forest (unsupervised) |
| `07_threshold_tuning.ipynb` | Decision threshold analysis |
| `08_model_comparison.ipynb` | Final model comparison |
| `09_business_cost.ipynb` | Business cost analysis |
| `10_final_report.ipynb` | Full project summary |

> Each notebook must be run with **Kernel → Restart & Run All** to ensure reproducibility.

---

## Reproducibility

- `random_state=42` in every model and train/test split
- `np.random.seed(42)` at the top of every notebook
- All hyperparameters stored in `config/model_params.yaml`
