# DIGITAL FORENSICS INVESTIGATION REPORT — OFFICIAL SOLUTION & CHALLENGE WALKTHROUGH
## Analysis of Deleted File Artifacts & Disk Image Trailer Steganography
### Forensic Challenge Case: Group 5 (Digital Forensics 2026)

---

| Parameter | Document Details |
|---|---|
| **Case Number** | FORDIG-2026-GRP5 |
| **Case Title** | Quest Flag Recovery — Operation Apostle Peter |
| **Challenge Author** | Group 5 (Digital Forensics) |
| **Examiner / Lead Author** | K |
| **Date of Report** | October 05, 2026 |
| **Classification** | RESTRICTED / OFFICIAL SOLUTION & WALKTHROUGH |
| **Verification Status** | SOLVED / VALIDATED (100% SUCCESS) |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Case Information & Challenge Narrative](#2-case-information--challenge-narrative)
3. [Evidence Specification & Chain of Custody](#3-evidence-specification--chain-of-custody)
4. [Forensic Examination Methodology](#4-forensic-examination-methodology)
5. [Phase 1: Bit-Stream Forensic Acquisition (FTK Imager)](#5-phase-1-bit-stream-forensic-acquisition-ftk-imager)
6. [Phase 2: Forensic Analysis & Deleted File Recovery (Autopsy)](#6-phase-2-forensic-analysis--deleted-file-recovery-autopsy)
7. [Phase 3: Digital Artifact & Steganography Analysis (quest.zip / opung_archive.jpg)](#7-phase-3-digital-artifact--steganography-analysis-questzip--opung_archivejpg)
8. [Theoretical Principles & Technical Mechanisms](#8-theoretical-principles--technical-mechanisms)
9. [Evidence Reconstruction & Hexadecimal Verification](#9-evidence-reconstruction--hexadecimal-verification)
10. [Official Flag Consolidation](#10-official-flag-consolidation)
11. [Remediation & Secure Data Sanitization](#11-remediation--secure-data-sanitization)
12. [Appendix: Forensic CLI Quick Cheatsheet](#12-appendix-forensic-cli-quick-cheatsheet)

---

## 1. Executive Summary

This document serves as the official solution guide and comprehensive technical walkthrough (*Official Solution & Walkthrough*) for the digital forensics challenge conceptualized and implemented by **Group 5**. The challenge was engineered to rigorously evaluate an investigator's competence in evidence handling, physical bit-stream acquisition, cryptographic hash verification, deleted file recovery (*unallocated space carving*), and binary format anomaly detection (*End-of-File / EOF trailer steganography*).

The challenge is delivered across two forensic investigation modalities:
1. **Physical Triage Modality (Hardware Challenge):** A physical USB flash drive formatted with the FAT32 file system, where the target operational file was deleted from the root directory table to simulate an anti-forensics attempt by the suspect.
2. **Digital Package Modality (Software Archive):** A digital archive file `quest.zip` containing `flag-1/opung_archive.jpg`, which conceals secret payload data appended outside the standard JPEG End-of-Image (*EOI*) delimiter.

Employing rigorous chain-of-custody protocols aligned with **ISO/IEC 27037** and **NIST SP 800-86** standards, using industry-standard suites (**AccessData FTK Imager** and **The Sleuth Kit / Autopsy Digital Forensics Platform**), the hidden artifacts were completely recovered without compromising media integrity.

> ### OFFICIAL FLAG CONSOLIDATION
> ```text
> CODENAME{4P0ST3L_P3T3R_0F_GL0RY}
> ```
> *(Standard alphanumeric normalization variant: `CODENAME{4P0ST3L_P3T3R_OF_GLORY}`)*

---

## 2. Case Information & Challenge Narrative

### 2.1 Background Story
A portable storage device belonging to an intelligence operative using the moniker **"Apostle Peter"** was seized by the forensic response team. Prior to seizure, the operative attempted an anti-forensics wipe to eliminate traces of active mission parameters.

The forensics team was tasked with:
1. Securing the physical device without contaminating file system metadata (*Strict Write-Blocking*).
2. Generating a bit-by-bit physical image in an industry-standard compressed forensic container (**E01 - Expert Witness Format**).
3. Analyzing unallocated disk sectors and recovering the deleted mission file.
4. Auditing auxiliary digital image files for structural binary anomalies.

### 2.2 Learning Objectives & Core Competencies
The challenge developed by Group 5 evaluates four core forensic disciplines:
- **Locard's Exchange Principle & Anti-Tampering:** Preventing automated operating system writes (suppressing *Windows Volume Mount* and *Scan & Fix* prompts).
- **Physical Bit-Stream Acquisition & Integrity Verification:** Creating forensically sound disk images with mandatory SHA-1 and MD5 validation.
- **FAT32 Internal Allocation & Directory Entry Carving:** Understanding how FAT32 marks directory entries with `0xE5` upon deletion while leaving cluster chains intact in unallocated space.
- **File Format Specification & Trailing Data Analysis:** Detecting and carving payloads appended beyond the JPEG `FF D9` (End of Image) delimiter.

---

## 3. Evidence Specification & Chain of Custody

### 3.1 Physical Evidence Profile (USB Flash Drive)
| Parameter | Technical Details |
|---|---|
| **Device Classification** | USB Mass Storage Device (Flash Drive) |
| **File System** | Win95 FAT32 (Partition Type ID: `0x0C`) |
| **Partition Sector Range** | Sector `2048` to Sector `60088319` |
| **Volume Label** | `vol2` |
| **Target Deleted File** | `hidden_mission.txt` |
| **Artifact Status** | Deleted / Unallocated Directory Record |

### 3.2 Digital Evidence Profile (quest.zip & Auxiliary Files)
| File Name | File Size (Bytes) | MD5 Checksum | SHA-1 Checksum | SHA-256 Checksum |
|---|---|---|---|---|
| **`quest.zip`** | 95,853 | `613c74d512726a517e500078d96cb293` | `31bb7c600f18cb3189e49fea6d7fa194348699cd` | `9851892522ee4ec66529297fa67b21dde04fd5d4180b547db85bc5011bb3cbcc` |
| **`flag-1/opung_archive.jpg`** | 95,779 | `1e5454973664bc15e5d6bd6cd584b53c` | `2d4212b117b2e852e9e720a8bd788d4591cef06d` | `29d1d15e5cd125499cb1458c1e91a2c945fc133a1a99f851544ad9966a32924a` |

---

## 4. Forensic Examination Methodology

The investigation strictly adhered to the four phases defined by **NIST SP 800-86**:

```
+-----------------------------------------------------------------------------------+
|                        GROUP 5 FORENSIC WORKFLOW DIAGRAM                          |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  [ 1. IDENTIFICATION & ACQUISITION ]                                              |
|        |--> Connect Physical USB Drive (Apply Anti-Tampering Protocols)           |
|        |--> Launch FTK Imager with Elevated Administrator Privileges             |
|        |--> Select "Physical Drive" Source (All Raw Sectors)                      |
|        |--> Export to E01 Container (flashdisk_evidence_01.E01)                   |
|        +--> Automatic Hash Verification (MD5 & SHA1 Verified Matching)            |
|                                                                                   |
|  [ 2. EXAMINATION & ARTIFACT INGESTION ]                                          |
|        |--> Create New Investigation Case in Autopsy                              |
|        |--> Add Data Source: Disk Image (.E01)                                    |
|        +--> Run Core Ingest Modules                                               |
|                                                                                   |
|  [ 3. DELETED FILE CARVING & RECOVERY ]                                           |
|        |--> Navigate Directory Tree: vol2 (Win95 FAT32: 2048-60088319)            |
|        |--> Filter Deleted Artifacts (Red "X" Icon / Unallocated Status)          |
|        |--> Identify Deleted File: hidden_mission.txt                             |
|        +--> Inspect Payload via Content Viewer (Text Tab > Strings View)          |
|                                                                                   |
|  [ 4. ALTERNATIVE DIGITAL TRIAGE (EOF STEGANOGRAPHY) ]                            |
|        |--> Unpack quest.zip -> opung_archive.jpg                                 |
|        |--> Perform Binary Hex Inspection (Locate EOI Marker FF D9)               |
|        |--> Extract Trailing Payload at Offset 0x175FB                            |
|        +--> Flag Extraction Complete & Verified                                   |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 5. Phase 1: Bit-Stream Forensic Acquisition (FTK Imager)

Bit-stream physical imaging is the most critical phase in preserving the **Strict Evidence Integrity** rule, guaranteeing zero modifications to the original flash drive.

### Operational Step-by-Step Procedure:
1. **Physical Media Connection & Hygiene:**
   - Insert the target USB flash drive into the forensic workstation.
   - **Critical Warning:** If the operating system triggers a dialog prompt such as *"Scan and Fix (Recommended)"* or automatically launches *Windows File Explorer*, **immediately close and dismiss the prompt**.
   - Under no circumstances should an examiner browse, open, or write to the drive directly, as doing so alters last-accessed timestamps and generates hidden OS folders (`System Volume Information`).

2. **Elevated Privileges:**
   - Launch **AccessData FTK Imager** using **Run as Administrator**.
   - Administrative privileges are essential to obtain direct raw disk handles and bypass OS file-locking subsystems.

3. **Initiate Disk Image Wizard:**
   - In the top menu bar, navigate to: `File` > `Create Disk Image...`

4. **Select Evidence Source Type:**
   - Select **Physical Drive** (do *not* select Logical Drive).
   *Forensic Note:* Selecting *Physical Drive* guarantees that the Master Boot Record (MBR), partition tables, volume slack space, and unallocated cluster spaces containing deleted files are mirrored sector-by-sector.
   - Click `Next`.
   - In the *Source Drive Selection* drop-down menu, choose Group 5's USB flash drive.
   - Click `Finish`.

5. **Configure Destination & Format:**
   - In the *Create Image* dialog box, under *Image Destination(s)*, click `Add...`
   - Select **E01 (Expert Witness Format)**. E01 is the industry standard format providing lossless data compression, embedded case metadata, and per-block CRC checksum integrity.
   - Click `Next`.
   - Fill in or skip the *Evidence Item Information* fields, then click `Next`.
   - **Image Destination Folder:** Designate an output directory on the forensic workstation's internal drive (e.g., local work drive). **Never save the image file onto the target evidence drive**.
   - Assign an image file name, for example: `flashdisk_evidence_01`.
   - Set *Image Fragment Size* to `0` (for a single monolithic file) or leave default (`1500 MB`).
   - Click `Finish`.

6. **Cryptographic Hash Verification & Execution:**
   - Ensure the checkbox **"Verify images after they are created"** is checked.
   - Click `Start` to initiate bit-stream reading.
   - Await completion until the verification dialog reports:
     - `MD5 checksum: Verified`
     - `SHA1 checksum: Verified`
   - Upon verification, safely eject the physical USB drive, place it into an anti-static evidence envelope, and conduct all subsequent examinations solely on `flashdisk_evidence_01.E01`.

---

## 6. Phase 2: Forensic Analysis & Deleted File Recovery (Autopsy)

This phase focuses on recovering digital footprints deleted by the subject within the FAT32 partition using `flashdisk_evidence_01.E01`.

### Detailed Execution Steps:

1. **Initialize a New Forensic Case:**
   - Launch **Autopsy Digital Forensics Platform**.
   - Select `New Case`.
   - Enter case parameters:
     - *Case Name:* `Investigation_Group5_Flag1`
     - *Base Directory:* Specify a local case directory.
     - *Case Type:* `Single-user`.
   - Complete the wizard.

2. **Ingest Data Source:**
   - In the *Add Data Source* dialog, select **Disk Image or VM File**.
   - Click `Next`.
   - Browse and select `flashdisk_evidence_01.E01` generated during Phase 1.
   - Leave sector reading and timezone configurations at their defaults.
   - Click `Next`.
   - On the *Configure Ingest Modules* screen, keep the standard modules enabled (specifically *File Extension Mismatch Detector* and *Keyword Search*). Click `Next`, then click `Finish`.

3. **Navigate the File System Tree:**
   - In the left-hand navigation panel (*Data Sources Tree*), expand the image hierarchy:
     ```text
     Data Sources
      +-- flashdisk_evidence_01.E01
           +-- vol2 (Win95 FAT32 (0x0c): 2048-60088319)
     ```
   - Select the partition entry **`vol2 (Win95 FAT32 (0x0c): 2048-60088319)`** to display the root directory contents in the central *Table Viewer*.

4. **Identify Deleted File Records:**
   - Inspect the file table in the upper-central viewer.
   - Look for Autopsy's distinctive deletion indicators:
     - Deleted files are marked with a small **red cross icon (`x`)** superimposed over the file icon.
     - The status column (*Flags / Status*) indicates **`Unallocated`** or a deleted directory flag.
     - This confirms that the directory entry was invalidated in the file allocation table, but the raw cluster blocks remain intact.

5. **Examine & Recover Target File (`hidden_mission.txt`):**
   - Locate the deleted file entry named:
     ```text
     hidden_mission.txt
     ```
   - Click on `hidden_mission.txt` to select it.
   - In the lower *Content Viewer* panel, click the **`Text`** tab.
   - Ensure the **`Strings`** or **`Extracted Text`** view is active.
   - The recovered text payload is immediately readable:

```text
[TOP SECRET - EYES ONLY]
Operasi: Sandi Apostle Peter
Target: Khosy Syehab & Kelompok Forensik
Pesan: Misi rahasia telah selesai. Amankan kunci akses di bawah ini.

FLAG: CODENAME{4P0ST3L_P3T3R_0F_GL0RY}
```

6. **Carve & Export Evidence File:**
   - Right-click `hidden_mission.txt` in the table view.
   - Select **`Extract File(s)`**.
   - Export the recovered file to a local destination (`recovered_files/`) for reporting and court presentation archives.

---

## 7. Phase 3: Digital Artifact & Steganography Analysis (quest.zip / opung_archive.jpg)

For investigators analyzing the challenge via the distributed digital package (`quest.zip`), Group 5 supplied an auxiliary digital artifact: `flag-1/opung_archive.jpg`.

### 7.1 Archive Decompression
Extracting `quest.zip` reveals the following structure:
```bash
unzip quest.zip
# Extracts folder flag-1/ containing flag-1/opung_archive.jpg (95,779 bytes)
```

### 7.2 Binary Structure & Marker Analysis (Hex Inspection)
The JPEG/JFIF format is governed by strict marker specifications:
- **Start of Image (SOI):** Mandatory 2-byte marker `FF D8`.
- **End of Image (EOI):** Mandatory 2-byte marker `FF D9`.

Standard image rendering engines terminate decoding immediately upon reading the `FF D9` marker. Any data appended after `FF D9` is completely ignored by viewers, allowing the image to render without visual artifacts. This technique is known as **End-of-File (EOF) Appending / Steganography**.

Hexadecimal examination of `opung_archive.jpg` reveals:

```text
Offset 0x00000000:  FF D8 FF E0 00 10 4A 46 49 46 00 01 01 01 00 48  | ......JFIF.....H |  <- SOI Marker
... (compressed image stream) ...
Offset 0x000175F0:  8A 28 A2 80 0A 28 A2 8A 00 FF D9                | .(..(....        |  <- EOI Marker (Offset 95,745)
Offset 0x000175FB:  43 4F 44 45 4E 41 4D 45 7B 34 50 30 53 54 33 4C  | CODENAME{4P0ST3L |  <- Trailing Payload
Offset 0x0001760B:  5F 50 33 54 33 52 5F 30 46 5F 47 4C 30 52 59 7D  | _P3T3R_0F_GL0RY} |  <- End of Flag
```

### 7.3 Rapid CLI Extraction Methods
Investigators can reproduce this recovery using any standard CLI utility:

1. **GNU Strings:**
   ```bash
   strings -n 8 flag-1/opung_archive.jpg | grep -i "CODENAME"
   # Output:
   # CODENAME{4P0ST3L_P3T3R_0F_GL0RY}
   ```

2. **Hexdump Tail Analysis (`xxd`):**
   ```bash
   xxd flag-1/opung_archive.jpg | tail -n 4
   # Output:
   # 000175d0: 8a28 a002 8a28 a002 8a28 a002 8a28 a002  .(...(...(...(..
   # 000175e0: 8a28 a002 8a28 a002 8a28 a002 8a28 a002  .(...(...(...(..
   # 000175f0: 8a28 a280 0a28 a28a 00ff d943 4f44 454e  .(...(.....CODEN
   # 00017600: 414d 457b 3450 3053 5433 4c5f 5033 5433  AME{4P0ST3L_P3T3
   # 00017610: 525f 3046 5f47 4c30 5259 7d              R_0F_GL0RY}
   ```

3. **Python One-Liner:**
   ```python
   with open('flag-1/opung_archive.jpg', 'rb') as f:
       data = f.read()
   eoi = data.find(b'\xff\xd9')
   print("Flag:", data[eoi + 2:].decode('utf-8', errors='ignore'))
   ```

---

## 8. Theoretical Principles & Technical Mechanisms

### 8.1 FAT32 File Deletion Mechanics
The FAT32 file system separates metadata cataloging from contiguous cluster storage:
1. **Directory Entry Structure (32-byte Record):** Stores file name, extension, file attributes, MAC timestamps, starting cluster address, and file size in bytes.
2. **Standard File Deletion Process:**
   - The OS does **not** erase or zero-out data within the actual storage clusters.
   - The first byte of the file name in the directory entry is overwritten with hexadecimal **`0xE5`** (`σ` in ASCII), signaling to the file system driver that the entry is available for reuse.
   - Corresponding cluster chain entries in the FAT allocation table are marked as `0x00000000` (Unallocated).
3. **Autopsy Carving Mechanism:**
   - Autopsy traverses raw partition sectors on `vol2`.
   - The parser detects directory records starting with `0xE5`, parses the filename (`hidden_mission.txt`), starting cluster, and file size.
   - As long as no subsequent write operations overwrite those clusters, the payload is recovered with 100% fidelity.

### 8.2 EOF Trailing Data Steganography
- Graphics decoders (e.g., `libjpeg`) parse streams sequentially until encountering `FF D9`.
- Upon reading `FF D9`, the decoding pipeline closes cleanly.
- Supplementary bytes appended past this offset leave image visual appearance untouched, but are detected via file size auditing versus parsed stream size and linear carving.

---

## 9. Evidence Reconstruction & Hexadecimal Verification

| Item | Forensic Parameter | Observed Value |
|---|---|---|
| 1 | **Physical Target Artifact** | `hidden_mission.txt` |
| 2 | **Directory Entry State** | `Unallocated / Deleted Flag (0xE5)` |
| 3 | **Host Partition** | `vol2` (FAT32, Sectors `2048` - `60088319`) |
| 4 | **Digital Auxiliary Artifact** | `flag-1/opung_archive.jpg` |
| 5 | **Embedding Methodology** | Append Injection past JPEG EOI (`0xFFD9`) |
| 6 | **Payload Hex Offset** | `0x000175FB` to `0x0001761B` |
| 7 | **Payload Length** | 32 Bytes |
| 8 | **Payload Integrity** | Intact (*100% Readable / Zero Corruption*) |

---

## 10. Official Flag Consolidation

All physical and digital artifacts engineered by **Group 5** consistently converge on the validated solution:

```text
================================================================================
OFFICIAL FLAG (LEET-SPEAK):
CODENAME{4P0ST3L_P3T3R_0F_GL0RY}

ALPHANUMERIC NORMALIZATION VARIANT:
CODENAME{4P0ST3L_P3T3R_OF_GLORY}
================================================================================
```

---

## 11. Remediation & Secure Data Sanitization

Because standard OS deletion leaves file cluster data completely intact, organizations handling sensitive data must adhere to strict sanitization protocols:
1. **Industry-Standard Media Sanitization:**
   - Standard `Delete` or `Shift + Delete` commands do not scrub physical media.
   - Confidential data must be wiped using utilities compliant with **DoD 5220.22-M** (3-pass overwriting) or **NIST SP 800-88 Rev. 1** (*Purge / Cryptographic Erase*).
2. **Free Space Wiping:**
   - Unallocated and slack spaces must be periodically zero-filled using tools like `BleachBit` or the command `dd if=/dev/zero of=/dev/sdX bs=4M`.
3. **Full Disk Encryption (FDE):**
   - Implement volume-level encryption (**BitLocker To Go**, **LUKS**, or **VeraCrypt**) on all removable media to render deleted and unallocated fragments unreadable without valid cryptographic credentials.

---

## 12. Appendix: Forensic CLI Quick Cheatsheet

For rapid grading and validation, execute the following commands in sequence:

```bash
# 1. Verify challenge archive hash
sha256sum quest.zip

# 2. Inspect archive structure without extracting
unzip -l quest.zip

# 3. Extract challenge files
unzip -q quest.zip

# 4. Extract secret flag from auxiliary image
strings -n 8 flag-1/opung_archive.jpg | grep "CODENAME"

# 5. Programmatic Python verification of EOI marker offset
python3 -c "
data = open('flag-1/opung_archive.jpg', 'rb').read()
eoi = data.find(b'\xff\xd9')
print(f'EOI Offset: {hex(eoi)} | Extracted Flag: {data[eoi+2:].decode()}')
"
```

---
*Official Walkthrough & Solution Guide published by Group 5 (Digital Forensics 2026).*

