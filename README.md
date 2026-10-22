# ETS-Machine-Learning-II

## Estimasi Ketebalan RNFL dari Citra Fundus Retina

### Overview

Proyek ini mengeksplorasi estimasi ketebalan *Retinal Nerve Fiber Layer* (RNFL) pada 12 sektor jam menggunakan citra fundus retina dan fitur visual dari RETFound. Penelitian dilakukan sebagai eksperimen regresi untuk mendukung eksplorasi skrining glaukoma, bukan sebagai pengganti pemeriksaan klinis menggunakan Optical Coherence Tomography (OCT).

### Tujuan

- Mengevaluasi kemampuan fitur RETFound dalam memprediksi ketebalan RNFL.
- Membandingkan performa regresi OLS, Ridge, Lasso, dan ElasticNet.
- Mengevaluasi galat prediksi menggunakan MAE, RMSE, dan R².
- Menguji ensemble, augmentasi fotometrik, serta strategi pemilihan data berlabel menggunakan UCB1.

### Methodology

1. **Dataset audit:** Memeriksa kelengkapan label RNFL, ketersediaan foto, kualitas citra, dan orientasi sektor.
2. **Preprocessing:** Mengubah citra menjadi RGB berukuran 224 × 224 dengan normalisasi ImageNet. Target mata kanan (OD) direfleksikan untuk menyesuaikan orientasi sektor.
3. **Feature extraction:** Menggunakan encoder RETFound berbasis Vision Transformer (ViT-L/16) yang dibekukan (*frozen*), menghasilkan 1.024 fitur per record.
4. **Model training:** Membandingkan OLS, Ridge, Lasso, dan ElasticNet dengan StandardScaler serta tuning hiperparameter.
5. **Evaluation:** Menggunakan nested GroupKFold berbasis pasien (5 outer folds dan 3 inner folds), prediksi out-of-fold (OOF), serta bootstrap pasien sebanyak 1.000 kali.
6. **Additional experiments:** Menguji ensemble Ridge–ExtraTrees, augmentasi fotometrik, dan strategi pemilihan data berlabel UCB1.

### Dataset
- Adapun dataset yang digunakan meruapakan dataset privat dari rumah sakit 
- Data awal: 175 record dari 90 pasien.
- Data setelah audit: 152 record dari 84 pasien.
- Kohort akhir: 78 mata kanan (OD) dan 74 mata kiri (OS).
- Kategori diagnosis: 82 record glaukoma dan 70 record normal.
- Target: ketebalan RNFL pada 12 sektor jam, dalam mikrometer (µm).

### Hasil

| Model | MAE (µm) | RMSE (µm) | R² |
|:--|--:|--:|--:|
| Ridge | 20.628771 | 27.380614 | 0.591709 |
| OLS | 24.684590 | 32.644848 | 0.419619 |
| Lasso | 22.053619 | 29.234386 | 0.534551 |
| ElasticNet | 21.464231 | 28.436489 | 0.559612 |

Ridge menghasilkan performa terbaik di antara empat regresor utama, dengan MAE 20.63 µm, RMSE 27.38 µm, dan R² 0.592. MAE-nya sekitar 16.4% lebih rendah dibandingkan OLS.

Ensemble Ridge–ExtraTrees dengan augmentasi menghasilkan MAE sekitar 20.53 µm. Namun, peningkatannya terhadap Ridge hanya sekitar 0.095 µm dan interval kepercayaan 95% perbedaannya mencakup nol, sehingga keunggulan tersebut belum meyakinkan.

Pada simulasi pemilihan pasien berlabel, UCB1 menghasilkan MAE 24.10 µm, sedangkan strategi random menghasilkan 23.95 µm. UCB1 belum menunjukkan keunggulan yang konsisten.

### Batasan

- Ukuran kohort relatif kecil, yaitu 84 pasien.
- Pairing citra fundus dengan label OCT dan kesesuaian tanggal pemeriksaan belum terverifikasi sepenuhnya.
- Kualitas citra dan kelengkapan label bervariasi.
- Beberapa sektor memiliki R² negatif.
- Lasso dan ElasticNet menunjukkan peringatan konvergensi.
- Belum dilakukan evaluasi eksternal atau pembandingan dengan backbone alternatif.

### Kesimpulan

Fitur RETFound yang dibekukan menunjukkan potensi untuk eksperimen regresi ketebalan RNFL pada 12 sektor. Ridge menjadi model utama berdasarkan evaluasi OOF berbasis pasien. Meskipun demikian, validasi label, perluasan kohort, pengujian eksternal, dan evaluasi klinis lebih lanjut diperlukan sebelum pendekatan ini dapat dipertimbangkan untuk penerapan skrining glaukoma.

### Referensi

- Zhou et al. (2023). RETFound. *Nature*, 622, 156–163. https://www.nature.com/articles/s41586-023-06555-x
- National Eye Institute. Glaucoma. https://www.nei.nih.gov/eye-health-information/eye-conditions-and-diseases/glaucoma
- American Academy of Ophthalmology. https://pubmed.ncbi.nlm.nih.gov/30322450/

### Project Source dan Catatan

Notebook project dibuat dan di audit di kaggle dengan menggunakan data privat. adapun `main.ipnb` meruapakan copy dari notebook terbaru
