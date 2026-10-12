# 📊 Analisis Faktor yang Memengaruhi Rating Aplikasi Google Play Store

Analisis ini bertujuan untuk mengidentifikasi faktor-faktor yang berkaitan dengan kemungkinan sebuah aplikasi memperoleh **rating ≥ 4** di Google Play Store. Metode utama yang digunakan adalah **regresi logistik biner**, dengan proses pengolahan data, pemilihan model, serta evaluasi performa menggunakan **R**.

> Proyek ini merupakan analisis data untuk memahami hubungan antara ulasan pengguna, jumlah instalasi, genre, dan kategori aplikasi terhadap rating aplikasi.

## 🎯 Latar Belakang

Rating merupakan salah satu informasi yang dapat dipertimbangkan pengguna ketika memilih aplikasi di Google Play Store. Rating juga dapat menjadi masukan bagi pengembang untuk memahami respons pengguna terhadap aplikasi yang dibuat.

Namun, rating dapat berkaitan dengan banyak karakteristik aplikasi. Oleh karena itu, analisis ini menggunakan regresi logistik untuk mempelajari hubungan sejumlah variabel dengan peluang aplikasi memiliki rating tinggi.

## 🎯 Tujuan Analisis

1. Mengidentifikasi faktor-faktor yang berkaitan dengan kemungkinan aplikasi memperoleh rating **≥ 4**.
2. Menyiapkan dan membersihkan dataset agar dapat digunakan dalam analisis.
3. Membandingkan model regresi logistik tanpa interaksi dan model dengan interaksi.
4. Memilih model berdasarkan prosedur seleksi model dan nilai Akaike Information Criterion (AIC).
5. Mengevaluasi model melalui uji ANOVA, uji Hosmer–Lemeshow, tabel klasifikasi, dan kurva ROC.

## 📁 Dataset

Dataset yang digunakan adalah **Google Play Store Apps** dari Kaggle.

- **Jumlah observasi awal:** 10.841 baris
- **Jumlah variabel:** 13 variabel
- **Missing value pada variabel target Rating:** 1.474
- **Jumlah baris setelah penghapusan nilai kosong:** 9.367 baris

Dataset berisi informasi aplikasi, seperti rating, jumlah ulasan, jumlah instalasi, kategori, genre, ukuran, tipe aplikasi, harga, rating konten, dan informasi versi.

Sumber data: [Google Play Store Apps — Kaggle](https://www.kaggle.com/datasets/lava18/google-play-store-apps)

## 🔎 Variabel Penelitian

### Variabel target

**Rating** diubah menjadi variabel biner:

- `1` — rating aplikasi ≥ 4
- `0` — rating aplikasi < 4

### Variabel prediktor utama

| Variabel | Keterangan |
|---|---|
| `Reviews` | Jumlah ulasan pengguna terhadap aplikasi |
| `Installs` | Jumlah instalasi aplikasi |
| `Genres` | Genre aplikasi yang telah dikelompokkan |
| `Category` | Kategori aplikasi yang telah dikelompokkan |

Dalam preprocessing, kategori aplikasi disederhanakan menjadi kelompok seperti **Lifestyle, Media, Productivity, Social**, dan **Other**. Genre juga dikelompokkan menjadi sejumlah kelompok, antara lain **Lifestyle, Edu, Games, Utility, Health, Entertain, Family, Music**, dan **Creative**.

## ⚙️ Tahapan Analisis

### 1. Preprocessing data

Langkah persiapan data meliputi:

- Memeriksa nilai kosong (*missing values*).
- Menghapus baris dengan nilai target yang kosong.
- Memeriksa dan menyesuaikan tipe data setiap variabel.
- Mengelompokkan kategori dan genre agar jumlah kategori lebih mudah dikelola.
- Mengubah rating menjadi variabel target biner berdasarkan ambang 4.

Setelah penghapusan missing value, data yang tersisa berjumlah **9.367 baris**.

### 2. Uji multikolinearitas

Pemeriksaan multikolinearitas dilakukan menggunakan **Variance Inflation Factor (VIF)**. Berdasarkan laporan, nilai GVIF yang telah disesuaikan berada di bawah 10, sehingga tidak ditemukan indikasi multikolinearitas yang melewati batas tersebut pada pemeriksaan awal.

### 3. Membangun model regresi logistik

Dua pendekatan pemodelan dibandingkan:

- **Model tanpa interaksi**, yang mengasumsikan pengaruh prediktor dimasukkan tanpa suku interaksi.
- **Model dengan interaksi**, yang mempertimbangkan kemungkinan hubungan antarvariabel prediktor, termasuk interaksi dua arah dan interaksi tingkat lebih tinggi.

Seleksi variabel dilakukan dengan pendekatan **backward elimination** dan **forward selection**.

### 4. Seleksi model

Model dibandingkan menggunakan nilai **Akaike Information Criterion (AIC)** serta uji ANOVA untuk membandingkan model tanpa interaksi dan model dengan interaksi.

Berdasarkan nilai AIC dalam laporan, model dengan interaksi hasil backward elimination dipilih sebagai model kandidat terbaik.

### 5. Evaluasi model

Model dievaluasi dengan beberapa cara:

- **Uji ANOVA:** membandingkan model dengan dan tanpa interaksi.
- **Uji Hosmer–Lemeshow:** mengevaluasi kecocokan model dengan data.
- **Tabel klasifikasi (*confusion matrix*):** menilai hasil klasifikasi pada ambang probabilitas 0,50.
- **Kurva ROC dan AUC:** mengukur kemampuan model membedakan dua kelompok rating.

## 🧮 Pemodelan

Model awal tanpa interaksi dirumuskan sebagai:

```r
Rating ~ Reviews + Installs + Genres + Category
```

Model tanpa interaksi hasil seleksi dalam laporan:

```r
Rating ~ Reviews + Category + Genres
```

Model awal dengan interaksi:

```r
Rating ~ Reviews * Installs * Genres * Category
```

Dari proses seleksi, model dengan interaksi hasil **backward elimination** memperoleh AIC terendah di antara model yang dibandingkan dalam laporan. Model ini kemudian dibandingkan dengan model tanpa interaksi menggunakan uji ANOVA.

## 📈 Evaluasi Model

| Indikator | Hasil dalam laporan | Interpretasi |
|---|---:|---|
| AIC model tanpa interaksi | 10.710 | Menjadi pembanding model |
| AIC model interaksi — backward elimination | 10.067,64 | Terendah di antara model yang dilaporkan |
| AIC model interaksi — forward selection | 10.184,74 | Lebih tinggi daripada model backward |
| Akurasi klasifikasi | 72,62% | Sekitar 72,6% prediksi sesuai dengan kelas aktual |
| Error klasifikasi | 27,37% | Proporsi prediksi yang salah pada ambang yang digunakan |
| AUC ROC | 0,652 | Kemampuan diskriminasi model terbatas hingga cukup |
| Uji Hosmer–Lemeshow | p-value = 2,22 × 10⁻¹⁶ | Mengindikasikan kecocokan model yang kurang memadai |

**Catatan interpretasi:** nilai AIC yang lebih rendah mendukung pemilihan model relatif terhadap model lain yang dibandingkan. Namun, AIC terendah dan akurasi yang cukup tinggi tidak otomatis berarti model telah terkalibrasi atau cocok dengan data. Hasil uji Hosmer–Lemeshow dalam laporan justru menunjukkan adanya masalah kecocokan model.

## 💡 Hasil dan Temuan Utama

1. **Interaksi antarprediktor penting untuk dipertimbangkan.** Uji ANOVA dalam laporan menunjukkan bahwa model dengan interaksi secara signifikan lebih baik daripada model tanpa interaksi berdasarkan kriteria pengujian yang digunakan.
2. **Seleksi backward menghasilkan AIC terendah.** Di antara model yang dibandingkan, model interaksi hasil backward elimination memperoleh AIC sebesar **10.067,64**.
3. **Akurasi model sekitar 72,6%.** Model dapat mengklasifikasikan kelompok rating dengan tingkat ketepatan tersebut pada ambang probabilitas 0,50, tetapi masih memiliki error sekitar 27,4%.
4. **AUC sebesar 0,652.** Nilai ini menunjukkan kemampuan model membedakan aplikasi dengan rating ≥ 4 dan rating < 4 yang belum kuat.
5. **Kecocokan model masih menjadi perhatian.** Uji Hosmer–Lemeshow menghasilkan p-value yang sangat kecil, sehingga asumsi kecocokan model terhadap data perlu ditinjau kembali.
6. **Kategori, genre, dan ulasan muncul dalam model akhir.** Model akhir dalam laporan memuat efek kategori serta sejumlah interaksi antara `Reviews`, `Genres`, dan `Category`. Karena model akhir menggunakan interaksi, pengaruh suatu variabel tidak sebaiknya ditafsirkan secara terpisah dari variabel yang berinteraksi dengannya.

## ✅ Kesimpulan

Analisis ini menerapkan regresi logistik biner untuk mempelajari faktor-faktor yang berkaitan dengan rating aplikasi di Google Play Store. Data diproses melalui penanganan missing value, penyesuaian tipe data, pengelompokan kategori, serta pengubahan rating menjadi dua kelas, yaitu rating ≥ 4 dan rating < 4.

Dari perbandingan model, **regresi logistik dengan interaksi hasil backward elimination** dipilih karena memiliki AIC terendah di antara model yang dilaporkan. Uji ANOVA juga mendukung penggunaan model dengan interaksi dibandingkan model tanpa interaksi. Model menghasilkan akurasi sekitar **72,62%** dan AUC sebesar **0,652**.

Meski demikian, uji Hosmer–Lemeshow menghasilkan p-value yang sangat kecil, yang menunjukkan bahwa kecocokan model dengan data belum memadai. Oleh sebab itu, hasil prediksi perlu digunakan secara hati-hati. Analisis lanjutan dapat mempertimbangkan pemeriksaan spesifikasi model, evaluasi pada data uji terpisah, peninjauan kalibrasi, serta penanganan ketidakseimbangan kelas bila ditemukan.

Secara keseluruhan, proyek ini memberikan pengalaman dalam proses analisis data end-to-end: mulai dari preprocessing, eksplorasi hubungan antarvariabel, pemodelan regresi logistik, seleksi model, hingga evaluasi performa dan interpretasi hasil.

## ⚠️ Keterbatasan dan Catatan

- Hasil yang ditampilkan merupakan hasil yang dilaporkan dalam dokumen analisis.
- Akurasi bergantung pada ambang klasifikasi yang digunakan; dalam laporan, ambang yang digunakan adalah 0,50.
- AUC 0,652 menunjukkan bahwa kemampuan pemisahan kelas masih terbatas dan perlu ditingkatkan.
- Hasil uji Hosmer–Lemeshow mengindikasikan bahwa model belum fit terhadap data, sehingga akurasi saja tidak cukup untuk menyatakan model sudah baik.
- Hubungan yang ditemukan oleh regresi tidak dengan sendirinya membuktikan hubungan sebab-akibat.
- Jumlah data setelah preprocessing dilaporkan sebanyak 9.367 baris; perhitungan evaluasi dalam laporan menggunakan penyebut 9.366 pada tabel klasifikasi. Perbedaan ini sebaiknya diperiksa kembali pada kode analisis sebelum hasil dipublikasikan sebagai hasil final.

## 🛠️ Tools dan Referensi

### Tools

- **R** — preprocessing, pemodelan regresi logistik, seleksi model, dan evaluasi.
- **`car`** — pemeriksaan VIF.
- **`ResourceSelection`** — uji Hosmer–Lemeshow.
- **`gmodels`** — tabel klasifikasi.
- **`pROC`** — analisis kurva ROC dan AUC.

### Referensi

1. Agresti, A. (2013). *Categorical Data Analysis* (3rd ed.). Wiley.
2. Lavanya. *Google Play Store Apps Dataset*. Kaggle: [https://www.kaggle.com/datasets/lava18/google-play-store-apps](https://www.kaggle.com/datasets/lava18/google-play-store-apps)

## 👥 Tim Peneliti

Penelitian ini dikerjakan oleh mahasiswa Program Studi Matematika, Fakultas Sains dan Teknologi, UIN Syarif Hidayatullah Jakarta.

- Rio Tri Wibowo
- Aisya Shalifa
- Arjuananta Malik
- Rachmat Nadhif Priatno
- Kevin Septiyan Candra

Berdasarkan laporan, kontribusi tim mencakup perumusan masalah, penyusunan laporan, pembuatan presentasi, dan presentasi hasil.

---

<p align="center">
  Dibuat sebagai dokumentasi proyek analisis data dan pemodelan statistik menggunakan regresi logistik.
</p>
