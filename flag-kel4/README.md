# LAPORAN INVESTIGASI FORENSIK DIGITAL
## Walkthrough Analisis Barang Bukti Flashdisk Kelompok 4
### Disusun oleh: **Kelompok 5** | Mata Kuliah: Forensik Digital

---

## Informasi Kasus

| Parameter | Keterangan |
| :--- | :--- |
| **Judul Kasus** | Investigasi Steganografi & Pemulihan File Terhapus pada Flashdisk Kelompok 4 |
| **Tim Penyelidik (Examiner)** | **Kelompok 5** (Forensik Digital) |
| **Pemilik Barang Bukti** | **Kelompok 4** |
| **Subjek Barang Bukti** | Flashdisk (folder `tugas` dan `rhythm game`) |
| **Tools yang Digunakan** | OpenStego (ekstraksi steganografi), Autopsy (recovery file terhapus) |
| **Jumlah Sasaran (Flag)** | 2 |
| **Status Akhir** | **SOLVED** (2 dari 2 flag berhasil ditemukan) |

### Ringkasan Hasil

| Flag | Metode | Lokasi / Evidence | Hasil |
| :---: | :--- | :--- | :--- |
| **Flag 1** | Steganografi (OpenStego) | `rhythm game/bg/data/bt/` (file PNG `250926`) | `FLAG{k4t4H1kar1_0K3}` |
| **Flag 2** | File Recovery (Autopsy) | File terhapus `cobainAES128` pada folder `tugas` | `FLAG{F1L3nY4DiH4pu5}` |

---

## Daftar Isi

1. [Identifikasi Barang Bukti](#1-identifikasi-barang-bukti)
2. [Investigasi Flag Pertama: Steganografi](#2-investigasi-flag-pertama--steganografi)
   - [2.1 Menelusuri Folder Barang Bukti](#21-menelusuri-folder-barang-bukti)
   - [2.2 Ekstraksi Menggunakan OpenStego](#22-ekstraksi-menggunakan-openstego)
   - [2.3 Hasil Flag 1](#23-hasil-flag-1)
3. [Investigasi Flag Kedua: File yang Dihapus](#3-investigasi-flag-kedua--file-yang-dihapus)
   - [3.1 Membuka Autopsy](#31-membuka-autopsy)
   - [3.2 Mencari File yang Telah Dihapus](#32-mencari-file-yang-telah-dihapus)
   - [3.3 Melakukan Recovery](#33-melakukan-recovery)
4. [Hasil Investigasi](#4-hasil-investigasi)
5. [Kesimpulan](#5-kesimpulan)

---

## 1. Identifikasi Barang Bukti

Barang bukti yang diperiksa berasal dari sebuah flashdisk milik Kelompok 4 dengan dua folder utama:

```text
Flashdisk
├── tugas
└── rhythm game
```

![Exhibit 1: Struktur flashdisk](./screenshots/ss1_struktur_flashdisk.png)
*Gambar 1: Struktur folder pada flashdisk barang bukti.*

- Folder **`tugas`** berisi beberapa file teks yang menjadi salah satu sumber informasi dalam proses investigasi.
- Folder **`rhythm game`** berisi data yang berkaitan dengan aplikasi/game, termasuk folder `bg` yang menjadi lokasi ditemukannya salah satu barang bukti.

### Tools yang Digunakan

| Tools | Fungsi |
| :--- | :--- |
| **Steganography / OpenStego** | Mengekstrak data tersembunyi dari file gambar |
| **Autopsy** | Melakukan recovery terhadap file yang telah dihapus |

---

## 2. Investigasi Flag Pertama: Steganografi

### 2.1 Menelusuri Folder Barang Bukti

Investigasi pertama dilakukan dengan menelusuri folder `rhythm game`, kemudian masuk ke `rhythm game/bg/data/bt`. Di dalam folder tersebut ditemukan sebuah file PNG dengan penamaan yang berkaitan dengan tanggal **`250926`**. File ini diduga sebagai *stego file*, yaitu file yang kemungkinan mengandung data tersembunyi.

Struktur lokasi file:

```text
rhythm game
└── bg
    └── data
        └── bt
            └── [file PNG 250926]
```

### 2.2 Ekstraksi Menggunakan OpenStego

File PNG tersebut diperiksa menggunakan aplikasi steganografi OpenStego dengan langkah:

1. Memilih opsi **Extract Data**.
2. Memasukkan file PNG `250926` sebagai **Input Stego File**.
3. Mengarahkan output ke folder hasil ekstraksi yang telah disiapkan.

![Exhibit 2: Proses ekstraksi OpenStego](./screenshots/ss2_openstego_extract.png)
*Gambar 2: Proses Extract Data pada OpenStego.*

Setelah proses ekstraksi selesai, ditemukan data tersembunyi yang berisi flag.

**Alur proses:**

```text
File PNG 250926
      ↓
Steganography / OpenStego
      ↓
Extract Data
      ↓
Hidden File
      ↓
FLAG
```

### 2.3 Hasil Flag 1

![Exhibit 3: Hasil Flag 1](./screenshots/ss3_hasil_flag1.png)
*Gambar 3: Hasil ekstraksi steganografi berupa Flag 1.*

```text
FLAG{k4t4H1kar1_0K3}
```

Dengan demikian, Flag 1 berhasil ditemukan melalui proses ekstraksi steganografi.

---

## 3. Investigasi Flag Kedua: File yang Dihapus

Flag kedua memiliki metode yang berbeda: file yang mengandung flag telah **dihapus** dari media penyimpanan.

![Exhibit 4: Folder tugas](./screenshots/ss4_file_terhapus_tugas.png)
*Gambar 4: Folder `tugas` pada flashdisk, tempat file `cobainAES128` sebelumnya berada.*

Nama file yang telah dihapus dari folder `tugas` adalah **`cobainAES128`**. Karena file tersebut sudah tidak terlihat pada filesystem normal, pemeriksaan dilakukan menggunakan **Autopsy** untuk mencari dan melakukan recovery terhadap *deleted file*.

### 3.1 Membuka Autopsy

Autopsy digunakan sebagai *forensic analysis tool* untuk memeriksa barang bukti. Langkah awal:

1. Membuka Autopsy.
2. Membuat **New Case**.
3. Mengisi nama case sesuai kebutuhan.
4. Menambahkan barang bukti sebagai **Data Source**.

Setelah evidence berhasil ditambahkan, Autopsy melakukan proses *ingest* terhadap data sehingga filesystem dan artefak yang terdapat pada evidence dapat dianalisis.

### 3.2 Mencari File yang Telah Dihapus

Setelah proses analisis selesai, dilakukan pencarian file dengan nama `cobainAES128` menggunakan fitur pencarian file pada Autopsy dan pemeriksaan pada bagian **Deleted Files**. File tersebut ditemukan sebagai *deleted file*, sehingga dapat disimpulkan bahwa file pernah berada pada media penyimpanan tetapi telah dihapus.

![Exhibit 5: Deleted Files pada Autopsy](./screenshots/ss5_autopsy_deleted_files.png)
*Gambar 5: File `cobainAES128` terdeteksi sebagai deleted file di Autopsy.*

### 3.3 Melakukan Recovery

File `cobainAES128` dipilih, kemudian dilakukan proses **Recover/Extract** menggunakan fitur recovery pada Autopsy.

**Alur proses:**

```text
Evidence
   ↓
Autopsy
   ↓
Deleted Files
   ↓
cobainAES128
   ↓
Recover File
   ↓
Buka hasil recovery
   ↓
Temukan FLAG
```

Setelah file berhasil direcover, isi file diperiksa untuk menemukan flag kedua.

![Exhibit 6: Hasil Flag 2](./screenshots/ss6_hasil_flag2.png)
*Gambar 6: Isi file hasil recovery berupa Flag 2.*

```text
FLAG{F1L3nY4DiH4pu5}
```

Dengan demikian, Flag 2 berhasil ditemukan melalui proses recovery Autopsy.

---

## 4. Hasil Investigasi

Berdasarkan investigasi yang dilakukan, ditemukan dua flag dengan metode yang berbeda.

| Flag | Metode | Lokasi / Evidence | Hasil |
| :---: | :--- | :--- | :--- |
| **Flag 1** | Steganografi | `rhythm game/bg/data/bt/` (PNG `250926`) | `FLAG{k4t4H1kar1_0K3}` |
| **Flag 2** | File Recovery | Deleted file `cobainAES128` | `FLAG{F1L3nY4DiH4pu5}` |

- Flag pertama ditemukan dengan mengekstrak data tersembunyi dari file PNG menggunakan **Steganography/OpenStego**.
- Flag kedua ditemukan dengan melakukan recovery terhadap file `cobainAES128` yang telah dihapus menggunakan **Autopsy**.

---

## 5. Kesimpulan

Berdasarkan proses investigasi digital terhadap barang bukti flashdisk, ditemukan **dua mekanisme penyembunyian evidence**:

1. **Steganografi.** Sebuah file PNG pada `rhythm game/bg/data/bt/` digunakan sebagai media untuk menyembunyikan informasi. Melalui teknik steganografi dan proses ekstraksi, ditemukan `FLAG{k4t4H1kar1_0K3}`.
2. **Penghapusan file.** File bernama `cobainAES128` telah dihapus dari media penyimpanan dan tidak ditemukan melalui pemeriksaan filesystem biasa. File tersebut berhasil ditemukan kembali melalui analisis *Deleted Files* pada Autopsy, lalu dilakukan recovery sehingga diperoleh `FLAG{F1L3nY4DiH4pu5}`.

Dengan demikian, kedua flag dapat diperoleh melalui dua teknik forensik yang berbeda, yaitu **steganography extraction** dan **deleted file recovery**.

---

## Pengesahan

```text
Tim Penyelidik : KELOMPOK 5 (Forensik Digital)
Subjek Bukti   : Flashdisk Kelompok 4
Status         : SOLVED (2/2 flag ditemukan)
```
