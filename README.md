# FraudGuard — Kredi Kartı Dolandırıcılığı Tespiti

Üniversite Makine Öğrenmesi dersi kapsamında geliştirilen akademik proje.

## Problem Tanımı

Avrupa bankaları tarafından gerçekleştirilen kredi kartı işlemlerinde dolandırıcılık tespiti.
Veri seti son derece dengesiz: 284.807 işlemden yalnızca 492'si (%0,17) dolandırıcılık.

## Veri Seti

- **Kaynak:** [Kaggle — Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- **Dosya:** `data/raw/creditcard.csv`
- **Özellikler:** V1–V28 (PCA ile anonim), `Amount`, `Time`
- **Hedef:** `Class` (0 = normal, 1 = fraud)

## Metodoloji

1. **EDA** — Sınıf dağılımı, istatistiksel özetler, korelasyon analizi (yalnızca train seti)
2. **Ön İşleme** — StandardScaler (train fit, test transform), train/test split (80/20, stratified)
3. **Dengesizlik Giderme** — SMOTE (yalnızca train setine)
4. **Modeller:** Logistic Regression (baseline) → Random Forest → XGBoost
5. **Değerlendirme:** ROC-AUC, PR-AUC, F1, Precision, Recall, Confusion Matrix

## Temel Kural: Data Leakage Önleme

- Test seti EDA aşamasında açılmaz
- SMOTE yalnızca training verisine uygulanır
- Scaler train verisinde fit edilir, test verisine transform uygulanır

## Proje Yapısı

```
fraud_guard_project/
├── config/
│   └── model_params.yaml
├── data/
│   ├── raw/          # ham veri (git'e eklenmez)
│   └── processed/    # işlenmiş veri (git'e eklenmez)
├── models/           # eğitilmiş modeller (git'e eklenmez)
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_baseline.ipynb
│   ├── 04_smote.ipynb
│   ├── 05_random_forest.ipynb
│   ├── 06_xgboost.ipynb
│   └── 07_comparison.ipynb
├── results/
│   ├── figures/      # PNG grafikler (dpi >= 150)
│   └── metrics/      # CSV / JSON metrikler
├── CLAUDE.md
├── PROGRESS.md
├── README.md
└── requirements.txt
```

## Tekrar Üretilebilirlik

- `random_state=42` tüm model ve split işlemlerinde
- `np.random.seed(42)` her notebook'un başında
- Tüm hiperparametreler `config/model_params.yaml` içinde
