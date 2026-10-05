# LAPORAN FORENSIK DIGITAL

---

| Field | Detail |
|---|---|
| **Nomor Kasus** | FORDIG-2026-KEL1 |
| **Judul Kasus** | Analisis Barang Bukti Digital Kelompok 1 — Investigasi File Terhapus pada Flashdisk |
| **Tanggal Laporan** | 05 Oktober 2026 |
| **Pemeriksa** | Kelompok 5 |
| **Klasifikasi** | TERBATAS |

---

## Daftar Isi

1. [Ringkasan Eksekutif](#1-ringkasan-eksekutif)
2. [Informasi Kasus](#2-informasi-kasus)
3. [Metodologi Pemeriksaan](#3-metodologi-pemeriksaan)
4. [Spesifikasi Barang Bukti](#4-spesifikasi-barang-bukti)
5. [Proses Akuisisi (FTK Imager)](#5-proses-akuisisi-ftk-imager)
6. [Analisis Image dengan Autopsy](#6-analisis-image-dengan-autopsy)
7. [Temuan Pemeriksaan](#7-temuan-pemeriksaan)
8. [Recovery File Terhapus](#8-recovery-file-terhapus)
9. [Rekonstruksi Timeline Kejadian](#9-rekonstruksi-timeline-kejadian)
10. [Kesimpulan & Flag](#10-kesimpulan--flag)
11. [Rekomendasi](#11-rekomendasi)

---

## 1. Ringkasan Eksekutif

Pemeriksaan forensik ini dilakukan terhadap forensic image dari sebuah **flashdisk** milik Kelompok 1 yang diberikan sebagai barang bukti tantangan forensik digital. Image bernama `soal_forensik.img` berukuran **64 MiB** dengan sistem file **FAT32** dan label volume `FORENSIK`.

Analisis menggunakan **Autopsy 4.23.1** terhadap forensic image tersebut mengungkap adanya sebuah file bernama `flag.txt` yang telah **dihapus** dari direktori `Tugas/`. Meskipun entri direktori file tersebut telah ditandai sebagai dihapus (marker `0xE5`), data aktual file masih dapat dipulihkan (*recovered*) karena cluster penyimpanan belum di-overwrite.

Selain itu, ditemukan dua file decoy berupa `IMG_0042.JPG` dan `lagu.mp3` yang secara fisik ada dalam sistem file namun **tidak memiliki magic bytes yang valid** — keduanya berisi data acak yang kemungkinan sengaja ditempatkan untuk mengecoh analis.

> **FLAG DITEMUKAN:**
> ```
> F0RD1G{w0w_congr4t5_d4p3t_fl44444ggg_}
> ```

---

## 2. Informasi Kasus

| Field | Detail |
|---|---|
| **Nomor Kasus** | FORDIG-2026-KEL1 |
| **Tanggal Penerimaan** | 05 Oktober 2026 |
| **Tanggal Pemeriksaan** | 05 Oktober 2026 |
| **Pemilik Barang Bukti** | Kelompok 1 (Forensik Digital) |
| **Tim Pemeriksa** | Kelompok 5 (Forensik Digital) |

### Latar Belakang

Barang bukti berupa forensic image flashdisk diterima dari Kelompok 1 sebagai bagian dari kegiatan tugas mata kuliah Forensik Digital. Image diberikan bersama file verifikasi SHA-256 dalam format PNG. Tugas pemeriksa adalah menganalisis image tersebut dan menemukan flag yang telah disembunyikan oleh pembuat soal.

### Tujuan Pemeriksaan

- Memverifikasi integritas forensic image menggunakan hash kriptografis
- Menganalisis struktur sistem file pada forensic image
- Mengidentifikasi file yang tersembunyi atau telah dihapus
- Menemukan dan memulihkan flag forensik
- Mendokumentasikan seluruh proses pemeriksaan

---

## 3. Metodologi Pemeriksaan

| Prinsip | Keterangan |
|---|---|
| **Integritas Bukti** | Seluruh pemeriksaan dilakukan pada forensic image, bukan pada media original |
| **Chain of Custody** | Seluruh penanganan bukti terdokumentasi secara berurutan |
| **Non-Repudiation** | Hash SHA-256 diverifikasi sebelum pemeriksaan dimulai |
| **Read-Only Analysis** | Image tidak dimodifikasi selama proses pemeriksaan |

### Tahapan Pemeriksaan

| # | Tahap | Keterangan |
|---|---|---|
| 1 | **Verifikasi Hash** | Validasi integritas SHA-256 forensic image |
| 2 | **Akuisisi** | Image diterima dalam format `.img` (raw dd) dari Kelompok 1 |
| 3 | **Identifikasi Partisi** | Analisis struktur partisi menggunakan FTK Imager |
| 4 | **Analisis Sistem File** | Pemeriksaan artefak FAT32 menggunakan Autopsy 4.23.1 |
| 5 | **Investigasi Deleted Files** | Pencarian dan recovery file terhapus via Autopsy |
| 6 | **Rekonstruksi Timeline** | Penyusunan timeline berdasarkan metadata fseventsd |
| 7 | **Pelaporan** | Penyusunan laporan dengan temuan dan kesimpulan |

### Perangkat Lunak yang Digunakan

| Tool | Versi | Fungsi |
|---|---|---|
| FTK Imager | 4.7.1.2 | Verifikasi hash dan analisis awal image |
| Autopsy | 4.23.1 | Analisis image, file recovery, artifact browser |
| `sha256sum` | GNU coreutils | Verifikasi integritas SHA-256 |
| `file` | 5.45 | Identifikasi tipe file |
| `xxd` | - | Hex dump analisis magic bytes |
| `strings` | GNU binutils | Ekstraksi string dari binary/image |

---

## 4. Spesifikasi Barang Bukti

| Field | Detail |
|---|---|
| **Label Bukti** | BB-KEL1-001 |
| **Jenis** | Forensic Image (Raw DD) |
| **Nama File** | `soal_forensik.img` |
| **Ukuran** | 67.108.864 bytes (64 MiB) |
| **Sistem File** | FAT32 |
| **Volume Label** | `FORENSIK` |
| **Kondisi** | Baik, dapat dibaca |

### Hash Forensic Image

| Algoritma | Nilai Hash |
|---|---|
| **MD5** | `8cfe719df3fb9f469cd964e3107eb716` |
| **SHA-256** | `321031cfc0727004562df456618015fcfa9190a16a4d6898408ed8e80d9d4b10` |

### File Pendukung

Bersama dengan `soal_forensik.img`, terdapat file:

```
321031cfc0727004562df456618015fcfa9190a16a4d6898408ed8e80d9d4b10.png
```

File PNG tersebut berisi teks:

```
shasum -a 256 soal_forensik.img
zip soal_forensik.zip soal_forensik.img
321031cfc0727004562df456618015fcfa9190a16a4d6898408ed8e80d9d4b10  soal_forensik.img
```

> Nama file PNG identik dengan nilai SHA-256 dari `soal_forensik.img`, berfungsi sebagai **bukti integritas (checksum certificate)** bahwa image tidak mengalami modifikasi sejak diberikan.

---

## 5. Proses Akuisisi (FTK Imager)

### 5.1 Verifikasi Hash Awal

Sebelum analisis dimulai, dilakukan verifikasi integritas image menggunakan **FTK Imager** dengan fitur *Verify Drive/Image*:

**a) Buka FTK Imager** → pilih menu: `File > Verify Drive/Image`

**b) Pilih file:** `soal_forensik.img`

**c) Hasil Verifikasi:**

| Parameter | Nilai |
|---|---|
| Image File | `soal_forensik.img` |
| MD5 Computed | `8cfe719df3fb9f469cd964e3107eb716` |
| SHA-256 Computed | `321031cfc0727004562df456618015fcfa9190a16a4d6898408ed8e80d9d4b10` |
| Verifikasi | **PASS** ✅ |

> Nilai SHA-256 yang dihitung cocok dengan nama file PNG checksum yang diberikan oleh Kelompok 1 → **integritas image terkonfirmasi**.

### 5.2 Analisis Struktur Image dengan FTK Imager

**a) Buka FTK Imager** → pilih: `File > Add Evidence Item`

**b) Source Type:** `Image File` → pilih `soal_forensik.img`

**c) Hasil identifikasi struktur partisi:**

| Parameter | Nilai |
|---|---|
| Tipe Image | Raw/DD |
| Disk Identifier | `0x00000000` |
| Disklabel Type | MBR (DOS) |
| Total Sektors | 131.072 |
| Ukuran Sektor | 512 bytes |

**d) Tabel Partisi:**

| Partisi | Start Sektor | End Sektor | Jumlah Sektor | Ukuran | Tipe |
|---|---|---|---|---|---|
| Partition 1 | 63 | 131.039 | 130.977 | ~64 MiB | `0x0B` — W95 FAT32 |

**e) Detail FAT32 Boot Parameter Block (BPB):**

| Parameter | Nilai |
|---|---|
| Volume Label | `FORENSIK` |
| Bytes per Sektor | 512 |
| Sektor per Cluster | 1 |
| Reserved Sectors | 32 |
| Number of FATs | 2 |
| FAT Size | 1.008 sektor |
| Root Cluster | 2 |
| Data Region | mulai sektor 2.048 (relatif ke partisi) |

### 5.3 Struktur Direktori Root (FTK Imager View)

Dari tampilan *Evidence Tree* FTK Imager, direktori root volume `FORENSIK` memiliki isi:

```
[FORENSIK] (FAT32 Volume)
├── .fseventsd/               [attr: Hidden + System]
├── .Spotlight-V100/          [attr: Hidden + System]
├── Dokumen/
├── Foto/
├── Musik/
└── Tugas/
```

> Keberadaan direktori `.fseventsd` dan `.Spotlight-V100` mengindikasikan image dibuat dari sistem operasi **macOS** — kedua direktori ini merupakan artefak bawaan macOS untuk event logging dan Spotlight indexing.

---

## 6. Analisis Image dengan Autopsy

### 6.1 Pembuatan Case Baru

Analisis lanjutan dilakukan menggunakan **Autopsy 4.23.1**.

**Langkah pembuatan case:**

1. Buka Autopsy → klik **New Case**
2. Isi detail case:

| Field | Nilai |
|---|---|
| Case Name | `Fordig-Kelompok1` |
| Case Number | `FORDIG-2026-KEL1` |
| Examiner | `Kelompok 5` |

3. Klik **Finish**

### 6.2 Penambahan Data Source

1. Pilih **Add Data Source** → pilih tipe: **Disk Image or VM File**
2. Browse ke `soal_forensik.img`
3. Aktifkan semua Ingest Modules default (termasuk *File Type Identification*, *Hash Lookup*, *Recent Activity*)
4. Klik **Finish** → tunggu proses ingest selesai

### 6.3 Hasil Identifikasi MIME Type

Setelah ingest selesai, dilakukan review melalui:

```
File Views → File Types → By MIME Type
```

| Kategori MIME | Subtipe | Jumlah |
|---|---|---|
| `application` | `octet-stream` | 2 |
| `text` | `plain` | 3 |

> **Anomali:** File `IMG_0042.JPG` dan `lagu.mp3` tidak terdeteksi sebagai `image/jpeg` maupun `audio/mpeg`. Autopsy mengidentifikasi keduanya sebagai `application/octet-stream` — menunjukkan bahwa **konten kedua file tidak sesuai dengan ekstensinya** (bukan file JPEG/MP3 yang valid).

### 6.4 Analisis Deleted Files

Pemeriksaan diarahkan ke menu:

```
Views → Deleted Files
```

File `flag.txt` muncul dengan status *Deleted* di bawah direktori `Tugas/` dan dapat diperiksa kontennya melalui tab **Text** di panel bawah Autopsy.

---

## 7. Temuan Pemeriksaan

### 7.1 Struktur Volume Lengkap

| Path | Status | Ukuran | Keterangan |
|---|---|---|---|
| `/.fseventsd/` | Allocated | — | macOS FSEvents log directory |
| `/.fseventsd/fseventsd-uuid` | Allocated | 36 bytes | UUID volume |
| `/.fseventsd/0000000169d3fbcb` | Allocated | 378 bytes | Event log #1 (gzip) |
| `/.fseventsd/0000000169d3fbcc` | Allocated | 73 bytes | Event log #2 (gzip) |
| `/.Spotlight-V100/VolumeConfiguration.plist` | Allocated | 4.564 bytes | Konfigurasi Spotlight |
| `/Dokumen/catatan1.txt` | Allocated | 24 bytes | "Catatan kuliah minggu 1" |
| `/Dokumen/belanja.txt` | Allocated | 35 bytes | "Daftar belanja: beras, telur, kopi" |
| `/Foto/IMG_0042.JPG` | Allocated | 500.000 bytes | **Decoy** — bukan JPEG valid |
| `/Musik/lagu.mp3` | Allocated | 1.000.000 bytes | **Decoy** — bukan MP3 valid |
| `/Tugas/draft.txt` | Allocated | 24 bytes | "Draft laporan praktikum" |
| `/Tugas/flag.txt` | **Deleted** | 39 bytes | **FILE TARGET — berisi FLAG** |

### 7.2 Analisis File Decoy

#### [A] `IMG_0042.JPG`

File berekstensi `.JPG` berukuran 500.000 bytes. Pemeriksaan magic bytes:

```
Offset 0x00:  44 46 FC 6E  →  TIDAK VALID
              (JPEG seharusnya: FF D8 FF)
```

Autopsy mengklasifikasikan file ini sebagai `application/octet-stream`. File berisi **data acak** tanpa format yang dapat diidentifikasi.

#### [B] `lagu.mp3`

File berekstensi `.mp3` berukuran 1.000.000 bytes. Pemeriksaan magic bytes:

```
Offset 0x00:  C8 16 95 17  →  TIDAK VALID
              (MP3 seharusnya: ID3 atau FF FB)
```

File berisi **data acak** tanpa format yang valid.

> Kedua file decoy sengaja ditempatkan untuk membuat flashdisk terlihat seperti milik pengguna biasa. Namun pemeriksaan MIME type via Autopsy langsung mengungkap anomali.

### 7.3 File Terhapus — `flag.txt`

File `flag.txt` ditemukan dalam kondisi terhapus (*deleted*) di direktori `/Tugas/`.

**Mekanisme penghapusan FAT32:**

| Komponen | Kondisi Normal | Kondisi Setelah Delete |
|---|---|---|
| Directory Entry byte[0] | `0x46` (`F`) | `0xE5` (marker deleted) |
| FAT Chain (cluster 3036) | `0x0FFFFFFF` (end of chain) | `0x00000000` (free) |
| Data di cluster 3036 | Berisi data file | **Masih berisi data file** ← tidak dihapus |

Melalui tab **Text** di Autopsy, isi file berhasil dibaca:

```
F0RD1G{w0w_congr4t5_d4p3t_fl44444ggg_}
```

---

## 8. Recovery File Terhapus

### 8.1 Prosedur Recovery via Autopsy

**Metode 1 — via Deleted Files View:**
1. Navigasi ke `Views → Deleted Files`
2. Cari file `flag.txt`
3. Pilih file → tab **Text** → isi file langsung terbaca
4. Atau klik kanan → **Extract File(s)** untuk menyimpan file

**Metode 2 — via Directory Tree:**
1. Navigasi ke `Data Sources → soal_forensik.img → vol2 → Tugas`
2. File `flag.txt` terlihat dengan ikon merah (deleted)
3. Pilih file → tab **Text**

### 8.2 Verifikasi Data Recovery

| Parameter | Nilai |
|---|---|
| Nama File | `flag.txt` |
| Status | Deleted (recoverable) |
| Ukuran | 39 bytes |
| Cluster | 3036 |
| FAT Entry | `0x00000000` (free — belum realokasi) |
| Kondisi Data | **Utuh** (tidak corrupt) |
| Konten | `F0RD1G{w0w_congr4t5_d4p3t_fl44444ggg_}` |

### 8.3 Mengapa File Masih Bisa Dipulihkan?

Pada FAT32, proses *delete* hanya:
1. Mengubah byte pertama directory entry menjadi `0xE5`
2. Membebaskan entri FAT (menset ke `0x00`)

**Data aktual di cluster TIDAK dihapus.** Selama cluster belum digunakan ulang, data dapat dipulihkan menggunakan Autopsy, TestDisk, atau Recuva.

---

## 9. Rekonstruksi Timeline Kejadian

### 9.1 Sumber Timeline

Timeline direkonstruksi dari dua sumber:
1. **`.fseventsd` logs** — macOS Filesystem Events log (gzip compressed)
2. **`.Spotlight-V100/VolumeConfiguration.plist`** — metadata Spotlight index

### 9.2 Dekompres FSEvents Log

File `.fseventsd/0000000169d3fbcb` (378 bytes, gzip) berhasil didekompresi menghasilkan log aktivitas filesystem. Urutan events yang terekam:

```
[CREATE] ._Dokumen, ._Foto, ._Musik, ._Tugas
[CREATE] Dokumen/._belanja.txt, Dokumen/._catatan1.txt
[WRITE]  Dokumen/belanja.txt   → "Daftar belanja: beras, telur, kopi"
[WRITE]  Dokumen/catatan1.txt  → "Catatan kuliah minggu 1"
[CREATE] Foto/._IMG_0042.jpg
[WRITE]  Foto/IMG_0042.jpg     → (data acak 500.000 bytes)
[CREATE] Musik/._lagu.mp3
[WRITE]  Musik/lagu.mp3        → (data acak 1.000.000 bytes)
[CREATE] Tugas/._draft.txt, Tugas/._flag.txt
[WRITE]  Tugas/draft.txt       → "Draft laporan praktikum"
[WRITE]  Tugas/flag.txt        → "F0RD1G{w0w_congr4t5_d4p3t_fl44444ggg_}"
[DELETE] Tugas/flag.txt        ← DIHAPUS
```

### 9.3 Rekonstruksi Timeline

> Timestamp dalam **UTC+0** (sumber: `VolumeConfiguration.plist`)

| Waktu (UTC) | Kejadian |
|---|---|
| 2026-10-05 01:08:26 | Volume di-mount di macOS → Spotlight mulai indexing |
| 2026-10-05 01:08:xx | Folder `Dokumen`, `Foto`, `Musik`, `Tugas` dibuat |
| 2026-10-05 01:08:xx | File konten disalin: catatan, belanja, draft, foto decoy, musik decoy |
| 2026-10-05 01:08:xx | `flag.txt` dibuat dan ditulis ke folder `Tugas` |
| 2026-10-05 01:09:xx | `flag.txt` **dihapus** dari filesystem (data di cluster 3036 tetap ada) |
| 2026-10-05 01:09:31 | Spotlight index diperbarui (update terakhir tercatat) |

**Kesimpulan timeline:**
- Flashdisk disiapkan dalam waktu singkat (±1 menit)
- `flag.txt` sengaja dibuat lalu dihapus sebagai teknik penyembunyian
- File decoy ditempatkan sebelum penghapusan flag untuk mengalihkan perhatian

---

## 10. Kesimpulan & Flag

### 10.1 Kesimpulan Pemeriksaan

**a)** Barang bukti berupa forensic image FAT32 (`soal_forensik.img`) terverifikasi integritasnya — SHA-256 cocok dengan nama file checksum PNG yang disertakan.

**b)** Image dibuat dari sistem **macOS**, dibuktikan dengan keberadaan `.fseventsd` dan `.Spotlight-V100` yang merupakan artefak sistem macOS.

**c)** Teknik penyembunyian yang digunakan adalah **penghapusan file** (`flag.txt`) dari FAT32. File di-mark deleted namun data di cluster 3036 masih intact karena belum di-overwrite.

**d)** Dua file decoy (`IMG_0042.JPG` dan `lagu.mp3`) ditempatkan dengan data acak — bukan file valid — sebagai pengalih perhatian analis.

**e)** Flag berhasil dipulihkan menggunakan fitur **Deleted File Recovery** pada Autopsy 4.23.1.

---

### 10.2 Flag Forensik

Flag ditemukan melalui recovery file terhapus (`flag.txt`) pada direktori `/Tugas/`:

```
Views → Deleted Files → flag.txt → Tab Text
```

---

> ## FLAG
>
> ```
> F0RD1G{w0w_congr4t5_d4p3t_fl44444ggg_}
> ```

---

## 11. Rekomendasi

### [1] Secure File Deletion

- Gunakan **secure delete** (multi-pass overwrite) saat menghapus file sensitif
- Tools: `shred` (Linux), `sdelete` (Windows), `srm` (macOS)
- Pertimbangkan enkripsi volume (BitLocker, VeraCrypt) sebagai lapisan tambahan

### [2] Anti-Forensic Awareness

- Untuk keperluan CTF/challenge, gunakan **full disk wipe** sebelum penempatan file
- Hapus metadata macOS (`.fseventsd`, `.Spotlight-V100`, `._*` AppleDouble files) agar tidak memberikan clue timeline yang tidak diinginkan

### [3] Forensic Best Practices

- Selalu verifikasi hash sebelum dan sesudah akuisisi
- Gunakan write-blocker hardware saat mengakses media original
- Dokumentasikan seluruh chain of custody

---

*Laporan ini disusun berdasarkan pemeriksaan forensik yang dilaksanakan sesuai dengan standar dan prosedur yang berlaku. Seluruh temuan terdokumentasi dan dapat direproduksi.*

---

| Pengesahan | |
|---|---|
| **Tim Pemeriksa** | Kelompok 5 (Forensik Digital) |
| **Subjek Bukti** | Forensic Image Flashdisk Kelompok 1 (`soal_forensik.img`) |
| **Tanggal** | 05 Oktober 2026 |
| **Status** | **SOLVED** ✅ |

---

*— AKHIR LAPORAN —*
