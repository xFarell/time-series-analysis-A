# Progress Minggu 1 — UAS ADW

**Tanggal:** 22 November 2026  
**Minggu ke-1 (rencana: 10 Nov – 16 Nov)**

---

## Artefak

| File | Deskripsi |
|---|---|
| `01_eda.ipynb` | Notebook EDA lengkap: statistik deskriptif, visualisasi tren, boxplot tahunan, uji ADF/KPSS, baseline Naive & Seasonal Naive |
| `seri_harian.csv` | Data utama: 1.339 hari (2023-01-01 s/d 2026-08-31), 3 komoditas, rata-rata 7 pasar |
| `statistika_deskriptif.csv` | Mean, Std, Min, Max, CV, Rezim untuk tiap komoditas |
| `baseline_metrik.csv` | MAE & RMSE untuk Naive (lag-1) dan Seasonal Naive (lag-7) |
| `uji_stasioneritas.csv` | Hasil ADF & KPSS pada level dan first difference |
| `ringkasan_mutu_data.csv` | Jumlah nilai asli vs diinterpolasi per komoditas |
| `etl_log.csv` | Log audit per file bulanan (44 file) |
| `etl_konflik.csv` | 43 selisih antara sheet harian vs REKAPITULASI |
| `01_tren_harga_historis.png` | Grafik 1 panel 3 garis (label CV dinamis) |
| `02_boxplot_tahunan.png` | Boxplot distribusi harga per tahun per komoditas |

---

## Ringkasan Hasil

| Komoditas | Mean (Rp) | Std (Rp) | CV | Rezim |
|---|---|---|---|---|
| Cabe Merah Kecil-Rawit | 45.048 | 19.003 | **0.42** | Tinggi |
| Bawang Merah Lokal | 32.936 | 7.426 | **0.23** | Sedang |
| Beras Premium | 14.915 | 1.070 | **0.07** | Rendah |

- Data bersih: 1.339 observasi per komoditas
- Missing value: 7 per kolom (0.5%) → diinterpolasi linear berbasis waktu
- Nilai negatif / nol: 0
- **Uji stasioneritas:** ketiga seri non-stasioner level, stasioner setelah differencing orde 1

---

## Catatan Teknis

**Perbaikan ETL (commit `c6263a6`):**
- Deteksi bulan pakai regex `^(\d{2})\.` → 44 file terbaca benar (sebelumnya Mar-Des jatuh ke Februari)
- Sumber utama diganti ke **sheet harian** (7 pasar per hari, rata-rata dihitung ulang, diverifikasi)
- REKAPITULASI dibaca sebagai pembanding → 43 konflik tercatat di `etl_konflik.csv`
- April 2023: hari 22–23 tidak ada di file sumber
- April 2026: hari 28–30 sheet harian kosong
- Total 7 hari tanpa sumber → diinterpolasi

---

## Link Repo

- Repo: https://github.com/xFarell/time-series-analysis-A
- Commit: `c6263a6`
- Branch: `main`