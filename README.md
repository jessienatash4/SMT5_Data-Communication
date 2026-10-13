# Deteksi Gelombang PQRST pada Sinyal ECG Menggunakan Discrete Wavelet Transform (DWT)

## 1. Gambaran Umum Program

`Program_Week_6_PQRST_DWT.ipynb` Program ini mengolah sinyal elektrokardiogram (ECG) menggunakan **Discrete Wavelet Transform (DWT)** dan menampilkan tahapan utama analisis, yaitu:

1. Sinyal ECG asli dan sinyal setelah prapemrosesan.
2. Koefisien filter DWT, fungsi scaling, dan fungsi wavelet.
3. Koefisien aproksimasi (`A_j`) dan detail (`D_j`) pada setiap tingkat dekomposisi.
4. Sinyal ECG dengan perkiraan penanda gelombang P, Q, R, S, dan T.
5. Berkas CSV berisi indeks, waktu, dan amplitudo titik PQRST yang terdeteksi.

## 2. Dataset

- **Nama berkas:** `ptbdb_normal.csv`
- **Jumlah sampel/rekaman yang dilaporkan:** 14.552
- **Frekuensi sampling:** 125 Hz
- **Interval sampling:** 0,008 detik per sampel
- **Subset data:** ECG normal

Notebook memeriksa struktur CSV sebelum memilih segmen sinyal untuk divisualisasikan. Jika berkas berisi beberapa segmen ECG dalam baris yang berbeda, analisis dilakukan pada satu segmen terpilih. Baris-baris tersebut tidak digabungkan menjadi satu sinyal kontinu.

> **Catatan:** pastikan struktur CSV sebelum menafsirkan angka 14.552 sebagai jumlah titik waktu dalam satu rekaman kontinu. Pada sejumlah dataset ECG, angka tersebut dapat merujuk pada jumlah rekaman atau segmen.

## 3. Metodologi

### 3.1 Memuat dan memilih sinyal ECG

Berkas CSV diunggah ke Google Colab dan dibaca menggunakan pandas. Notebook memeriksa apakah kolom terakhir tampak berisi label biner. Selanjutnya, satu segmen dipilih untuk divisualisasikan.

### 3.2 Preprocessing

Segmen ECG diproses menggunakan:

- **Filter bandpass Butterworth:** 0,5–40 Hz.
- **Penyaringan tanpa pergeseran fase:** menggunakan `sosfiltfilt`.
- **Normalisasi z-score:** mengurangi nilai rata-rata sinyal dan membaginya dengan simpangan baku.

Filter dirancang berdasarkan frekuensi sampling 125 Hz. Frekuensi batas atas harus berada di bawah frekuensi Nyquist, yaitu 62,5 Hz.

### 3.3 Visualisasi filter dan fungsi wavelet

Notebook menggunakan wavelet **Daubechies 4 (`db4`)** dan menampilkan:

- Koefisien filter dekomposisi low-pass, `h[n]`.
- Koefisien filter dekomposisi high-pass, `g[n]`.
- Fungsi scaling, `φ(t)`.
- Fungsi wavelet, `ψ(t)`.

Dalam PyWavelets, `dec_lo` dan `dec_hi` masing-masing merupakan koefisien filter dekomposisi low-pass dan high-pass.

### 3.4 Dekomposisi DWT multitingkat

Sinyal ECG yang telah dipraproses didekomposisi hingga tingkat 5 menggunakan `pywt.wavedec`.

Susunan koefisiennya adalah:

- `A5`: koefisien aproksimasi tingkat 5.
- `D5`, `D4`, `D3`, `D2`, `D1`: koefisien detail dari tingkat dekomposisi paling kasar hingga paling halus.

Panjang larik koefisien berbeda dari panjang sinyal ECG asli karena DWT melakukan penyaringan dan penurunan laju sampel. Karena itu, sumbu horizontal pada grafik koefisien menunjukkan indeks koefisien, bukan waktu dalam detik secara langsung.

Dengan frekuensi sampling 125 Hz, rentang frekuensi idealnya diperkirakan sebagai berikut:

| Komponen | Perkiraan rentang frekuensi |
|---|---:|
| `D1` | 31,25–62,5 Hz |
| `D2` | 15,625–31,25 Hz |
| `D3` | 7,8125–15,625 Hz |
| `D4` | 3,90625–7,8125 Hz |
| `D5` | 1,953125–3,90625 Hz |
| `A5` | 0–1,953125 Hz |

Rentang ini merupakan pendekatan ideal. Filter wavelet yang sebenarnya tidak memiliki batas frekuensi yang sepenuhnya tajam.

### 3.5 Rekonstruksi komponen untuk membantu deteksi QRS

Notebook merekonstruksi beberapa komponen detail DWT dan menggabungkan `D2` serta `D3` untuk membentuk sinyal yang menonjolkan kompleks QRS. Energi sinyal tersebut dihaluskan, kemudian puncak-puncaknya digunakan untuk mencari kandidat lokasi gelombang R.

### 3.6 Perkiraan penanda PQRST

Setelah kandidat gelombang R ditemukan, posisi gelombang lainnya diperkirakan sebagai berikut:

- **Q:** titik minimum lokal sebelum R.
- **R:** kandidat puncak R yang disesuaikan dengan sinyal ECG hasil prapemrosesan.
- **S:** titik minimum lokal setelah R.
- **P:** titik maksimum lokal pada jendela waktu tertentu sebelum R.
- **T:** titik maksimum lokal pada jendela waktu tertentu setelah R.

Metode ini menggunakan aturan heuristik berdasarkan titik ekstrem lokal dan jendela waktu tetap. Metode dapat salah mengidentifikasi gelombang P atau T, terutama pada bentuk gelombang yang tidak umum, sinyal bising, denyut yang tidak lengkap, atau segmen yang mengandung nilai pengisi (*padding*). Karena itu, hasil perlu diperiksa secara visual.

## 4. Perangkat dan Pustaka

Notebook menggunakan:

- Python
- NumPy
- pandas
- Matplotlib
- SciPy
- PyWavelets
- Fitur unggah dan unduh berkas Google Colab

## 5. Cara Menjalankan

1. Buka notebook di Google Colab.
2. Jalankan sel instalasi pustaka.
3. Jalankan sel unggah, lalu unggah `ptbdb_normal.csv`.
4. Jalankan sel-sel berikutnya secara berurutan.
5. Periksa grafik ECG asli dan hasil prapemrosesan.
6. Periksa grafik koefisien filter serta fungsi wavelet.
7. Periksa grafik koefisien `A5` dan `D1`–`D5`.
8. Periksa grafik ECG dengan penanda PQRST.
9. Unduh `pqrst_detection_results.csv` dari sel terakhir.

## 6. Ouput

1. Grafik sinyal ECG asli.
2. Grafik perbandingan sinyal ECG asli dan hasil prapemrosesan.
3. Grafik koefisien filter `h[n]` dan `g[n]`.
4. Grafik fungsi scaling dan fungsi wavelet.
5. Grafik koefisien DWT `A5` dan `D1`–`D5`.
6. Grafik komponen detail hasil rekonstruksi dan sinyal penonjol QRS.
7. Grafik ECG dengan perkiraan penanda P, Q, R, S, dan T.
8. Berkas `pqrst_detection_results.csv` yang memuat indeks sampel, waktu dalam detik, dan amplitudo ternormalisasi untuk setiap titik yang terdeteksi.
