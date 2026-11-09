# Evaluasi Performa SARIMA vs Tree-Based Machine Learning pada Deret Waktu Harga Pangan di Kota Surabaya

Tugas akhir mata kuliah **Analisis Deret Waktu (ADW)** — Program Studi Sains Data, UPN "Veteran" Jawa Timur.

Penelitian ini membandingkan performa model statistik klasik (**SARIMA**) dengan machine learning (**Random Forest** dan **XGBoost**) untuk memprediksi harga bahan pokok harian di Surabaya. Hasil akhir dari proyek ini berupa analisis komparatif dan **infografis** pemantauan harga pangan.

---

## 1. Latar Belakang & Masalah

Harga pangan di pasar tradisional sering kali naik-turun secara drastis, terutama komoditas seperti cabai dan bawang. Proyek ini bertujuan untuk:
1. Menganalisis karakteristik data harga harian (tren, pola musiman, dan volatilitas).
2. Membandingkan akurasi model SARIMA, Random Forest, dan XGBoost untuk prediksi 1 hari ($h=1$), 7 hari ($h=7$), dan 30 hari ($h=30$) ke depan.
3. Mengetahui pada kondisi volatilitas seperti apa model statistik unggul dibanding machine learning.

---

## 2. Dataset

* **Sumber:** Data laporan harga harian dari 7 pasar tradisional di Surabaya (Bappedalitbang / BRIDA Surabaya).
* **Periode:** 1 Januari 2023 – 31 Agustus 2026 (1.330 hari).
* **Pengolahan:** Harga dihitung dari rata-rata 7 pasar per hari untuk mewakili harga tingkat kota.
* **Komoditas Terpilih:**
  Dipilih 3 komoditas berdasarkan tingkat volatilitasnya (*Coefficient of Variation* / CV):
  - **Cabe Merah Kecil-Rawit** (CV = 0,422) $\rightarrow$ Volatilitas tinggi (sering lonjak drastis).
  - **Bawang Merah Lokal** (CV = 0,226) $\rightarrow$ Volatilitas sedang.
  - **Beras Premium** (CV = 0,067) $\rightarrow$ Volatilitas rendah (harga cenderung stabil).

---

## 3. Metode yang Digunakan

1. **Baseline (Pembanding Dasar):**
   * Naive (menebak pakai harga hari sebelumnya).
   * Seasonal Naive (menebak pakai harga hari yang sama minggu lalu).
2. **Statistik Klasik:**
   * **SARIMA** (mencari orde terbaik pakai AIC/BIC, lalu cek uji residual Ljung-Box).
3. **Machine Learning:**
   * **Random Forest** (pendekatan ensemble bagging).
   * **XGBoost** (pendekatan ensemble boosting).
   * *Fitur input:* Lag harga (1, 2, 3, 7, 14, 30 hari), rolling mean/std (7 dan 30 hari), hari dalam minggu, bulan, dan penanda hari libur/Ramadan.

---

## 4. Evaluasi Model

* **Validasi Data:** Menggunakan *walk-forward validation* (rolling origin) agar tidak terjadi kebocoran data masa depan:
  - Fold 1: Train 2023–2024 $\rightarrow$ Test Awal 2025
  - Fold 2: Train 2023–Mid 2025 $\rightarrow$ Test Akhir 2025
  - Fold 3: Train 2023–2025 $\rightarrow$ Test 2026
* **Metrik Evaluasi:**
  - **MASE** (metrik utama, jika nilainya $< 1$ berarti model lebih bagus dari tebakan Naive).
  - **RMSE & MAE** (untuk melihat error dalam satuan Rupiah).
  - Uji beda nyata **Diebold-Mariano** untuk memastikan perbedaan akurasi antar-model valid secara statistik.

---

## 5. Struktur Folder

```text
├── README.md
├── requirements.txt
├── data/
│   └── seri_harian.csv              # Data gabungan rata-rata 7 pasar
├── notebooks/
│   ├── 01_persiapan_dan_eda.ipynb   # Bersih data, visualisasi, & baseline
│   ├── 02_pemodelan_sarima.ipynb    # Model SARIMA & cek residual
│   ├── 03_pemodelan_rf.ipynb        # Feature engineering & Random Forest
│   ├── 04_pemodelan_xgboost.ipynb   # Model XGBoost & tuning
│   └── 05_evaluasi_komparasi.ipynb  # Perbandingan MASE/RMSE & uji statistik
├── output/
│   ├── tabel_evaluasi.csv           # Hasil rekap metrik semua model
│   └── figures/                     # Grafik prediksi vs aktual
└── infografis/
    └── infografis_final.png         # Poster infografis output UAS
```

---

## 6. Cara Menjalankan

1. Clone repositori ini:
   ```bash
   git clone https://github.com/xFarell/time-series-analysis-A.git
   cd time-series-analysis-A
   ```
2. Install library yang dibutuhkan:
   ```bash
   pip install -r requirements.txt
   ```
3. Buka dan jalankan Jupyter Notebook secara berurutan dari folder `notebooks/`.
