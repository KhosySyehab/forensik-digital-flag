# DIGITAL FORENSIC INVESTIGATION REPORT
## Investigasi Barang Bukti Digital Kelompok 2
### Disusun oleh: **Kelompok 5** | Mata Kuliah: Forensik Digital

---

### Informasi Kasus

| Parameter | Keterangan |
| :--- | :--- |
| **Judul Kasus** | Investigasi Barang Bukti Digital Kelompok 2: Identifikasi File Tersamar berdasarkan MIME Type |
| **Tim Penyelidik (Examiner)** | **Kelompok 5** (Forensik Digital) |
| **Pemilik Barang Bukti** | **Kelompok 2** |
| **Subjek Barang Bukti** | Data digital `KEBELET PSDM` (Logical Files) |
| **Tool Utama** | Autopsy 4.23.1 (case: `Fordig-Kelompok2`) |
| **Tanggal Analisis** | 5 Oktober 2026 |
| **Status Akhir** | **SOLVED** |

---

## Daftar Isi

1. [Ringkasan Eksekutif](#1-ringkasan-eksekutif)
2. [Lingkungan dan Metode Analisis](#2-lingkungan-dan-metode-analisis)
3. [Tahapan Investigasi](#3-tahapan-investigasi)
   - [3.1 Penambahan Data Source](#31-penambahan-data-source)
   - [3.2 Analisis MIME Type](#32-analisis-mime-type)
   - [3.3 Identifikasi File Mencurigakan dan Flag](#33-identifikasi-file-mencurigakan-dan-flag)
4. [Analisis Teknik Penyembunyian](#4-analisis-teknik-penyembunyian)
5. [Reproduksi Hasil](#5-reproduksi-hasil)
6. [Kesimpulan](#6-kesimpulan)

---

## 1. Ringkasan Eksekutif

Investigasi dilakukan terhadap data digital **KEBELET PSDM** milik Kelompok 2 menggunakan **Autopsy 4.23.1**. Data dianalisis sebagai *Logical Files* dengan fokus pada identifikasi tipe file berdasarkan **isi (konten)**, bukan berdasarkan ekstensi nama file.

Dari pemeriksaan MIME type, ditemukan satu file yang diklasifikasikan sebagai `text/plain`, yaitu `KONMED 5.png`. File ini berekstensi `.png`, tetapi tipe aktualnya adalah teks dan ukurannya hanya **25 byte**. Pemeriksaan melalui tab *Text* di Autopsy menunjukkan isi:

```text
FLAG{F0r3ns1k_K3l0mp0k_2}
```

Dengan demikian, barang bukti Kelompok 2 dinyatakan **SOLVED**.

| ID Sasaran | Lokasi / File | Teknik Penyembunyian | Status | Flag |
| :---: | :--- | :--- | :---: | :--- |
| **OBJ-01** | `KONMED 5.png` | File Masquerading (teks berekstensi `.png`) | **SOLVED** | `FLAG{F0r3ns1k_K3l0mp0k_2}` |

---

## 2. Lingkungan dan Metode Analisis

| Komponen | Keterangan |
| :--- | :--- |
| Tool | Autopsy 4.23.1 |
| Data source | Logical Files |
| Fitur utama | File Views → File Types → By MIME Type |
| Metode pemeriksaan | MIME type analysis, Text Viewer |
| Status | SOLVED |

**Alur pemeriksaan:**

```text
Data Source → Ingest → Analisis MIME Type → Identifikasi Anomali → Pemeriksaan Konten → Validasi Flag
```

---

## 3. Tahapan Investigasi

### 3.1 Penambahan Data Source

Data `KEBELET PSDM` ditambahkan ke Autopsy sebagai **Logical Files**. Setelah berhasil ditambahkan, Autopsy menampilkan `LogicalFileSet_1 Host` pada bagian *Data Sources*.

<img width="960" height="600" alt="ss - 1 " src="https://github.com/user-attachments/assets/f9ad5ee0-efb2-451f-ae65-2cde0a7078bd" />

*Gambar 1. Data source berhasil ditambahkan ke Autopsy (case `Fordig-Kelompok2`).*

### 3.2 Analisis MIME Type

Hasil identifikasi diperiksa melalui menu:

```text
File Views → File Types → By MIME Type
```

Autopsy mengelompokkan file berdasarkan tipe kontennya:

| Kategori MIME | Subtipe | Jumlah |
| :--- | :--- | :---: |
| `application` | `pdf` | 7 |
| `audio` | `mp4` | 1 |
| `image` | `png` | 75 |
| `image` | `jpeg` | 658 |
| `text` | `plain` | **1** |

Hampir seluruh file berupa gambar. Keberadaan **satu file `text/plain`** menjadi anomali yang diperiksa lebih lanjut. Pada *Deleted Files*, hasil pencarian menunjukkan `File System (0)` dan `All (0)`, sehingga tidak ada file terhapus yang perlu dipulihkan pada barang bukti ini.

<img width="960" height="600" alt="ss - 2" src="https://github.com/user-attachments/assets/c5a50d87-5be0-4d89-8eef-60f870762a82" />

*Gambar 2. Hasil identifikasi MIME type menunjukkan satu file `text/plain`.*

### 3.3 Identifikasi File Mencurigakan dan Flag

Kategori `text → plain (1)` berisi file bernama **`KONMED 5.png`** dengan ukuran **25 byte** dan status *Allocated*. Meskipun nama file berekstensi `.png`, Autopsy mengidentifikasinya sebagai `text/plain`.

Ketika file diperiksa melalui tab **Text** (*Extracted Text*), ditemukan:

```text
FLAG{F0r3ns1k_K3l0mp0k_2}
```

<img width="960" height="600" alt="ss - 3" src="https://github.com/user-attachments/assets/14c3d9cb-7283-4ba2-a843-31d4cd96cbaa" />

*Gambar 3. File `KONMED 5.png` teridentifikasi sebagai `text/plain` dan berisi flag.*

---

## 4. Analisis Teknik Penyembunyian

Teknik yang ditemukan adalah **file masquerading / extension mismatch sederhana**: file teks diberi ekstensi `.png` sehingga sekilas terlihat seperti file gambar di antara ratusan gambar lain. Pemeriksaan berdasarkan MIME type membuktikan bahwa konten aktualnya adalah `text/plain`.

| Atribut | Hasil |
| :--- | :--- |
| Nama file | `KONMED 5.png` |
| Ekstensi | `.png` |
| Tipe aktual | `text/plain` |
| Ukuran | 25 byte |
| Flag | `FLAG{F0r3ns1k_K3l0mp0k_2}` |

---

## 5. Reproduksi Hasil

Temuan dapat direproduksi dengan langkah berikut:

1. Buka case pada Autopsy 4.23.1.
2. Tambahkan data `KEBELET PSDM` sebagai *Logical Files*.
3. Jalankan ingest hingga selesai.
4. Buka `File Views → File Types → By MIME Type`.
5. Pilih `text → plain (1)`.
6. Pilih file `KONMED 5.png`.
7. Buka tab **Text**.
8. Verifikasi bahwa konten yang muncul adalah `FLAG{F0r3ns1k_K3l0mp0k_2}`.

---

## 6. Kesimpulan

Investigasi menggunakan Autopsy berhasil menemukan satu file yang disamarkan sebagai gambar PNG. `KONMED 5.png` berekstensi `.png`, tetapi hasil identifikasi menunjukkan tipe `text/plain`. Pemeriksaan konten menghasilkan flag `FLAG{F0r3ns1k_K3l0mp0k_2}`, sehingga investigasi barang bukti Kelompok 2 dinyatakan **SOLVED**.

---

### Pengesahan

```text
Tim Penyelidik : KELOMPOK 5 (Forensik Digital)
Subjek Bukti   : Data digital KEBELET PSDM, Kelompok 2
Tanggal        : 05 Oktober 2026
Status         : SOLVED
```
