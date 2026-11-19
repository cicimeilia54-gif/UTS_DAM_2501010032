# UTS Data Acquisition & Management — Data Pariwisata Indonesia

Proyek ini merupakan tugas mata kuliah **Data Acquisition & Management** yang membahas proses pengumpulan, pemeriksaan, penyimpanan, dan penggabungan data. Topik yang digunakan adalah pariwisata Indonesia dengan dataset utama **Indonesia Tourism Destination** dari Kaggle.

Ujian Tengah Semester **Data Acquisition & Management** (Kelas E, Semester Ganjil 2026)
Program Studi Informatika — INSTIKI
Dosen: Dr. I Gede Totok Suryawan, S.Kom., M.T.

| | |
|---|---|
| **Nama** | Ni Komang Cici Meilia |
| **NIM** | 2501010032 |
| **Notebook pipeline (Colab)** | https://colab.research.google.com/drive/1LJRmEnwg6BwvfrJEjp9Q0iN1u9234MZv?usp=sharing |
| **Repositori** | https://github.com/cicimeilia54-gif/UTS_DAM_2501010032.git |


## Topik

**Analisis dan Akuisisi Data Destinasi Pariwisata di Indonesia**

Dataset utama berisi informasi destinasi wisata di lima kota, yaitu Jakarta, Yogyakarta, Bandung, Semarang, dan Surabaya. Data tambahan diperoleh melalui Open-Meteo API untuk kondisi cuaca kota, serta web scraping dari halaman contoh negara pada Scrape This Site.

## Sumber Data

### 1. Dataset dari Kaggle

- **Nama dataset:** Indonesia Tourism Destination
- **Pembuat:** A. Prabowo
- **Sumber:** [Kaggle – Indonesia Tourism Destination](https://www.kaggle.com/datasets/aprabowo/indonesia-tourism-destination)
- **Metode akuisisi:** Mengunduh dataset dari Kaggle, kemudian mengunggah berkas CSV ke Google Colab.
- **Berkas yang digunakan:**
  - `tourism_with_id.csv`
  - `tourism_rating.csv`
  - `user.csv`
  - `package_tourism.csv`

Dataset mencakup informasi destinasi wisata, kategori, kota, harga, rating, koordinat, rating pengguna, data pengguna, dan paket wisata. Berdasarkan pemeriksaan dataset yang dilakukan pada tahap sebelumnya, `tourism_with_id.csv` memiliki 437 baris, `tourism_rating.csv` memiliki 10.000 baris, `user.csv` memiliki 300 baris, dan `package_tourism.csv` memiliki 100 baris. Jumlah tersebut tetap perlu dicocokkan dengan hasil eksekusi notebook pada berkas yang digunakan.

### 2. Data API

- **Nama layanan:** Open-Meteo Weather API
- **Situs:** [https://open-meteo.com/](https://open-meteo.com/)
- **Endpoint:** `https://api.open-meteo.com/v1/forecast`
- **Metode akuisisi:** Mengirim permintaan HTTP menggunakan pustaka `requests`.
- **Cakupan lokasi:** Jakarta, Yogyakarta, Bandung, Semarang, dan Surabaya.
- **Informasi yang diambil:** Suhu, kecepatan angin, arah angin, kode cuaca, waktu pengamatan, serta koordinat lokasi.

Data JSON dari respons API disimpan sebagai `raw_weather_api.json`, kemudian dinormalisasi menjadi tabel CSV `processed_weather.csv`.

### 3. Data Web Scraping

- **Situs:** [Scrape This Site – Simple Countries](https://www.scrapethissite.com/pages/simple/)
- **Halaman robots.txt:** [https://scrapethissite.com/robots.txt](https://scrapethissite.com/robots.txt)
- **Metode:** Mengambil halaman HTML menggunakan `requests` dan mengekstrak elemen HTML menggunakan `BeautifulSoup`.
- **Informasi yang diekstrak:** Nama negara, ibu kota, populasi, dan luas wilayah.
- **Berkas hasil:** `processed_countries.csv`

**Catatan ruang lingkup:** Halaman scraping yang digunakan merupakan halaman contoh daftar negara, bukan sumber data destinasi wisata Indonesia. Data ini dicatat sebagai hasil latihan metode web scraping dan tidak digunakan dalam proses penggabungan dataset pariwisata dengan data cuaca.

## Struktur Proyek

Struktur direktori yang dibuat oleh notebook di Google Colab adalah sebagai berikut:

```text
Data_Acquisition_Management/
│
├── data/
│   ├── raw/
│   │   ├── tourism_with_id.csv
│   │   ├── tourism_rating.csv
│   │   ├── user.csv
│   │   ├── package_tourism.csv
│   │   ├── raw_weather_api.json
│   │   └── raw_scraped_page.html
│   │
│   └── processed/
│       ├── processed_weather.csv
│       ├── processed_countries.csv
│       ├── merged_tourism_weather.csv
│       ├── metadata_summary.txt
│       └── metadata_summary.csv
│
└── provenance.csv
```

Berkas dibuat ketika sel notebook terkait berhasil dijalankan. Karena itu, keberadaan setiap berkas pada folder akhir bergantung pada keberhasilan eksekusi notebook di Google Colab.

## Ringkasan Pengerjaan

### Soal 1 – Identifikasi Karakteristik Dataset

Notebook mengunggah dan membaca empat berkas CSV dari Kaggle menggunakan `pandas`. Pemeriksaan dilakukan untuk melihat:

- Jumlah baris dan kolom setiap berkas.
- Nama kolom dan tipe data.
- Contoh isi data.
- Ukuran berkas mentah.

Notebook juga menghitung estimasi kebutuhan penyimpanan selama satu tahun menggunakan rumus:

```text
Estimasi storage = total ukuran data saat ini × 12 × 1,3
```

Perhitungan tersebut mengasumsikan ukuran data bulanan sama dengan ukuran dataset saat ini, dengan tambahan cadangan penyimpanan sebesar 30%.

### Soal 2 – Lisensi dan Data Ethics Checklist

Notebook menyediakan checklist etika data yang terdiri dari sembilan butir pemeriksaan, meliputi kejelasan lisensi, PII, data sensitif, persetujuan, bias geografis, keamanan penyimpanan, batasan penggunaan komersial, atribusi, dan duplikasi data pengguna.

Notebook mencatat lisensi dataset Kaggle sebagai **CC0: Public Domain**. Informasi lisensi dan ketentuan penggunaan tetap perlu dicocokkan dengan halaman sumber dataset yang digunakan. Pada tabel pengguna, kolom `User_Id`, `Location`, dan `Age` perlu diperhatikan sebagai pengenal pseudonim dan informasi yang berpotensi menjadi quasi-identifier jika digabungkan dengan informasi lain.

### Soal 3 – Akuisisi Data Menggunakan API

Notebook menggunakan Open-Meteo API untuk mengambil kondisi cuaca pada lima kota yang tercakup dalam dataset pariwisata. Permintaan dilakukan dengan header `User-Agent`, timeout 10 detik, dan jeda 2 detik antarpermintaan.

Respons API disimpan dalam format JSON, lalu diubah menjadi DataFrame dan disimpan sebagai `processed_weather.csv`.

**Catatan:** Implementasi notebook mengambil satu data kondisi cuaca saat ini untuk setiap kota. Dengan demikian, target minimal 30 baris belum dibuktikan oleh rancangan pengambilan data ini; jumlah baris hasil perlu diperiksa dari output eksekusi. Notebook juga belum menerapkan mekanisme retry khusus untuk permintaan yang gagal.

### Soal 4 – Web Scraping dan Pemeriksaan robots.txt

Notebook mengambil isi `robots.txt` dari Scrape This Site dan mengunduh halaman Simple Countries. HTML disimpan sebagai `raw_scraped_page.html`, kemudian `BeautifulSoup` digunakan untuk mengekstrak maksimal 35 entri negara.

Hasil ekstraksi disimpan sebagai `processed_countries.csv` dengan kolom negara, ibu kota, populasi, dan luas wilayah.

**Catatan:** Notebook menampilkan isi `robots.txt`, tetapi belum terlihat melakukan pemeriksaan otomatis terhadap izin akses path menggunakan aturan `robots.txt`. Jeda antarpermintaan juga belum diterapkan pada bagian scraping. Kepatuhan scraping perlu dipastikan sebelum menyimpulkan bahwa halaman tersebut boleh diakses untuk proses ini.

### Soal 5 – Penggabungan dan Validasi Data

Notebook menggabungkan `tourism_with_id.csv` dengan `processed_weather.csv` menggunakan kolom `City` sebagai kunci penggabungan. Penggabungan menggunakan metode `inner` dan parameter `validate="many_to_one"`.

Hasil penggabungan disimpan sebagai `merged_tourism_weather.csv`. Notebook kemudian memeriksa duplikasi seluruh baris dan duplikasi berdasarkan `Place_Id`.

Ringkasan metadata hasil penggabungan disimpan dalam dua format:

- `metadata_summary.txt`
- `metadata_summary.csv`

Penggabungan ini menggunakan data destinasi wisata dan data cuaca. Data hasil scraping negara tidak disertakan dalam proses merge.

## Provenance dan Integritas Data

Notebook membuat catatan provenance yang memuat:

- Nama sumber data.
- Nama berkas.
- URL sumber.
- Tanggal akses.
- Informasi lisensi yang dicatat.
- Hash MD5 untuk pemeriksaan integritas berkas.

Catatan provenance dari dataset Kaggle, API Open-Meteo, dan hasil web scraping dikumpulkan ke dalam `provenance.csv`.

Hash MD5 digunakan untuk membantu memeriksa apakah berkas berubah setelah dicatat. Nilai hash bukan pengganti pemeriksaan lisensi atau bukti bahwa data sumber benar.

## Etika dan Keamanan

Beberapa langkah yang diterapkan dalam notebook:

- Menggunakan header `User-Agent` yang memuat alamat email kampus.
- Memberikan timeout pada permintaan HTTP.
- Memberikan jeda antarpermintaan API.
- Menyimpan data mentah di direktori `data/raw/` dan hasil olahan di `data/processed/`.
- Mencatat hash MD5 untuk pemeriksaan integritas.
- Memeriksa isi `robots.txt` sebelum mengambil halaman scraping.

Data pengguna dari dataset Kaggle perlu diperlakukan dengan hati-hati, khususnya kolom `User_Id`, `Location`, dan `Age`. Data tersebut tidak perlu ditampilkan atau disebarkan jika tidak dibutuhkan dalam analisis.

## Keterbatasan Pengerjaan

- Dataset Kaggle berfokus pada lima kota dan tidak mewakili seluruh destinasi wisata di Indonesia.
- Data API yang dirancang pada notebook merupakan kondisi cuaca saat permintaan dilakukan, bukan riwayat cuaca jangka panjang.
- Rancangan API mengambil satu respons per kota sehingga belum menunjukkan minimal 30 baris.
- Data scraping berasal dari daftar negara dan ibu kota, bukan data pariwisata Indonesia.
- Pemeriksaan `robots.txt` pada notebook masih berupa pengambilan dan penampilan isi berkas; izin akses path belum divalidasi secara otomatis.
- Klaim lisensi pada provenance perlu dipastikan kembali pada halaman resmi masing-masing sumber.
- Hasil akhir dan jumlah baris harus mengikuti output aktual setelah seluruh sel notebook dijalankan.

## Cara Menjalankan Notebook

1. Buka Google Colab.
2. Unggah atau buka file notebook `.ipynb`.
3. Jalankan sel secara berurutan dari bagian awal sampai akhir.
4. Saat diminta, unggah empat file CSV dari dataset Kaggle.
5. Periksa output setiap tahap, terutama jumlah baris hasil API, scraping, dan merge.
6. Periksa folder `Data_Acquisition_Management` untuk melihat berkas mentah, hasil olahan, dan provenance.

## Atribusi

- Dataset pariwisata: [Indonesia Tourism Destination – Kaggle](https://www.kaggle.com/datasets/aprabowo/indonesia-tourism-destination).
- Data cuaca: [Open-Meteo](https://open-meteo.com/).
- Sumber latihan web scraping: [Scrape This Site – Simple Countries](https://www.scrapethissite.com/pages/simple/).

---

**Catatan:** README ini menjelaskan alur dan berkas yang tertulis di notebook. Angka hasil akhir, status keberhasilan setiap permintaan, dan kepatuhan lisensi perlu dipastikan berdasarkan output eksekusi notebook serta halaman ketentuan sumber data.
