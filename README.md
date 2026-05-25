# Analisis Deviasi Harga Berbasis Model pada Data Perumahan Kota Bandung

## Overview

Proyek ini mengeksplorasi bagaimana error prediksi dari model harga rumah dapat digunakan sebagai sinyal analitis terstruktur, bukan semata-mata dianggap sebagai kelemahan performa model.

Alih-alih berfokus pada akurasi prediksi saja, studi ini menganalisis bagaimana deviasi antara harga aktual dan harga hasil estimasi model dapat mengindikasikan penyimpangan harga relatif terhadap fitur struktural yang dapat diamati. Analisis dilakukan menggunakan data perumahan Kota Bandung dengan membandingkan empat kelas model untuk mengevaluasi stabilitas dan perilaku sinyal deviasi.

Prediksi model dalam proyek ini tidak diinterpretasikan sebagai "harga benar" atau nilai intrinsik pasar. Prediksi diperlakukan sebagai estimasi statistik berbasis fitur yang tersedia dalam data.

---

## Motivasi

Pemodelan harga rumah umumnya diformulasikan sebagai masalah regresi untuk memperoleh estimasi harga. Namun, dalam praktik pengambilan keputusan, nilai analitis tidak hanya berasal dari angka prediksi, tetapi juga dari identifikasi properti yang menyimpang secara signifikan dari pola yang diharapkan.

Dengan mereformulasi residual sebagai potensi indikator deviasi harga, proyek ini bertujuan untuk:

- Mengevaluasi apakah residual berbasis model dapat berfungsi sebagai sinyal awal deviasi harga,
- Menguji konsistensi sinyal deviasi pada berbagai jenis model,
- Mengidentifikasi potensi bias sistematis dalam perilaku model.

Pendekatan ini menekankan kedalaman analitis dan interpretabilitas dibanding sekadar performa numerik.

---

## Struktur Notebook

Notebook (`notebooks/baseline_model.ipynb`) mengikuti pipeline 5-bagian yang terstruktur:

### 1. Data Loading & EDA
- Memuat `data/results.csv` dengan rename kolom semantik
- Analisis distribusi harga (linear & log scale)
- Eksplorasi kardinalitas lokasi dan fitur numerik

### 2. Preprocessing Pipeline
Menggunakan `sklearn.Pipeline` dan `ColumnTransformer`:
- **Numerik** (bedroom, bathroom, carport, luas tanah, luas bangunan, building_ratio): `SimpleImputer(median)` + `StandardScaler`
- **Kategorik** (type, status): `SimpleImputer(constant='Unknown')` + `OneHotEncoder(drop='first')`
- **Lokasi** (high-cardinality): `FrequencyEncoder` custom — setiap kategori dipetakan ke frekuensi relatifnya di training set

Fitur yang direkayasa:
- `building_ratio = building_area_sqm / land_area_sqm` — masuk ke model
- `price_per_sqm = price_numeric / land_area_sqm` — analisis saja, tidak masuk ke X

Strategi cleaning: baris dengan `land_area` atau `building_area` bernilai 0 atau NaN di-drop. NaN pada bedroom/bathroom/carport diimputasi dengan median (bukan di-drop, bukan di-set ke 0).

### 3. Modeling
Empat model dibangun dalam pipeline yang konsisten:

| Model | Konfigurasi |
|:------|:------------|
| **Linear Regression** | Baseline tanpa regularisasi |
| **Ridge (RidgeCV)** | Hyperparameter α dipilih via 5-fold CV, `alphas=logspace(-2, 6, 100)` |
| **Lasso (LassoCV)** | Hyperparameter α dipilih via 5-fold CV, `n_alphas=50` |
| **Random Forest** | `n_estimators=100`, `max_depth=10`, konfigurasi ringan |

### 4. Evaluation
- **K-Fold CV (k=5)** via `cross_val_score` dengan metrik MAE dan R² untuk semua model
- **Test set evaluation**: MAE, MAPE, R²
- **Tabel perbandingan lengkap**: Test MAE, MAPE, R², CV MAE mean±std, CV R² mean±std

### 5. Residual Signal Analysis

Residual ratio didefinisikan sebagai:

$$r = \frac{y - \hat{y}}{\hat{y}}$$

dengan:
- $y$ = harga aktual
- $\hat{y}$ = harga prediksi model

Klasifikasi deviasi dilakukan berbasis kuantil data-driven:
- **Underpriced** → 10% tertinggi residual ratio (model underestimate; harga aktual relatif tinggi)
- **Overpriced**  → 10% terendah residual ratio (model overestimate; harga aktual relatif rendah)
- **Fair**        → 80% sisanya

Visualisasi yang dihasilkan:
1. Scatter plot residual ratio vs harga aktual (log scale) — per model terbaik dan grid 4 model
2. Bar chart proporsi klasifikasi deviasi per segmen harga (Rendah / Menengah / Tinggi)
3. Tabel konsistensi sinyal: % baris test set di mana semua model sepakat pada klasifikasi yang sama

---

## Prinsip Utama

Proyek ini memperlakukan error prediksi sebagai sinyal analitis, bukan sebagai kebenaran pasar.

Deviasi yang teridentifikasi merupakan deviasi relatif terhadap struktur model, bukan bukti definitif adanya mispricing di pasar.

---

## Keterbatasan

- Model hanya menggunakan fitur struktural yang tersedia dalam dataset.
- Faktor mikro-lokasi dan atribut kualitatif tidak sepenuhnya tercakup.
- Residual besar dapat mencerminkan keterbatasan model atau variabel yang tidak teramati.
- Data menggunakan harga listing, bukan harga transaksi aktual.
- Klasifikasi deviasi bergantung pada struktur model yang digunakan.
- Ukuran dataset dapat memengaruhi stabilitas estimasi pada distribusi ekor.

Seluruh keterbatasan ini diakui untuk mencegah overinterpretasi terhadap hasil analisis.
