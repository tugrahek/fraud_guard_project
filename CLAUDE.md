# FraudGuard — Claude Code Çalışma Kuralları

## Proje Hakkında
Credit Card Fraud Detection — sayısal işlem verilerinden dolandırıcılık
tespiti için Makine Öğrenmesi tabanlı sınıflandırma sistemi.

**Dataset:** Kaggle "Credit Card Fraud Detection" (mlg-ulb/creditcardfraud)
- 284.807 işlem, 492 fraud (%0.172)
- V1–V28 PCA feature'ları + Time + Amount + Class

**Hedef:** Class imbalance handle edilerek, en az 4 farklı model
karşılaştırılarak, business cost perspektifinden değerlendirilen
bir fraud detection sistemi geliştirmek.

## Bu Dosya Hakkında — Önemli

**CLAUDE.md ve PROGRESS.md `.gitignore` ile gizlenmiştir.**
Bu iki dosya GitHub'da görünmez, sadece lokal disk'te bulunur.
Bu yüzden:

- README.md projenin TEK görünür dokümantasyonudur
- CLAUDE.md ve PROGRESS.md çalışma araçlarıdır, public değildir
- Bu dosyalar bilgisayar bozulursa kaybolur — Dropbox/OneDrive gibi
  cloud bir klasörde tutulması önerilir

Her oturum başında `CLAUDE.md`, `PROGRESS.md` ve `README.md`'yi oku.
Okumadan hiçbir şey yapma.

## ZORUNLU ÇALIŞMA AKIŞI

Her geliştirme adımından önce şunları yap:

1. **PLAN** — Ne yapacağını açıkla:
   - Hangi dosyalar oluşturulacak/değiştirilecek
   - Metodolojik kararlar ve gerekçeler
   - Edge case'ler ve riskler (özellikle data leakage)

2. **ONAY BEKLE** — "Onaylıyor musun?" diye sor.
   Kutay "devam et", "onayla" veya "evet" demeden KESİNLİKLE kod yazma.

3. **KODLA** — Onay gelince yaz.

4. **RAPOR ET** — Ne yaptığını özetle, hangi metriklerin
   üretildiğini ve neyi gösterdiğini belirt.

## Model Kullanım Rehberi
- Planlama, mimari, model seçimi: Opus (/model claude-opus-4-7)
- Rutin kodlama, EDA, görselleştirme: Sonnet (/model claude-sonnet-4-6)
- Oturum başında default: Sonnet
- Karmaşık metodoloji veya bug çıktığında: Opus'a geç, iş bitince Sonnet'e dön

## Kod Standartları

### Python
- Type hint: her fonksiyonda zorunlu
- Docstring: her public class ve metodda (parametreler, dönüş, özel durumlar)
- Random seed: her model ve split için `random_state=42` zorunlu
- Reproducibility: `numpy.random.seed(42)` notebook başında set edilir
- Max dosya: 400 satır
- Max fonksiyon: 40 satır
- Dataframe sütun isimleri snake_case
- Test yazmadan model commit yapma

### Notebook Standartları
- Her notebook'un en üstünde: amaç, input, output, çalıştırma süresi
- Markdown hücrelerle bölümlendir (1. EDA, 2. Preprocessing, ...)
- Her grafik mutlaka başlık + eksen etiketi + legend içerir
- Notebook çıktıları commit'lenir (görselleri kaybetme)
- Pickle dosyaları `models/` altına kaydedilir

## ML / Veri Mühendisliği Kuralları

Bunlar projenin teknik düzgünlüğü için kritik —
ihlali raporda metodolojik hata sayılır:

### Veri Sızıntısı Önleme (Data Leakage)
- SMOTE / oversampling SADECE train setine uygulanır
- Scaler'lar SADECE train üzerinde fit edilir, test'e transform edilir
- Test seti analiz/keşif aşamasında dahi açılmaz
- Cross-validation içinde resampling pipeline ile yapılır

### Train/Test Split
- `stratify=y` parametresi zorunlu
- `random_state=42` her zaman set edilir
- Train %70 / Validation %15 / Test %15
- Split bir kez yapılır, tüm modeller aynı split üzerinde değerlendirilir

### Sınıf Dengesizliği (Class Imbalance)
- ROC-AUC tek başına raporlanmaz, mutlaka PR-AUC ile birlikte
- Accuracy metric olarak rapor edilmez (yanıltıcıdır)
- Confusion matrix tüm modeller için gösterilir
- F1, Precision, Recall ayrı ayrı raporlanır

### Model Eğitimi
- Eğitilen her model `models/{model_name}.pkl` olarak kaydedilir
- Hiperparametreler `config/model_params.yaml` içinde tutulur
- Magic number kullanma — eşik değer, threshold, seed config'den okunur

## Onay Gerektiren Kararlar
Bunlarda MUTLAKA dur ve sor:
- Yeni dependency eklemek
- Train/test split yöntemini değiştirmek
- Yeni resampling tekniği denemek (SMOTE → ADASYN gibi)
- Feature engineering ekleme/çıkarma
- 3'ten fazla notebook'u etkileyen refactor
- Hyperparameter search alanını genişletmek

## İletişim Dili
- Türkçe konuş
- Kod içi comment'ler İngilizce olabilir
- Değişken/fonksiyon/dosya adları İngilizce
- Görsel başlıkları (chart title, axis label) Türkçe (rapor için)
- Hata mesajları Türkçe
- Commit mesajları İngilizce (Conventional Commits)

## PROGRESS.md Güncelleme Kuralı

Her step tamamlandığında PROGRESS.md'yi şu formatta güncelle
(başlıkları aynen kullan):

Dosya adı: PROGRESS.md
Alanlar:
- Son güncelleme: [tarih]
- Tamamlanan Steps: - [x] Step N — Başlık (tarih)
- Önemli Kararlar: [bu step'te alınan metodolojik/teknik kararlar]
- Üretilen Sonuçlar: [bu step'te elde edilen metrikler/grafikler]
- Açık Sorunlar: [çözülmemiş, sonraya bırakılan şeyler]
- Sonraki Step: Step N+1 — Başlık

## README.md Güncelleme Kuralı

README.md projenin görünür yüzüdür. Şu durumlarda güncellenir:

- Step 10 (Final evaluation) tamamlandığında "Sonuçlar" tablosu doldurulur
- Step 11 (Business cost) tamamlandığında "Önemli Bulgular" yazılır
- Step 12 (Rapor) tamamlandığında "Yazar" ve "Teşekkür" bölümleri kişiselleştirilir
- Yeni dependency eklendiğinde "Teknolojiler" tablosu güncellenir

README dışındaki dokümanlar (architecture.md, methodology.md) `docs/`
altında ve commit edilebilir.

## Klasör Yapısı

```
fraud-detection/
├── data/
│   ├── raw/creditcard.csv          # gitignore (Kaggle datası)
│   ├── processed/                  # gitignore (split'ler)
│   └── README.md                   # commit edilir
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
│   ├── data/                       # Veri yükleme ve split
│   ├── features/                   # Feature engineering, scaling
│   ├── models/                     # Model wrapper sınıfları
│   ├── evaluation/                 # Metrikler, görselleştirme
│   └── utils/                      # Yardımcı fonksiyonlar
├── models/                         # gitignore (.pkl dosyaları)
├── results/
│   ├── figures/                    # Rapor için PNG'ler (commit edilir)
│   ├── metrics/                    # CSV / JSON metrik tabloları
│   └── reports/
├── config/
│   └── model_params.yaml
├── docs/
│   ├── architecture.md
│   ├── methodology.md
│   └── references.md
├── tests/
├── CLAUDE.md                       # GİZLİ — gitignore
├── PROGRESS.md                     # GİZLİ — gitignore
├── README.md                       # GÖRÜNÜR — public dokümantasyon
├── requirements.txt
├── LICENSE
└── .gitignore
```

## Görselleştirme Kuralları

### Genel
- Tüm grafikler `results/figures/` altına PNG olarak kaydedilir
- DPI: minimum 150
- Boyut: figsize=(12, 6) veya (10, 8)
- Stil: `seaborn-v0_8-whitegrid` veya `seaborn-v0_8-darkgrid`
- Renk paleti: tutarlı (her notebook'ta aynı)

### Metrik Renkleri
- Fraud: kırmızı (#DC2626)
- Normal: mavi (#2563EB)
- Pozitif metrik artışı: yeşil (#16A34A)
- Negatif/risk: kırmızı (#DC2626)

### Zorunlu Görseller (her model için)
- Confusion matrix (heatmap)
- ROC curve
- Precision-Recall curve
- Feature importance (RF ve XGB için)

## Test Çalıştırma Kuralı
- Testleri her zaman `pytest` ile çalıştır
- Örnek: `pytest src/tests/ -v`
- Notebook çıktıları için `nbval`: `pytest --nbval notebooks/`

## Git Workflow

Her step başında:
```
git checkout main
git pull origin main
git checkout -b step/XX-step-adi
```

Step boyunca ara commit'ler yap.

Step bitince — bu sırayla:
1. Notebook çalıştır, tüm hücreleri yenile
2. Doğrulama metrikleri kaydet (results/metrics/)
3. `git status` çalıştır ve gizli dosyaların staged olmadığını doğrula
4. Kutay'a rapor ver
5. Kutay onayladıktan sonra commit ve push yap

Branch isimlendirme:
```
step/01-setup
step/02-data-acquisition
step/03-eda
step/04-preprocessing
step/05-baseline-logistic
step/06-random-forest
step/07-xgboost
step/08-isolation-forest
step/09-threshold-tuning
step/10-evaluation
step/11-business-cost
step/12-report
```

## Commit Formatı
```
feat(module): açıklama
fix(module): açıklama
docs(module): açıklama
exp(module): deney/model eğitimi
chore: açıklama
```

Module örnekleri: `eda`, `preproc`, `lr`, `rf`, `xgb`, `iso`, `eval`, `report`

## Asla Commit Edilmeyecek Dosyalar

Şu dosyalar `.gitignore` ile korunmaktadır, asla staged edilmemelidir:

- `CLAUDE.md` — bu dosya, çalışma kuralları (gizli)
- `PROGRESS.md` — proje takip dosyası (gizli)
- `data/raw/*.csv` — Kaggle verisi (lisans gereği yeniden dağıtım yasak)
- `data/processed/*` — büyük dosyalar
- `models/*.pkl`, `models/*.joblib` — büyük dosyalar
- `fraud_env/`, `.venv/` — virtual environment
- `__pycache__/`, `*.pyc` — Python cache
- `.ipynb_checkpoints/` — Jupyter checkpoint
- `.vscode/`, `.idea/` — IDE config (kişisel)
- `kaggle.json` — Kaggle API key (gizli)

## Git Hijyeni — Kritik Kurallar

ASLA `git add .` veya `git add -A` çalıştırma. Her zaman dosyaları açıkça belirt:

```bash
# YANLIŞ — gizli dosyalar yanlışlıkla eklenebilir
git add .

# DOĞRU — sadece istenen dosyalar
git add notebooks/01_eda.ipynb src/data/loader.py results/figures/eda_class_dist.png
```

Commit'ten ÖNCE her zaman `git status` çıktısını kontrol et:

```bash
git status
# Eğer CLAUDE.md, PROGRESS.md veya data/raw/ görünüyorsa unstage et:
git reset HEAD CLAUDE.md PROGRESS.md
```

## Commit Kuralları
- Commit yapmadan önce notebook hücreleri eksiksiz çalışmış olmalı
- Kutay onaylamadan commit yapma
- `git config user.name` ve `user.email` değiştirme
- Tüm commit'ler Kutay adına yapılır
- Commit mesajları İngilizce (Conventional Commits)
- Co-authored-by veya Claude/AI imzası KESİNLİKLE ekleme

## Dokümantasyon Kuralları

### Notebook İçi Dokümantasyon
- Her notebook'un başında markdown hücre: amaç + input + output
- Her kod hücresinden önce ne yaptığını anlatan markdown
- Her grafiğin altında 2-3 cümlelik gözlem
- "Sonuç" bölümü her notebook'un sonunda zorunlu

### Step Sonu Kontrol Listesi
Bir step'in son hücresi tamamlandığında otomatik olarak şunları yap:
1. Notebook'un tüm hücrelerini sıfırdan çalıştır (Restart & Run All)
2. Üretilen figürleri results/figures/ altına kaydet
3. Üretilen metrikleri results/metrics/ altına kaydet
4. PROGRESS.md'yi güncelle
5. README.md güncellenmesi gereken bölüm var mı kontrol et
6. `git status` çıktısını Kutay'a göster
7. Onay sonrası commit + push
8. Kutay'a "Step X tamamlandı" raporu ver

## Geçici Çözüm Kuralı

Bir kodu geçici olarak başka bir yere koyduğunda
(ör: helper fonksiyonu utils yerine notebook içinde),
bunu PROGRESS.md'nin "Açık Sorunlar" bölümüne not olarak ekle.

Format:
"[Step X] — [Fonksiyon]: Geçici olarak [şurada].
[Şu zamanda] [şuraya] taşınacak."

Bu kuralı atlamak yasak.

## Akademik Dürüstlük

- Tüm referanslar (paper, blog, kod) docs/references.md altında listelenir
- Kopyalanan kodun kaynağı yorum satırında belirtilir
- Stack Overflow / GitHub'dan alınan kod adapte edilmeden kullanılmaz
- AI ile üretilen kod insan tarafından review edilir, anlamadığın hiçbir şey commit edilmez