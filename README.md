# Forensik Digital Flag Hunting
## **Dokumentasi investigasi barang bukti digital oleh Kelompok 5**

Repository ini berisi dokumentasi proses investigasi forensik digital yang dilakukan oleh **Kelompok 5** terhadap barang bukti dari empat kelompok target (Kelompok 1 sampai Kelompok 4) pada mata kuliah Forensik Digital.

Setiap barang bukti memuat satu atau lebih *flag* yang disembunyikan dengan teknik berbeda. Tugas kami adalah mengidentifikasi artefak, menemukan flag, dan mendokumentasikan seluruh langkah pemeriksaan sehingga hasilnya dapat direproduksi.

---

## Tim Penyelidik

**Kelompok 5**

| No. | Nama Lengkap | NRP |
| :-: | :--- | :---: |
| 1 | Aras Rizky Ananta | 5027221053 |
| 2 | Muhammad Khosyi Syehab | 5027241089 |
| 3 | Clarissa Aydin Rahmazea | 5027241014 |
| 4 | I Dewa Made Satya Raditya | 5027231051 |

---

## Status Investigasi

**4 dari 4 target berhasil diselesaikan.**

| Target | Jumlah Flag | Teknik yang Ditemukan | Status | Dokumentasi |
| :--- | :---: | :--- | :---: | :---: |
| Kelompok 1 | 1 | Penghapusan file pada image FAT32 | Solved | [Buka](./flag-kel1) |
| Kelompok 2 | 1 | File masquerading (teks berekstensi `.png`) | Solved | [Buka](./flag-kel2) |
| Kelompok 3 | 1 | Static analysis malware BadUSB | Solved | [Buka](./flag-kel3) |
| Kelompok 4 | 2 | Steganografi dan recovery file terhapus | Solved | [Buka](./flag-kel4) |

---

## Ringkasan Kasus

### Kelompok 1: File Terhapus pada Forensic Image

Barang bukti berupa forensic image flashdisk (`soal_forensik.img`, FAT32, 64 MiB). Integritas image diverifikasi dengan hash SHA-256 sebelum analisis. Melalui Autopsy ditemukan `flag.txt` yang telah dihapus dari direktori `Tugas/`, namun datanya masih utuh pada cluster sehingga dapat dipulihkan. Dua file umpan (`IMG_0042.JPG` dan `lagu.mp3`) juga ditemukan dengan isi data acak.

### Kelompok 2: File Masquerading

Data digital `KEBELET PSDM` dianalisis sebagai *Logical Files* di Autopsy. Pemeriksaan MIME type menunjukkan satu file `KONMED 5.png` yang ternyata bertipe `text/plain` dan berukuran hanya 25 byte. Flag ditemukan dari isi teks file tersebut.

### Kelompok 3: BadUSB dan Malware Nim

Barang bukti berupa image USB SanDisk (format E01) yang diduga digunakan dalam serangan BadUSB. Analisis artefak dan *static analysis* terhadap binary `combined_v2_gui.exe` (dikompilasi dengan Nim) menggunakan `strings` berhasil mengungkap flag yang tertanam di dalam kode. Laporan ini juga memuat rekonstruksi timeline dan indikator kompromi (IOC).

### Kelompok 4: Steganografi dan File Terhapus

Barang bukti berupa flashdisk dengan dua flag. Flag pertama disembunyikan di dalam file PNG dan diekstrak dengan OpenStego. Flag kedua berada pada file `cobainAES128` yang telah dihapus, dan dipulihkan menggunakan Autopsy.

---

## Tools

| Tool | Fungsi |
| :--- | :--- |
| **Autopsy 4.23.1** | Analisis image, MIME type, deleted files, dan recovery |
| **FTK Imager** | Akuisisi dan verifikasi hash forensic image |
| **OpenStego** | Ekstraksi data tersembunyi (steganografi) |
| **strings** | Ekstraksi string dari file binary |
| **xxd** | Hex dump dan pemeriksaan magic bytes |
| **file**, **sha256sum / md5sum** | Identifikasi tipe file dan verifikasi integritas |

Tool tambahan, bila ada, dicantumkan pada dokumentasi masing-masing investigasi.

---

## Struktur Repository

```text
forensik-digital-flag/
│
├── README.md                       # Ringkasan umum (dokumen ini)
├── quest.zip                       # Berkas soal
├── build_up_walkthrough_quest.md   # Walkthrough pembuatan soal
├── solver_quest.md                 # Solver soal
│
├── flag-kel1/                      # Investigasi barang bukti Kelompok 1
│   ├── kel1-report.md              # Laporan investigasi
│   ├── flag.txt                    # Flag hasil recovery
│   └── duplicated_evidence/        # Salinan barang bukti
│
├── flag-kel2/                      # Investigasi barang bukti Kelompok 2
│   ├── report-kel2.md              # Laporan investigasi
│   ├── Autopsy-Kelompok2/          # Case Autopsy
│   ├── Screenshots/                # Screenshot tahapan analisis
│   └── recovered_files/            # File hasil recovery
│
├── flag-kel3/                      # Investigasi barang bukti Kelompok 3
│   ├── kel3-report.md              # Laporan investigasi
│   ├── flag.txt                    # Flag hasil analisis
│   ├── screenshots/                # Screenshot tahapan analisis
│   └── duplicated_evidence/        # Salinan barang bukti
│
└── flag-kel4/                      # Investigasi barang bukti Kelompok 4
    ├── report-kel4.md              # Laporan investigasi
    ├── Autopsy-Kelompok4/          # Case Autopsy
    ├── Screenshots/                # Screenshot tahapan analisis
    └── recovered_files/            # File hasil recovery
```

Setiap folder berisi laporan investigasi beserta screenshot pendukung, sehingga proses dan hasil pemeriksaan tiap target dapat ditelusuri secara terpisah.

---

## Alur Investigasi

Secara umum, setiap barang bukti diperiksa dengan alur berikut:

```text
Penerimaan Bukti → Verifikasi Integritas → Analisis (Autopsy / tools CLI)
      → Identifikasi Anomali → Temuan Flag → Dokumentasi → Kesimpulan
```

Prinsip yang dijaga selama pemeriksaan:

- Analisis tidak memodifikasi barang bukti asli.
- Integritas diverifikasi dengan hash bila tersedia (misalnya pada Kelompok 1 dan Kelompok 3).
- Setiap langkah didokumentasikan dengan screenshot agar dapat direproduksi.

---

## Dokumentasi

| Target | Laporan |
| :--- | :--- |
| Kelompok 1 | [flag-kel1](./flag-kel1) |
| Kelompok 2 | [flag-kel2](./flag-kel2) |
| Kelompok 3 | [flag-kel3](./flag-kel3) |
| Kelompok 4 | [flag-kel4](./flag-kel4) |

---

<sub>Repository ini dibuat untuk keperluan akademik pada mata kuliah Forensik Digital, Kelompok 5.</sub>
