# Evaluasi Performa SARIMA vs Tree-Based Machine Learning pada Deret Waktu Harga Pangan di Kota Surabaya

Tugas akhir mata kuliah **Analisis Deret Waktu (ADW)** — Program Studi Sains Data, UPN "Veteran" Jawa Timur.

Penelitian ini membandingkan performa model statistik klasik (**SARIMA**) dengan machine learning (**Random Forest** dan **XGBoost**) untuk memprediksi harga bahan pokok harian di Surabaya.

---

## 1. Latar Belakang & Masalah

Harga pangan di pasar tradisional sering kali naik-turun secara drastis. Proyek ini bertujuan untuk:
1. Menganalisis karakteristik data harga harian (tren, pola musiman, dan volatilitas).
2. Membandingkan akurasi model SARIMA, Random Forest, dan XGBoost untuk prediksi 1 hari ($h=1$), 7 hari ($h=7$), dan 30 hari ($h=30$) ke depan.
3. Mengetahui pada kondisi volatilitas seperti apa model statistik unggul dibanding machine learning.

---

## 2. Dataset

* **Sumber:** Data laporan harga harian dari 7 pasar tradisional di Surabaya.
* **Periode:** 1 Januari 2023 – 31 Agustus 2026 (1.339 hari).
* **Pengolahan:** Harga dihitung dari rata-rata 7 pasar per hari.
* **Komoditas Terpilih (purposive sampling berdasarkan CV):**
  - **Cabe Rawit** (CV = 0,42) — Volatilitas tinggi
  - **Bawang Merah Lokal** (CV = 0,23) — Volatilitas sedang  
  - **Beras Premium** (CV = 0,07) — Volatilitas rendah

---

## 3. Metode

| Model | Jenis | Keterangan |
|-------|-------|------------|
| Naive / Seasonal Naive | Baseline | Tebakan sederhana |
| SARIMA | Statistik Klasik | Orde dipilih via AIC/BIC, cek residual Ljung-Box, BDS test |
| Random Forest | ML (Bagging) | Feature: lag, rolling stats, calendar |
| XGBoost | ML (Boosting) | Hyperparameter tuning + feature engineering |

---

## 4. Validasi & Evaluasi

* **Validasi:** Rolling Origin 3 Fold (Walk-forward)
  - Fold 1: Train 2023–2024 → Test Jan–Jun 2025
  - Fold 2: Train 2023–Jun 2025 → Test Jul–Des 2025
  - Fold 3: Train 2023–2025 → Test Jan–Agu 2026
* **Horizon:** $h = 1, 7, 30$ hari
* **Metrik Utama:** **MASE** (Mean Absolute Scaled Error)
* **Metrik Pendukung:** RMSE, MAE, MAPE
* **Uji Statistik:** Diebold-Mariano (paired, per horizon)

---

## 5. Struktur Repositori

```text
repo-root/
├── README.md
├── progress/
│   ├── minggu-1/          # ETL, EDA, Baseline, Stasioneritas
│   │   ├── 00_etl.ipynb
│   │   ├── 01_eda.ipynb
│   │   ├── seri_harian.csv
│   │   ├── etl_log.csv / etl_konflik.csv
│   │   ├── statistika_deskriptif.csv
│   │   ├── uji_stasioneritas.csv
│   │   ├── baseline_metrik.csv
│   │   ├── ringkasan_mutu_data.csv
│   │   ├── 01_tren_harga_historis.png
│   │   ├── 02_boxplot_tahunan.png
│   │   └── README.md
│   ├── minggu-2/          # SARIMA Modeling & Diagnostics
│   │   ├── 02_sarima.ipynb
│   │   ├── sarima_orders.csv
│   │   ├── sarima_forecast_all.csv
│   │   ├── sarima_metrics_summary.csv
│   │   ├── sarima_metrics_per_fold.csv
│   │   ├── sarima_residual_diagnostics.csv
│   │   ├── sarima_bds_test.csv
│   │   ├── sarima_model_summaries.txt
│   │   ├── grid_search_*.csv
│   │   ├── 03_acf_pacf_sarima.png
│   │   ├── 04_residual_diagnostics.png
│   │   ├── 05_sarima_forecast_vs_actual.png
│   │   └── README.md
│   └── minggu-3/          # Random Forest & XGBoost (akan diisi)
│       ├── 03_random_forest.ipynb
│       └── README.md
```

---

## 6. Cara Menjalankan

1. Clone repositori:
   ```bash
   git clone https://github.com/xFarell/time-series-analysis-A.git
   cd time-series-analysis-A
   ```
2. Install dependencies (Python 3.12+):
   ```bash
   pip install -r requirements.txt
   ```
3. Jalankan notebook per minggu:
   - Minggu 1: `progress/minggu-1/00_etl.ipynb` → `01_eda.ipynb`
   - Minggu 2: `progress/minggu-2/02_sarima.ipynb`
   - Minggu 3: `progress/minggu-3/03_random_forest.ipynb` → `04_xgboost.ipynb`
   - Minggu 5: `progress/minggu-5/05_perbandingan.ipynb` (akan dibuat)

---

## 7. Hasil Sementara (Minggu 1-2)

| Model | Cabe (MAPE h=30) | Bawang (MAPE h=30) | Beras (MAPE h=30) |
|-------|------------------|---------------------|-------------------|
| SARIMA | 51.36% | 16.16% | 0.81% |

SARIMA cukup baik untuk beras (stabil), tapi error besar untuk cabe/bawang di horizon panjang → dibutuhkan ML non-linear.
