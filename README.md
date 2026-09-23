# Mini Project: Pengambilan dan Pembersihan Data Melalui API
### Pengambilan dan Eksplorasi Data TV Series Menggunakan TMDB API

**Nama:** Muhammad Amar Primus Firdaus
**Program:** AI Automation Engineer
**Judul Project:** Pengambilan dan Pembersihan Data Melalui API

---

## 📌 Tentang Project Ini

Project ini bertujuan untuk **mengambil data serial TV** dari **TMDB (The Movie Database) API**, lalu **membersihkan data tersebut** agar siap digunakan untuk analisis lebih lanjut. Secara garis besar, alurnya adalah:

```
Ambil API Key → Panggil API → Bungkus jadi Class → Bersihkan Data → Simpan ke CSV → Rangkum Hasil
```

Notebook ini cocok dipelajari oleh siapa saja yang ingin belajar cara:
- Mengambil data dari API pihak ketiga menggunakan Python
- Menyimpan credential (API key) dengan aman
- Menyusun kode pengambilan data ke dalam sebuah *class* (OOP)
- Melakukan pembersihan data (data cleaning) dengan pandas

---

## 🛠️ Persyaratan (Requirements)

Sebelum menjalankan notebook ini, pastikan sudah menyiapkan:

1. **Python 3.11+** beserta Jupyter Notebook/JupyterLab
2. **API Key dari TMDB** — daftar gratis di [themoviedb.org](https://www.themoviedb.org/) lalu buat API key di halaman pengaturan akun (Settings → API)
3. Library Python berikut:
   - `requests` — untuk mengirim permintaan ke API
   - `python-dotenv` — untuk membaca file `.env`
   - `pandas` — untuk mengolah data dalam bentuk tabel (DataFrame)

Install semua library dengan perintah:
```bash
pip install requests python-dotenv pandas
```

4. Buat file bernama **`.env`** di folder yang sama dengan notebook, lalu isi dengan:
```
API_KEY=isi_dengan_api_key_TMDB_kamu
```
> File `.env` ini berfungsi menyimpan API key secara terpisah dari kode program, sehingga key tidak ter-*expose* jika notebook dibagikan ke orang lain.

---

## 🚶 Langkah-Langkah dalam Notebook

Notebook ini terbagi menjadi **6 tahap** utama. Berikut penjelasan tiap tahap:

### **Tahap 1 — Mendapatkan dan Memuat API Key**
- Menginstall library `requests` dan `python-dotenv`.
- Membaca API key dari file `.env` menggunakan `load_dotenv()` dan `os.getenv("API_KEY")`.
- Notebook akan mencetak pesan apakah API key berhasil dimuat atau belum.

### **Tahap 2 — Melakukan Pemanggilan (Request) ke API**
- Menggunakan endpoint `discover/tv` dari TMDB untuk mendapatkan daftar serial TV.
- Parameter yang dikirim:
  | Parameter | Fungsi |
  |---|---|
  | `api_key` | Kredensial untuk autentikasi ke API |
  | `language` | Bahasa data yang ditampilkan (contoh: `en-US`) |
  | `page` | Halaman data yang diambil (tiap halaman berisi ±20 data) |
  | `sort_by` | Urutan data, misalnya `popularity.desc` (dari yang paling populer) |
- Melakukan uji coba mengambil 1 halaman data, lalu menampilkan salah satu contoh datanya (judul, ringkasan cerita/*overview*, dan tanggal tayang).

### **Tahap 3 — Mengubah Kode Menjadi Class (OOP)**
- Kode pemanggilan API dibungkus ke dalam sebuah **class** bernama `TMBDFavTV`, dengan fungsi utama `get_tv_data(target_rows)`.
- Tujuannya agar kode lebih rapi, terorganisir, dan bisa dipakai ulang tanpa menulis ulang logic yang sama.
- Cara kerja class ini:
  1. Menghitung berapa banyak halaman yang perlu diambil agar jumlah data mencukupi target (misalnya 1000 data ÷ 20 data/halaman ≈ 50 halaman).
  2. Melakukan looping request ke API untuk setiap halaman.
  3. Menggabungkan seluruh hasil menjadi satu **DataFrame** pandas.
- Notebook memanggil `tmbd.get_tv_data(1000)` untuk mengambil **1000 data serial TV**.

### **Tahap 4 — Membersihkan Data (Data Cleaning)**
Setelah data terkumpul, dilakukan pemeriksaan dan pembersihan berdasarkan 3 kriteria wajib:

1. **Nilai kosong (missing values)**
   - Ditemukan nilai kosong pada kolom `backdrop_path` (41 data) dan `poster_path` (14 data).
   - Karena kolom ini hanya berisi link gambar (bukan data penting untuk analisis), nilai kosong **tidak dihapus**, melainkan diisi dengan keterangan `"Tidak tersedia"`.

2. **Baris duplikat (data kembar)**
   - Ditemukan **67 baris data** yang memiliki `id` yang sama (menandakan serial TV yang sama muncul lebih dari sekali).
   - Baris duplikat ini **dihapus** menggunakan `drop_duplicates(subset="id")`, sehingga jumlah data berkurang dari **1000 → 933 baris**.

3. **Tipe data yang tidak sesuai**
   - Kolom `first_air_date` (tanggal tayang pertama) awalnya bertipe **teks (string)**.
   - Tipe data diubah menjadi **datetime** menggunakan `pd.to_datetime()` agar bisa dianalisis berdasarkan waktu (misalnya untuk mencari tren per tahun).

### **Tahap 5 — Menyimpan Hasil**
- Data yang sudah bersih (`df_bersih`) disimpan ke dalam file **`dataset_berita.csv`**.
- File tersebut kemudian dibaca kembali untuk memastikan proses penyimpanan berhasil (933 baris, 15 kolom).

### **Tahap 6 — Rangkuman Hasil (Bahan untuk Slide)**
Notebook menutup dengan mencetak ringkasan angka-angka penting dari keseluruhan proses, yaitu:

| Keterangan | Nilai |
|---|---|
| Sumber data | The Movie Database (TMDB) |
| Jenis data | Serial TV |
| Target jumlah data | 1000 |
| Jumlah baris sebelum dibersihkan | 1000 |
| Jumlah baris dataset akhir | 933 |
| Nilai kosong `backdrop_path` | 41 |
| Nilai kosong `poster_path` | 14 |
| Baris duplikat yang dibuang | 67 |
| Perubahan tipe data | `first_air_date`: str → datetime |

**Class yang dibuat:**
- `TMBDFavTV` — mengambil data serial TV dari TMDB API

**Function yang dibuat:**
- `bersihkan_path` — menangani nilai kosong pada kolom path gambar
- `ubah_ke_tanggal` — mengubah teks tanggal menjadi tipe datetime

---

## 📂 Struktur Data Akhir

Dataset akhir (`dataset_berita.csv`) memiliki **933 baris** dan **15 kolom**, di antaranya:
- `id` — ID unik serial TV di TMDB
- `name` / `original_name` — nama serial TV
- `overview` — ringkasan cerita
- `first_air_date` — tanggal tayang pertama (format datetime)
- `popularity`, `vote_average`, `vote_count` — metrik popularitas dan rating
- `genre_ids`, `origin_country`, `original_language` — informasi kategori dan asal
- `backdrop_path`, `poster_path` — link gambar (sudah diisi "Tidak tersedia" jika kosong)
- `adult`, `softcore` — penanda kategori konten

---

## ▶️ Cara Menjalankan Notebook

1. Pastikan file `.env` sudah dibuat dan berisi API key TMDB yang valid.
2. Buka notebook `mini_project_1_Muhammad_Amar_Primus_Firdaus.ipynb` di Jupyter Notebook/JupyterLab.
3. Jalankan setiap sel secara berurutan dari atas ke bawah (**Run All** atau `Shift + Enter` satu per satu).
4. Tunggu proses pengambilan data selesai (akan terlihat status `Page 1 | Status Code: 200`, dst., hingga seluruh halaman selesai diambil).
5. Setelah selesai, cek file **`dataset_berita.csv`** yang otomatis terbentuk di folder yang sama — itulah hasil akhir data yang sudah bersih dan siap dianalisis.

---

## 💡 Catatan Tambahan

- Nama file output adalah `dataset_berita.csv`, meskipun isinya adalah data **serial TV** (bukan berita) — ini hanya penamaan file dari penulis notebook.
- Jika jumlah `total_results` di TMDB berubah seiring waktu, jumlah data yang berhasil diambil bisa sedikit berbeda dari 1000 baris.
- Karena menggunakan API key pribadi, **jangan membagikan file `.env`** ke publik (misalnya saat mengunggah project ke GitHub, tambahkan `.env` ke dalam `.gitignore`).
