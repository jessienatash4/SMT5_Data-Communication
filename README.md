# Tugas Week 6: Deteksi Gelombang PQRST pada Sinyal ECG Menggunakan DWT

## Deskripsi
Program ini mendeteksi gelombang P, Q, R, S, dan T pada sinyal elektrokardiogram (ECG) menggunakan **Discrete Wavelet Transform (DWT)** dan aturan deteksi berbasis amplitudo serta interval waktu.

Program dijalankan menggunakan Python di Google Colab dan mengambil dataset langsung dari Kaggle melalui KaggleHub.

## Dataset
- Sumber: [ECG Dataset — Kaggle](https://www.kaggle.com/datasets/devavratatripathy/ecg-dataset)
- File yang digunakan: `ecg.csv`
- Dataset perlu diperiksa terlebih dahulu untuk memastikan kolom sinyal, label, dan frekuensi sampling yang benar.

## Metode
1. **Pengambilan data:** Mengunduh dataset Kaggle menggunakan KaggleHub.
2. **Preprocessing:** Memfilter sinyal dengan band-pass filter 0,5–40 Hz untuk mengurangi noise dan gangguan baseline, lalu melakukan normalisasi.
3. **Dekomposisi DWT:** Menggunakan wavelet `db4` (dapat diganti) untuk mendekomposisi sinyal. Komponen detail pada rentang frekuensi yang dipilih direkonstruksi untuk membantu mendeteksi kompleks QRS.
4. **Deteksi R:** Mencari kandidat puncak berdasarkan energi komponen DWT, lalu menyesuaikan posisinya ke puncak positif ECG terfilter.
5. **Deteksi P, Q, S, dan T:** Q dan S dicari sebagai minimum lokal di sekitar R. P dan T dicari berdasarkan deviasi amplitudo pada jendela waktu relatif terhadap R.
6. **Visualisasi dan ekspor:** Menampilkan hasil pada grafik serta menyimpan indeks sampel dan estimasi waktu gelombang ke CSV.

## Teknologi dan pustaka
- Python
- Google Colab
- KaggleHub
- NumPy
- pandas
- Matplotlib
- SciPy
- PyWavelets

## Cara Menjalankan
1. Buka notebook di Google Colab.
2. Jalankan cell instalasi pustaka.
3. Jalankan cell untuk mengunduh dataset dari Kaggle.
4. Periksa struktur `ecg.csv` dan pilih kolom sinyal yang sesuai.
5. Pastikan nilai frekuensi sampling (`FS`) sesuai dengan dokumentasi dataset.
6. Jalankan cell preprocessing, DWT, deteksi PQRST, visualisasi, dan ekspor hasil secara berurutan.

## Parameter yang Dapat Diubah
- `WAVELET`: jenis wavelet, misalnya `db4`, `db6`, `sym4`, atau `coif5`.
- `FS`: frekuensi sampling sinyal dalam Hz; harus diisi berdasarkan dokumentasi dataset.
- Batas filter dan interval pencarian PQRST: dapat disesuaikan dengan karakteristik sinyal.

## Output
- Grafik sinyal ECG setelah preprocessing.
- Grafik komponen hasil rekonstruksi DWT.
- Grafik ECG dengan penanda gelombang P, Q, R, S, dan T.
- File `hasil_deteksi_PQRST.csv` berisi indeks sampel dan estimasi waktu setiap gelombang.

## Keterbatasan
- Deteksi PQRST menggunakan kombinasi DWT dan aturan heuristik; DWT sendiri tidak secara otomatis memberi label gelombang.
- Deteksi P dan T berdasarkan deviasi amplitudo dapat keliru pada sinyal bising, morfologi abnormal, atau gelombang yang tumpang tindih.
- Dataset dengan label normal/abnormal belum tentu menyediakan anotasi posisi PQRST sebagai ground truth.
- Frekuensi sampling tidak boleh diasumsikan. Interpretasi waktu dalam detik hanya valid jika `FS` benar.
- Program ini merupakan prototipe pembelajaran pengolahan sinyal, bukan alat diagnosis klinis.

## Pengembangan Lanjutan
- Memastikan kolom ECG dan frekuensi sampling berdasarkan dokumentasi dataset.
- Menggunakan anotasi PQRST sebagai referensi validasi.
- Mengevaluasi sensitivitas, positive predictive value (PPV), dan galat waktu deteksi.
- Membandingkan beberapa wavelet dan parameter deteksi.

## Lisensi dan Penggunaan Dataset
Periksa ketentuan lisensi serta atribusi pada halaman dataset Kaggle sebelum mendistribusikan ulang data.

---
**Catatan:** Hasil deteksi harus diperiksa secara visual dan divalidasi terhadap anotasi referensi sebelum digunakan untuk analisis lebih lanjut.
