# UAS Analisis Deret Waktu — Perbandingan SARIMA vs Machine Learning

Perbandingan performa model statistik klasik (SARIMA) dengan metode machine learning
(Random Forest, XGBoost) untuk peramalan harga pangan harian Kota Surabaya.

## 1. Rumusan Masalah

1. Bagaimana karakteristik deret harga pangan harian (stasioneritas, pola musiman,
   dependensi non-linear)?
2. Bagaimana performa SARIMA dibandingkan Random Forest dan XGBoost pada horizon
   1, 7, dan 30 hari?
3. Pada kondisi seri seperti apa masing-masing metode unggul?

Kontribusi penelitian bukan "model X lebih baik", melainkan identifikasi **kondisi**
kapan tiap metode unggul.

## 2. Data

| Item | Keterangan |
|---|---|
| Sumber | `master_table_processing.xlsx`, sheet `master_table_mentah_clean` |
| Baris | 492.986 |
| Periode | 2023-01-01 s/d 2026-08-31 (1.330 hari) |
| Pasar | 7 pasar Kota Surabaya |
| Satuan analisis | `kg` |
| Referensi HET | `het_final.csv` (22 komoditas, 11 ber-HET valid) |

### Komoditas Terpilih (purposive sampling berlapis volatilitas)

| Komoditas | CV | Rezim |
|---|---|---|
| Cabe Merah Kecil-Rawit | 0,422 | volatilitas tinggi |
| Bawang Merah (Kualitas Lokal) | 0,226 | volatilitas sedang |
| Beras Premium | 0,067 | volatilitas rendah |

Kriteria: data harian penuh 1.330 hari pada 7 pasar, satuan `kg`, dan sebaran CV
mencakup tiga rezim volatilitas.

## 3. Metode

### Baseline
- Naive: `y_hat(t+h) = y(t)`
- Seasonal Naive: `y_hat(t+h) = y(t+h-7)`

### Statistik klasik
- **SARIMA(p,d,q)(P,D,Q)7** via `pmdarima.auto_arima(seasonal=True, m=7)`
- Pemilihan orde berdasarkan AIC/BIC
- Diagnostik residual: Ljung-Box, Jarque-Bera, ARCH-LM
- **Uji BDS pada residual** untuk mendeteksi struktur non-linear tersisa

### Machine learning
- **Random Forest** dan **XGBoost**
- Fitur: lag (1,2,3,7,14,30), rolling mean/std/min/max (7,14,30), kalender,
  jarak ke hari raya, rasio harga/HET
- Tuning hyperparameter via time-series cross-validation

### Di luar cakupan
- LSTM / deep learning: 1.330 observasi terlalu pendek; CPU-only
- SSA: tidak punya uji signifikansi/interval kepercayaan baku

## 4. Protokol Evaluasi

### Rolling origin (walk-forward)

| Fold | Train | Test |
|---|---|---|
| 1 | 2023-01 .. 2024-12 | 2025-01 .. 2025-06 |
| 2 | 2023-01 .. 2025-06 | 2025-07 .. 2025-12 |
| 3 | 2023-01 .. 2025-12 | 2026-01 .. 2026-08 |

Random split dan single 80:20 tidak dipakai.

### Horizon
`h = 1` (EWS harian), `h = 7` (operasi pasar), `h = 30` (perencanaan bulanan).

### Metrik

| Metrik | Peran |
|---|---|
| **MASE** | utama — `MAE(model) / MAE(naive)`, skala-free |
| RMSE | pendukung, interpretasi rupiah |
| MAE | pendukung, tahan outlier |
| Waktu komputasi | bagian dari performa |

MASE < 1 berarti model mengalahkan naive.

### Uji statistik perbandingan
- Diebold-Mariano (+ koreksi Harvey-Leybourne-Newbold)
- Friedman + Nemenyi jika membandingkan lintas banyak seri

## 5. Rencana Pengerjaan

| Minggu | Target | Deliverable |
|---|---|---|
| 1 (10 Nov) | Persiapan seri + baseline + uji stasioner | `01_persiapan_seri.ipynb`, `seri_harian.csv`, `baseline_metrik.csv`, `fig_seri.png` |
| 2 (17 Nov) | SARIMA + diagnostik + BDS residual | `02_sarima.ipynb`, `sarima_order.csv`, `sarima_forecast.csv` |
| 3 (24 Nov) | Feature engineering + Random Forest | `03_random_forest.ipynb`, `rf_forecast.csv` |
| 4 (1 Des) | XGBoost + tabel perbandingan | `04_xgboost.ipynb`, `xgb_forecast.csv`, `mase_semua.csv` |
| 5 (8 Des) | Rolling origin + Diebold-Mariano | `05_perbandingan.ipynb`, `dm_test.csv` |
| 6 | Visualisasi + draf laporan | semua grafik + draf bab |

Setiap minggu wajib menyertakan: notebook yang berjalan tanpa error, minimal satu
grafik, minimal satu tabel hasil (CSV), dan satu paragraf interpretasi.

## 6. Struktur Repo

```
uas-adw/
├── README.md
├── requirements.txt
├── .gitignore
├── progress/
│   ├── minggu-1/
│   │   ├── README.md
│   │   └── 01_persiapan_seri.ipynb
│   ├── minggu-2/
│   ├── minggu-3/
│   ├── minggu-4/
│   └── minggu-5/
├── data/
│   └── seri_harian.csv
└── output/
    ├── baseline_metrik.csv
    ├── sarima_order.csv
    ├── sarima_forecast.csv
    ├── rf_forecast.csv
    ├── xgb_forecast.csv
    ├── mase_semua.csv
    ├── dm_test.csv
    └── figures/
```

## 7. Notebook Minggu 1

| Sel | Isi | Output |
|---|---|---|
| 1 | Import + konfigurasi 3 komoditas | — |
| 2 | Load master, filter `satuan == "kg"` dan 3 jenis | ± 4.000 baris |
| 3 | Agregasi rata-rata 7 pasar per tanggal | 1.330 titik |
| 4 | `asfreq("D")` + interpolasi 6 gap | 0 NaN |
| 5 | EDA: statistik deskriptif, CV, boxplot per tahun | tabel + grafik |
| 6 | Plot 3 seri + ACF/PACF lag 60 | `fig_seri.png`, `fig_acf.png` |
| 7 | ADF + KPSS (level dan difference) | tabel stasioner |
| 8 | Baseline Naive + Seasonal Naive → MASE | `baseline_metrik.csv` |

## 8. Cara Menjalankan

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

Jalankan notebook berurutan dari `progress/minggu-1/` dan seterusnya.

## 9. Kebutuhan Library

```
pandas
numpy
matplotlib
seaborn
statsmodels
pmdarima
scikit-learn
xgboost
sktime
jupyterlab
openpyxl
```

## 10. Catatan

- File mentah (`master_table_processing.xlsx`, `master_table_mentah.xlsx`, folder
  `raw_data/`) **tidak di-commit** karena ukurannya besar.
- Hanya CSV hasil olahan yang masuk repo.
- Repo ini berisi progres mingguan, bukan hanya hasil akhir.

## 11. Referensi

- Box, G. E. P., Jenkins, G. M., & Reinsel, G. C. *Time Series Analysis: Forecasting
  and Control*.
- Hyndman, R. J., & Athanasopoulos, G. *Forecasting: Principles and Practice*.
- Brock, W. A., Dechert, W. D., & Scheinkman, J. A. (1987). *A Test for Independence
  Based on the Correlation Dimension*.
- Diebold, F. X., & Mariano, R. S. (1995). *Comparing Predictive Accuracy*.
- Makridakis, S., Spiliotis, E., & Assimakopoulos, V. (2020, 2022). *M4/M5
  Competitions*.
