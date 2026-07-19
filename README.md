# BCG X Data Science (Forage) Job Simulation

## 📉 Analisis Customer Churn PowerCo

Simulasi kasus data science (BCG X) yang menyelidiki mengapa PowerCo — perusahaan
utilitas gas & listrik yang melayani usaha kecil dan menengah (SME) — kehilangan
pelanggan ke kompetitor, dan apakah **sensitivitas harga** benar-benar menjadi
penyebabnya?!

> Hipotesis klien (H1): pelanggan berhenti berlangganan karena mereka menjadi lebih sensitif
> terhadap harga (mahal) dan beralih ke penyedia berbiaya lebih rendah. Proyek ini menguji
> hipotesis tersebut menggunakan data.

---

## 📄 Ringkasan Singkat

- Churn rate keseluruhan: **9.7%** dari total pelanggan.
- Fitur terkait harga (termasuk hipotesis perubahan harga off-peak Desember–Januari)
  ternyata berada **di bawah** margin, tenure, dan konsumsi dalam memprediksi churn.
- **Sensitivitas harga bukan pendorong utama churn.**
- Pelanggan yang churn justru memiliki margin bersih rata-rata *lebih tinggi* (€228)
  dibanding pelanggan yang bertahan (€185) — kehilangan mereka jauh lebih merugikan.
- Model Random Forest, saat digunakan untuk memeringkat pelanggan berdasarkan skor
  risiko, mampu menangkap **3.2x** lebih banyak churner dibanding outreach acak ketika
  menyasar 10% akun paling berisiko — meskipun performanya pada threshold default 0.5
  (recall ≈ 5–8%) belum cukup kuat untuk dipakai mandiri secara operasional.

---

## Metodologi Proyek

Proyek ini mengikuti metodologi data science 5 langkah standar yang digunakan di
seluruh tahapan:

1. Memahami masalah bisnis
2. Mengumpulkan dan membersihkan data
3. Eksplorasi data analisis (EDA)
4. Rekayasa fitur (feature engineering)
5. Membangun, mengevaluasi, dan menginterpretasi model prediktif
6. Mengomunikasikan/ menginterpretasikan temuan kepada stakeholder
   
---

## 📊 Data

| File | Deskripsi |
|---|---|
| `client_data.csv` | Profil pelanggan: konsumsi, tanggal kontrak, margin, status churn |
| `price_data.csv` | Harga energi/daya variable & fixed bulanan per pelanggan, tahun 2015 |
| `clean_data_after_eda.csv` | Data pelanggan hasil pembersihan pasca-EDA, dipakai sebagai input feature engineering |

### Kolom fitur utama yang direkayasa

- `offpeak_diff_dec_january_energy` / `_power` — hipotesis awal klien: perubahan harga
  antara Desember dan Januari sebelumnya
- `off_peak_peak_var_mean_diff`, `off_peak_mid_peak_var_mean_diff` — volatilitas harga antar periode
- `tenure` / `months_activ`, `months_to_end`, `months_modif_prod`, `months_renewal` — fitur siklus hidup kontrak
- One-hot encoding untuk `channel_sales` dan `origin_up` (kategori langka dikelompokkan sebagai `other`)

---

## 💡 Ringkasan Modeling

**Algoritma:** `RandomForestClassifier` (scikit-learn), `n_estimators=1000`,
`class_weight='balanced'`, `random_state=42`, split train/test 75/25 (stratified).

| Metrik | Nilai |
|---|---|
| Accuracy | ~0.90–0.91 |
| Precision (churn) | ~0.86–0.87 |
| Recall (churn) | ~0.05–0.08 |
| F1-score (churn) | ~0.10–0.15 |
| ROC-AUC | ~0.66–0.71 |

*(Nilai persis bisa sedikit berbeda tergantung environment/versi scikit-learn meski
`random_state` sudah ditetapkan; lihat notebook modeling untuk hasil run yang dipakai
dalam ringkasan repo ini.)*

**Kenapa metrik-metrik ini yang dipakai:** churn adalah kejadian langka (9.7% dari total
pelanggan), sehingga accuracy bisa menyesatkan, yaitu model yang selalu memprediksi "tidak
churn" saja sudah mencapai akurasi ~90% tanpa menangkap satupun churner (pelanggan yang berhenti 
menggunakan atau berlangganan suatu layanan bisnis dalam periode tertentu). Precision,
recall, F1, dan ROC-AUC dipakai sebagai gantinya karena memperhitungkan ketidakseimbangan
kelas dan mencerminkan biaya nyata dari churner yang terlewat (*false negative*) vs.
penawaran retensi yang terbuang (*false positive*).

**Evaluasi:** recall pada threshold default tergolong rendah dimana sebagian besar churner 
terlewat. Namun, saat dipakai sebagai **alat pemeringkat risiko**
(bukan penyaring ya/tidak), model ini tetap memberi nilai bisnis nyata:
menyasar 10% pelanggan berisiko tertinggi berdasarkan probabilitas prediksi menangkap
~3x lebih banyak churner dibanding outreach acak.

---

## 🗂️ PowerCo Executive Summary
<img width="1280" height="800" alt="Dashboard_Executive Summary" src="https://github.com/user-attachments/assets/fd475dfd-958c-4b60-9577-57278523bbec" />


