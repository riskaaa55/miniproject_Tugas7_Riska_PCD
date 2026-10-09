# Miniproject Pengolahan Citra Ijazah

## 1. Deskripsi Proyek

Miniproject ini membuat prototype pengolahan citra ijazah untuk:
- Memproses citra ijazah dengan grayscale dan image enhancement.
- Mengambil Region of Interest (ROI) nomor ijazah dan area tanda tangan.
- Membaca nomor ijazah menggunakan OCR Tesseract.
- Membandingkan beberapa metode preprocessing berdasarkan Character Error Rate (CER).
- Menerapkan thresholding dan morphology pada area tanda tangan.
- Menghasilkan tabel hasil pengujian untuk sembilan citra dan mengekspornya ke CSV.

## 2. Alur Sistem

```text
Citra Ijazah
    |
    v
Pemeriksaan Orientasi
    |
    v
Grayscale
    |
    v
Image Enhancement
    |
    +----------------------------+
    |                            |
    v                            v
ROI Nomor Ijazah             ROI Tanda Tangan
    |                            |
    v                            v
Preprocessing                  Thresholding
    |                            |
    v                            v
OCR Tesseract                 Morphology
    |                            |
    v                            v
Nomor Ijazah                  Deteksi berbasis piksel
    |                            |
    +-------------+--------------+
                  v
          Hasil Verifikasi
```

## 3. Teknologi dan Library

- Python
- OpenCV (`cv2`) untuk pengolahan citra
- Tesseract OCR dan `pytesseract` untuk pengenalan karakter
- NumPy untuk operasi numerik
- Pandas untuk tabel hasil dan ekspor CSV
- Matplotlib untuk menampilkan citra dan hasil preprocessing
- Google Colab sebagai lingkungan eksekusi

## 4. Metode yang Digunakan

### Grayscale
Mengubah citra berwarna menjadi citra intensitas abu-abu untuk menyederhanakan pemrosesan.

### CLAHE
Contrast Limited Adaptive Histogram Equalization meningkatkan kontras secara lokal agar karakter lebih mudah dibedakan dari latar.

### Denoising dan Sharpening
Denoising mengurangi sebagian noise. Sharpening mempertegas tepi karakter, tetapi jika terlalu kuat dapat memperbesar artefak.

### Thresholding
- **Global Threshold:** menerapkan satu ambang intensitas ke seluruh citra.
- **Otsu Threshold:** memilih ambang secara otomatis berdasarkan histogram intensitas.
- **Adaptive Threshold:** menentukan ambang berdasarkan lingkungan lokal piksel.

### Morphology
- **Opening:** operasi erosi lalu dilasi untuk membantu mengurangi komponen kecil.
- **Closing:** operasi dilasi lalu erosi untuk membantu menutup celah kecil.

### OCR
Tesseract digunakan untuk membaca karakter pada ROI nomor ijazah.

### Character Error Rate (CER)
CER membandingkan teks OCR dengan nomor acuan menggunakan jarak edit karakter:

`CER = jumlah kesalahan karakter / jumlah karakter pada teks acuan`

Semakin rendah nilai CER, semakin dekat hasil OCR dengan teks acuan. CER 0 berarti tidak ada kesalahan karakter setelah normalisasi yang diterapkan program.

### Deteksi Tanda Tangan
Prototype menghitung rasio piksel foreground pada ROI setelah thresholding dan morphology. Ini merupakan pendekatan dasar berbasis piksel, bukan model pengenal tanda tangan terlatih. Cap, tulisan, noda, atau pola latar dapat memengaruhi hasil, sehingga status `PRESENT` atau `ABSENT` harus diperiksa terhadap label manual.

## 5. Cara Menjalankan di Google Colab

1. Buka [Google Colab](https://colab.research.google.com/).
2. Pilih **Upload notebook**, lalu unggah `Miniproject_Pengolahan_Citra_Ijazah.ipynb`.
3. Pastikan sembilan gambar pengujian tersedia di runtime Colab.
4. Jalankan sel instalasi dependensi.
5. Jalankan sel pencarian gambar. Jika gambar tidak ditemukan, sesuaikan `DATA_DIR` dengan folder gambar yang benar, atau pastikan variabel `gambar_files` berisi path gambar yang valid.
6. Periksa pratinjau rotasi 0°, 90°, 180°, dan 270°. Atur `ROTATION_DEGREES` sesuai orientasi yang membuat teks terbaca normal. Pada gambar yang diuji dalam sesi ini, orientasi yang dipakai adalah 90°.
7. Atur `ROI_NUMBER` dan `ROI_SIGNATURE` berdasarkan gambar setelah rotasi. Jalankan sel ROI dan periksa kedua hasil crop sebelum meneruskan proses.
8. Isi `REFERENCE_NUMBER` dengan nomor ijazah yang benar-benar tercetak pada gambar acuan.
9. Jalankan pipeline untuk semua gambar.
10. Jalankan bagian perbandingan CER, periksa visualisasi preprocessing, dan tinjau status tanda tangan.
11. Notebook akan menyimpan hasil ke `hasil_miniproject_ijazah.csv`. Unduh file tersebut untuk dokumentasi hasil.

**Penting:** koordinat ROI bergantung pada ukuran, orientasi, dan posisi isi gambar. Jangan menyalin koordinat contoh tanpa memeriksa hasil crop.

## 6. Hasil Pengujian CER

Pada hasil yang terlihat dalam pengujian sembilan citra, nilai rata-rata CER yang ditampilkan adalah:

| Metode | Rata-rata CER |
|---|---:|
| Grayscale | 0.000000 |
| CLAHE | 0.000000 |
| Otsu Threshold | 0.000000 |
| Global Threshold | 0.029630 |
| CLAHE + Denoising + Sharpening | 0.111111 |
| Adaptive Threshold | 0.333333 |

Grayscale, CLAHE, dan Otsu Threshold memiliki nilai CER terendah yang sama pada pengujian ini. Karena hasilnya seri, tidak tepat menyatakan hanya satu metode yang paling unggul berdasarkan CER. Kesimpulan ini berlaku untuk sembilan citra dan pengaturan ROI yang diuji, bukan untuk semua jenis ijazah.

## 7. Output Prototype

Contoh format output yang diharapkan:

```text
Input: ijazah_001.jpg
Nomor Ijazah: 571012022000056
Tanda Tangan: PRESENT
```

Nomor di atas merupakan contoh berdasarkan citra yang diuji. Hasil nyata harus dicocokkan dengan dokumen sumber.

## 8. Keterbatasan

- ROI perlu disesuaikan dan diverifikasi untuk memastikan nomor serta tanda tangan tidak terpotong.
- OCR dapat keliru pada citra buram, kontras rendah, atau beresolusi rendah.
- Deteksi tanda tangan berbasis rasio piksel dapat salah mengklasifikasikan cap, teks, noda, atau pola latar sebagai tanda tangan.
- Untuk mengukur performa deteksi `PRESENT` dan `ABSENT`, diperlukan contoh gambar dengan label manual yang diketahui, lalu dibandingkan menggunakan confusion matrix atau metrik klasifikasi.
- Nilai CER hanya bermakna jika nomor acuan benar dan digunakan konsisten.

## 9. Struktur Repository yang Disarankan

```text
miniproject-pengolahan-citra-ijazah/
├── Miniproject_Pengolahan_Citra_Ijazah.ipynb
├── README.md
└── hasil_miniproject_ijazah.csv   # hasil setelah notebook dijalankan
```

Jangan mengunggah ijazah asli atau informasi pribadi ke repository publik tanpa izin. Jika perlu menyertakan data, gunakan gambar yang sudah dianonimkan atau data uji yang diizinkan.

## 10. Mengunggah ke GitHub

1. Masuk ke GitHub dan buat repository baru.
2. Unggah notebook `.ipynb` dan file `README.md`.
3. Tambahkan CSV hasil jika diperlukan dan aman untuk dibagikan.
4. Buka halaman repository dan salin URL-nya untuk dikumpulkan.
