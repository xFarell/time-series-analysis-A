# Minggu 3 — SARIMA Modeling & Diagnostics

## Artefak SARIMA
| File | Deskripsi |
|------|-----------|
| `02_sarima.ipynb` | Notebook lengkap: ACF/PACF, grid search AIC/BIC, fit, diagnostik residual, rolling origin forecasting, BDS test |
| `sarima_orders.csv` | Orde SARIMA terpilih per komoditas (AIC minimal) |
| `sarima_forecast_all.csv` | Semua prediksi rolling origin (3 fold × 3 komoditas × 3 horizon) |
| `sarima_metrics_summary.csv` | Metrik MAE, RMSE, MAPE pooled per komoditas & horizon |
| `sarima_metrics_per_fold.csv` | Metrik per fold untuk analisis stabilitas |
| `sarima_residual_diagnostics.csv` | Hasil uji Ljung-Box, Jarque-Bera, ARCH-LM |
| `sarima_bds_test.csv` | Hasil BDS test pada residual (justifikasi non-linearitas → ML) |
| `grid_search_*.csv` | Hasil grid search AIC/BIC per komoditas |
| `sarima_model_summaries.txt` | Ringkasan model statistik lengkap |
| `03_acf_pacf_sarima.png` | ACF/PACF level & diff 1 untuk 3 komoditas |
| `04_residual_diagnostics.png` | Residual: time series, histogram, Q-Q, ACF |
| `05_sarima_forecast_vs_actual.png` | Forecast vs aktual h=1 per komoditas |

## Orde Terpilih (AIC)
| Komoditas | Order (p,d,q)(P,D,Q)₇ |
|-----------|------------------------|
| Cabe Rawit | (3,1,1)(2,1,2)₇ |
| Bawang Merah | (3,1,3)(1,1,2)₇ |
| Beras Premium | (3,1,3)(0,1,2)₇ |

## Diagnostik Residual (Kesimpulan)
- **Ljung-Box**: Cabe lolos h=20/30; Bawang & Beras GAGAL (autokorelasi tersisa)
- **Jarque-Bera**: Semua GAGAL (residual tidak normal)
- **ARCH-LM**: Semua GAGAL (heteroskedastisitas)
- **BDS**: Reject H0 i.i.d. → residual mengandung non-linearitas
→ **Justifikasi kuat** untuk model non-linear (Random Forest, XGBoost)

## Metrik Forecasting (Pooled 3 Fold, N=521)
| Komoditas | Horizon | MAE | RMSE | MAPE |
|-----------|---------|-----|------|------|
| Beras Premium | 1 | 6.68 | 10.01 | 0.04% |
| Beras Premium | 7 | 53.92 | 88.52 | 0.34% |
| Beras Premium | 30 | 128.11 | 156.27 | 0.81% |
| Bawang Merah | 1 | 152.14 | 204.17 | 0.41% |
| Bawang Merah | 7 | 1,823.64 | 2,450.28 | 4.80% |
| Bawang Merah | 30 | 5,962.51 | 7,483.73 | 16.16% |
| Cabe Rawit | 1 | 1,366.33 | 2,162.33 | 3.02% |
| Cabe Rawit | 7 | 7,463.03 | 11,243.32 | 14.37% |
| Cabe Rawit | 30 | 23,543.40 | 29,686.77 | 51.36% |

**Kesimpulan**: SARIMA cukup baik untuk beras (MAPE <1%), tapi error melonjak di horizon panjang untuk cabe & bawang. Baseline untuk dibandingkan dengan ML.

---

## Next: Random Forest & XGBoost (akan ditambahkan)
- `03_random_forest.ipynb` — Feature engineering, RF training, rolling origin
- `04_xgboost.ipynb` — XGBoost training + hyperparameter tuning
