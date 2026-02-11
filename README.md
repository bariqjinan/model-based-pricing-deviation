# Analisis Deviasi Harga Berbasis Model pada Data Perumahan Kota Bandung

## Overview

Proyek ini mengeksplorasi bagaimana error prediksi dari model harga rumah dapat digunakan sebagai sinyal analitis terstruktur, bukan semata-mata dianggap sebagai kelemahan performa model.

Alih-alih berfokus pada akurasi prediksi saja, studi ini menganalisis bagaimana deviasi antara harga aktual dan harga hasil estimasi model dapat mengindikasikan penyimpangan harga relatif terhadap fitur struktural yang dapat diamati. Analisis dilakukan menggunakan data perumahan Kota Bandung dengan membandingkan beberapa kelas model untuk mengevaluasi stabilitas dan perilaku sinyal deviasi.

Prediksi model dalam proyek ini tidak diinterpretasikan sebagai “harga benar” atau nilai intrinsik pasar. Prediksi diperlakukan sebagai estimasi statistik berbasis fitur yang tersedia dalam data.

---

## Motivasi

Pemodelan harga rumah umumnya diformulasikan sebagai masalah regresi untuk memperoleh estimasi harga. Namun, dalam praktik pengambilan keputusan, nilai analitis tidak hanya berasal dari angka prediksi, tetapi juga dari identifikasi properti yang menyimpang secara signifikan dari pola yang diharapkan.

Dengan mereformulasi residual sebagai potensi indikator deviasi harga, proyek ini bertujuan untuk:

- Mengevaluasi apakah residual berbasis model dapat berfungsi sebagai sinyal awal deviasi harga,
- Menguji konsistensi sinyal deviasi pada berbagai jenis model,
- Mengidentifikasi potensi bias sistematis dalam perilaku model.

Pendekatan ini menekankan kedalaman analitis dan interpretabilitas dibanding sekadar performa numerik.

---

## Metodologi

Proyek ini mengikuti pipeline pemodelan terstruktur sebagai berikut:

### 1. Model Baseline
- Linear Regression sebagai model referensi awal.

### 2. Model Regularisasi
- Ridge Regression  
- Lasso Regression  

Digunakan untuk menguji pengaruh pengendalian kompleksitas dan penyusutan koefisien terhadap stabilitas model.

### 3. Model Berbasis Pohon
- Decision Tree atau Random Forest (konfigurasi ringan)

Digunakan untuk mengevaluasi pola non-linear dan efek segmentasi dalam data.

### 4. Cross-Validation
- K-Fold Cross-Validation digunakan untuk menilai stabilitas performa model dan mengurangi ketergantungan pada satu pembagian data.

### 5. Ekstraksi Sinyal Berbasis Residual

Residual r
