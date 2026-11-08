# README — Project B15: Karakteristik Ekspresi Gen Berdasarkan Sampel

Notebook ini menganalisis karakteristik ekspresi gen pada *cell line* kanker, melakukan reduksi dimensi menggunakan PCA, lalu mencari pola kelompok (*clustering*). Hasil clustering dibandingkan dengan informasi jaringan dan respons obat untuk keperluan evaluasi serta interpretasi.

## 1. Struktur folder

Simpan notebook dan kedua dataset dalam satu folder agar pemanggilan file pada notebook dapat berjalan tanpa mengubah path.

```text
Project-B15/
├── Proyek_Kelompok_7.ipynb
├── GDSC_DATASET.csv
└── CCLE_RNAseq_rsem_genes_tpm_20180929.txt.gz
```

**Dataset yang diperlukan:**

| File | Keterangan |
|---|---|
| `GDSC_DATASET.csv` | Data respons obat, termasuk `LN_IC50`, `AUC`, `Z_SCORE`, serta metadata *cell line* kanker. |
| `CCLE_RNAseq_rsem_genes_tpm_20180929.txt.gz` | Data ekspresi gen RNA-seq dalam nilai TPM. Notebook mengharapkan file terkompresi `.txt.gz` ini tersedia dengan nama tersebut. |

## 2. Persyaratan

- Python 3.10 atau versi yang kompatibel dengan pustaka di bawah.
- Jupyter Notebook atau JupyterLab.
- RAM yang memadai. Matriks ekspresi gen berukuran besar, sehingga proses pemuatan data, transformasi, PCA, dan validasi dapat membutuhkan memori serta waktu komputasi yang cukup besar.

## 3. Instalasi
Install pustaka yang digunakan notebook:

```bash
pip install jupyterlab notebook pandas numpy matplotlib seaborn scipy scikit-learn
```

Notebook menggunakan pustaka utama berikut:

- **pandas** dan **numpy** — pengolahan data numerik dan tabel.
- **matplotlib** dan **seaborn** — visualisasi dan EDA.
- **scipy** — uji statistik serta analisis hierarki.
- **scikit-learn** — preprocessing, PCA, validasi, metrik evaluasi, dan algoritma clustering.

## 4. Menjalankan notebook

1. Pastikan dua file dataset berada di folder yang sama dengan notebook.
2. Buka terminal pada folder proyek.
3. Jalankan:

   ```bash
   jupyter lab
   ```

   atau:

   ```bash
   jupyter notebook
   ```

4. Buka `Proyek_Kelompok_7.ipynb`.
5. Pilih kernel Python dari environment yang sudah dipasangi pustaka.
6. Jalankan sel secara berurutan dari bagian paling atas sampai paling bawah (**Run All** atau `Shift + Enter` per sel).

**Penting:** Jalankan notebook dari awal secara berurutan. Beberapa bagian menggunakan variabel dan hasil preprocessing yang dibuat pada sel sebelumnya. Menjalankan sel di bagian tengah tanpa menjalankan bagian sebelumnya dapat menimbulkan error seperti `NameError`.

## 5. Alur analisis notebook

Secara umum, notebook disusun dalam tahapan berikut:

1. **Data Understanding** — membaca struktur, ukuran, tipe data, nilai kosong, statistik dasar, dan kategori pada dataset GDSC dan CCLE.
2. **EDA (Exploratory Data Analysis)** — mengeksplorasi distribusi respons obat (`LN_IC50`, `AUC`) dan karakteristik ekspresi gen.
3. **Data Quality Findings** — memeriksa kualitas data, termasuk missing value, duplikasi, perbedaan identitas *cell line*, serta perubahan cakupan setelah penggabungan data.
4. **Preprocessing** — merapikan struktur data, menangani missing value dan outlier, menata fitur kategorikal dan skala, mencocokkan GDSC dengan CCLE, serta menghindari *data leakage*.
5. **Initial Feature Engineering dan Data Pipeline** — membangun fitur dan menyusun data analisis dari hasil pencocokan kedua dataset.
6. **Reduksi dimensi** — melakukan transformasi ekspresi gen dan PCA. Perbandingan model menggunakan ruang fitur komponen utama yang sama sesuai konfigurasi eksperimen.
7. **Pemodelan dan evaluasi clustering** — membandingkan empat pendekatan:
   - **K-Means** sebagai baseline.
   - **Hierarchical Clustering (Agglomerative, Ward)** sebagai kandidat.
   - **DBSCAN** sebagai kandidat berbasis kepadatan.
   - **Gaussian Mixture Model (GMM)** sebagai kandidat berbasis campuran distribusi probabilistik.
8. **Validasi dan kestabilan** — membandingkan model menggunakan metrik internal, kesesuaian terhadap label pembanding, serta uji kestabilan melalui validasi silang/subsampel.
9. **Evaluasi final** — notebook memisahkan development set dan test set. Konfigurasi dipilih pada development set; test set digunakan untuk evaluasi akhir baseline yang telah ditetapkan.

## 6. Catatan penting tentang model

- Proyek ini merupakan **unsupervised learning**. Label jaringan dan `LN_IC50` digunakan untuk evaluasi/interpretasi, bukan sebagai target untuk melatih clustering.
- K-Means, Ward, dan GMM menggunakan jumlah cluster (`k`) yang ditentukan dalam eksperimen.
- DBSCAN **tidak menggunakan parameter jumlah cluster (`k`)**. Jumlah cluster ditentukan berdasarkan kepadatan data; sampel yang tidak masuk cluster dapat diberi label *noise* (`-1`).
- Jangan menyamakan label cluster dengan label kelas kanker. Nomor cluster hanyalah identitas kelompok yang dibentuk algoritma.
- Pada bagian evaluasi final, test set tidak digunakan untuk memilih model atau parameter agar evaluasi tidak mengalami kebocoran informasi.

## 7. Jika terjadi masalah

| Masalah | Hal yang perlu diperiksa |
|---|---|
| `FileNotFoundError` | Pastikan kedua dataset berada di folder kerja notebook dan nama file sama persis dengan yang dipanggil kode. |
| `ModuleNotFoundError` | Install pustaka yang belum tersedia melalui `pip install nama-pustaka`, lalu pilih kernel dari environment yang benar. |
| `NameError` | Jalankan ulang notebook dari sel pertama secara berurutan agar seluruh variabel terbentuk. |
| Kernel berhenti / kehabisan memori | Tutup aplikasi lain, gunakan komputer dengan RAM lebih besar, dan hindari menjalankan beberapa notebook berat secara bersamaan. |
| Proses berjalan lama | Waktu komputasi dapat meningkat pada pemuatan matriks ekspresi, seleksi fitur, PCA, clustering, dan validasi silang. Tunggu proses selesai sebelum menjalankan sel berikutnya. |

Jika mengubah nama atau lokasi dataset, sesuaikan juga path pada sel pemanggilan `pd.read_csv(...)` di notebook.

## 8. Hasil yang diharapkan

Setelah seluruh sel berhasil dijalankan, notebook menampilkan:

- Ringkasan dan visualisasi karakteristik dataset.
- Temuan kualitas data serta hasil preprocessing dan pencocokan GDSC–CCLE.
- Hasil transformasi ekspresi gen dan reduksi dimensi PCA.
- Perbandingan hasil empat algoritma clustering.
- Metrik evaluasi, kestabilan, dan profil cluster.
- Evaluasi baseline final pada test set.

---

**Project ID:** B15  
**Topik:** Karakteristik Ekspresi Gen Berdasarkan Sampel (reduksi dimensi dan pencarian pola)
