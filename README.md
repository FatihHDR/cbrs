# Case-Based Reasoning: Analisis Putusan Pengadilan MA

Proyek ini mengimplementasikan sistem **Case-Based Reasoning (CBR)** untuk menganalisis dan mencari kemiripan antar putusan pengadilan dari situs resmi Mahkamah Agung RI ([putusan3.mahkamahagung.go.id](https://putusan3.mahkamahagung.go.id)).

---

## Struktur Direktori

```
case-base-reasoning/
├── data/
│   ├── raw/
│   │   ├── pdf/               # File PDF putusan yang diunduh
│   │   └── metadata/          # CSV hasil scraping metadata
│   └── processed/
│       ├── teks_ekstrak/      # Teks yang diekstrak dari PDF (.txt)
│       └── case_base/         # Case base CBR dalam format JSON & CSV
├── notebooks/
│   ├── 01_scraping_metadata.ipynb   # Tahap 1: Scraping metadata
│   ├── 02_unduh_pdf.ipynb           # Tahap 2: Unduh PDF
│   ├── 03_ekstrak_teks.ipynb        # Tahap 3: Ekstraksi teks
│   ├── 04_bangun_case_base.ipynb    # Tahap 4: Bangun case base CBR
│   └── 05_demo_cbr.ipynb            # Tahap 5: Demo pencarian kasus
├── requirements.txt
└── README.md
```

---

## Instalasi

### 1. Clone repositori

```bash
git clone https://github.com/<username>/case-base-reasoning.git
cd case-base-reasoning
```

### 2. Buat virtual environment (opsional tapi direkomendasikan)

```bash
python -m venv venv
source venv/bin/activate        # Linux/Mac
# atau
venv\Scripts\activate           # Windows
```

### 3. Install dependensi

```bash
pip install -r requirements.txt
```

---

## Cara Menjalankan Pipeline

Jalankan notebook **secara berurutan** dari tahap 1 hingga 5.

### Menggunakan Jupyter Notebook

```bash
jupyter notebook
```

Kemudian buka folder `notebooks/` dan jalankan setiap notebook dari atas ke bawah.

### Menggunakan Jupyter Lab

```bash
jupyter lab
```

---

## Deskripsi Setiap Tahap

### Tahap 1 — Scraping Metadata (`01_scraping_metadata.ipynb`)

Mengambil daftar putusan dari situs MA berdasarkan kategori dan sub-kategori yang dipilih pengguna.

- **Input:** Pilihan pengguna (kategori, sub-kategori, jumlah halaman, filter tahun/keyword)
- **Output:** `data/raw/metadata/metadata_[kategori]_[timestamp].csv`

```bash
jupyter notebook notebooks/01_scraping_metadata.ipynb
```

Kategori yang tersedia:
| Kode | Kategori | Contoh Sub-Kategori |
|------|----------|---------------------|
| 1 | Perdata Umum | Wanprestasi, PMH, Waris, Tanah |
| 2 | Perdata Khusus | PHI, Kepailitan, HKI, PKPU |
| 3 | Perdata Agama | Perceraian, Waris Islam, Harta Bersama |

---

### Tahap 2 — Unduh PDF (`02_unduh_pdf.ipynb`)

Mengunduh file PDF putusan berdasarkan URL detail di metadata CSV.

- **Input:** `data/raw/metadata/metadata_*.csv`
- **Output:** `data/raw/pdf/*.pdf` + `data/raw/metadata/log_unduhan_*.csv`

```bash
jupyter notebook notebooks/02_unduh_pdf.ipynb
```

Parameter yang dapat dikonfigurasi: path CSV, maksimal PDF yang diunduh, delay antar request.

---

### Tahap 3 — Ekstraksi Teks (`03_ekstrak_teks.ipynb`)

Mengekstrak teks dari file PDF menggunakan `pdfplumber` (dengan fallback ke `PyPDF2`).

- **Input:** `data/raw/pdf/*.pdf`
- **Output:** `data/processed/teks_ekstrak/*.txt`

```bash
jupyter notebook notebooks/03_ekstrak_teks.ipynb
```

---

### Tahap 4 — Bangun Case Base CBR (`04_bangun_case_base.ipynb`)

Memproses file teks menjadi struktur kasus terstruktur untuk CBR, termasuk ekstraksi amar putusan, pokok perkara, dasar hukum, dan fitur untuk similarity matching.

- **Input:** `data/processed/teks_ekstrak/*.txt`
- **Output:**
  - `data/processed/case_base/case_base_[timestamp].json`
  - `data/processed/case_base/case_base_[timestamp].csv`

```bash
jupyter notebook notebooks/04_bangun_case_base.ipynb
```

---

### Tahap 5 — Demo CBR (`05_demo_cbr.ipynb`)

Demonstrasi pencarian kasus serupa menggunakan **Weighted Feature Similarity** berdasarkan kata kunci, jenis perkara, pengadilan, dan kesamaan teks.

- **Input:** `data/processed/case_base/case_base_*.json`
- **Output:** Tampilan hasil pencarian + `data/processed/case_base/hasil_cbr_*.csv`

```bash
jupyter notebook notebooks/05_demo_cbr.ipynb
```

Contoh query:
```
Deskripsi kasus: sengketa tanah waris antara ahli waris atas sertifikat hak milik
Filter jenis   : Perdata Umum
Filter pengadilan: (kosong = semua)
Jumlah hasil   : 5
```

---

## Catatan Penting

- **HTTP 403:** Situs MA dapat memblokir request otomatis. Jika terjadi, tingkatkan nilai `DELAY_DETIK` (minimal 3–5 detik) atau coba di waktu berbeda.
- **Penggunaan Etis:** Gunakan hanya untuk keperluan akademis/riset. Patuhi ketentuan penggunaan situs resmi MA.
- **Urutan Notebook:** Tahap harus dijalankan berurutan karena setiap tahap menggunakan output dari tahap sebelumnya.

---

## Dependensi Utama

| Library | Kegunaan |
|---------|----------|
| `requests` + `beautifulsoup4` | Scraping HTML |
| `pandas` | Manipulasi data tabular |
| `pdfplumber` / `PyPDF2` | Ekstraksi teks dari PDF |
| `tqdm` | Progress bar |
| `jupyter` + `notebook` | Menjalankan notebook |
