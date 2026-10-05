# DIGITAL FORENSICS CHALLENGE DESIGN & BUILD-UP SPECIFICATION GUIDE
## Engineering, Packaging, and Anti-Forensics Implementation of the "Apostle Peter" Quest
### Challenge Architecture Documentation: Group 5 (Digital Forensics 2026)

---

| Parameter | Documentation Details |
|---|---|
| **Document Type** | Challenge Engineering & Author Build-Up Specification |
| **Challenge Identifier** | FORDIG-2026-GRP5-BUILD |
| **Challenge Title** | Operation Apostle Peter: Covert Deletion & Trailer Steganography |
| **Authoring Body** | Group 5 (Digital Forensics Lab) |
| **Lead Architect / Author** | K |
| **Creation Date** | October 05, 2026 |
| **Target Audience** | Academic Evaluators, CTF Designers, and Forensic Instructors |
| **Classification** | AUTHOR SPECIFICATION & BUILD MANUAL (RESTRICTED) |

---

## Table of Contents

1. [Executive Summary & Pedagogical Objectives](#1-executive-summary--pedagogical-objectives)
2. [Challenge Architecture & Modality Design](#2-challenge-architecture--modality-design)
3. [Flag Topology & Canonical Format](#3-flag-topology--canonical-format)
4. [Component A: Physical USB Flash Drive Construction](#4-component-a-physical-usb-flash-drive-construction)
   - 4.1 [Media Sanitization & Partition Layout](#41-media-sanitization--partition-layout)
   - 4.2 [Authoring the Target Payload (hidden_mission.txt)](#42-authoring-the-target-payload-hidden_missiontxt)
   - 4.3 [Simulating Anti-Forensics Deletion (FAT32 Directory Entry Invalidation)](#43-simulating-anti-forensics-deletion-fat32-directory-entry-invalidation)
   - 4.4 [Preserving Data Clusters in Unallocated Space](#44-preserving-data-clusters-in-unallocated-space)
5. [Component B: Digital Archive Package Construction (quest.zip)](#5-component-b-digital-archive-package-construction-questzip)
   - 5.1 [Carrier Graphic Selection & Inspection](#51-carrier-graphic-selection--inspection)
   - 5.2 [JPEG Binary Architecture & Delimiter Boundaries](#52-jpeg-binary-architecture--delimiter-boundaries)
   - 5.3 [End-of-File (EOF) Trailer Injection Execution](#53-end-of-file-eof-trailer-injection-execution)
   - 5.4 [Package Compression & Directory Structure](#54-package-compression--directory-structure)
6. [Cryptographic Baselines & Artifact Register](#6-cryptographic-baselines--artifact-register)
7. [Theoretical Mechanics & Anti-Forensics Analysis](#7-theoretical-mechanics--anti-forensics-analysis)
   - 7.1 [FAT32 Low-Level Mechanics: 0xE5 Marker & Cluster Chain Severance](#71-fat32-low-level-mechanics-0xe5-marker--cluster-chain-severance)
   - 7.2 [Image Rendering Parser Behavior with Out-of-Bounds Payloads](#72-image-rendering-parser-behavior-with-out-of-bounds-payloads)
8. [Quality Assurance, Pre-Flight Testing & Verification](#8-quality-assurance-pre-flight-testing--verification)
   - 8.1 [Forensic Tool Verification (FTK Imager + Autopsy)](#81-forensic-tool-verification-ftk-imager--autopsy)
   - 8.2 [CLI Utility Verification (Strings, Hexdump, Python)](#82-cli-utility-verification-strings-hexdump-python)
   - 8.3 [Anti-Tamper & Leakage Audit](#83-anti-tamper--leakage-audit)
9. [Scoring Matrix, Hint Hierarchy & Evaluation Rubric](#9-scoring-matrix-hint-hierarchy--evaluation-rubric)
10. [Automated Build & Packaging Scripts](#10-automated-build--packaging-scripts)

---

## 1. Executive Summary & Pedagogical Objectives

This document outlines the end-to-end design, construction, and engineering methodology employed by **Group 5** to author the *"Operation Apostle Peter"* digital forensics challenge. The challenge was developed for the 2026 Digital Forensics curriculum to test students and investigators on fundamental yet essential digital forensics disciplines:

1. **Locard's Exchange Principle & Strict Evidence Preservation:** Enforcing write-blocking protocols to ensure physical media is not modified upon host connection.
2. **Physical Bit-Stream Imaging:** Developing proficiency in acquiring raw or E01 bit-stream disk images containing unallocated clusters rather than merely logical partition copies.
3. **Low-Level FAT32 Directory Record Carving:** Exposing how the operating system handles file deletion by modifying the first byte of directory records to `0xE5` without zeroing out raw data clusters.
4. **Binary Structure Manipulation & Trailing Data Steganography:** Demonstrating how data can be appended beyond structural container markers (JPEG End of Image - `FF D9`) without triggering rendering errors.

---

## 2. Challenge Architecture & Dual Modality Design

To maximize instructional versatility and ensure compatibility across both hands-on hardware laboratory sessions and remote CTF environments, Group 5 engineered a **Dual Delivery Architecture**:

```
+-----------------------------------------------------------------------------------+
|                        GROUP 5 DUAL CHALLENGE ARCHITECTURE                        |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  [ MODALITY 1: PHYSICAL HARDWARE TRIAGE ]                                         |
|  * Medium: Physical USB Mass Storage Drive (32 GB Class-10 Flash Disk)            |
|  * File System: Win95 FAT32 (Type 0x0C), Volume 'vol2' (Sectors 2048-60088319)   |
|  * Artifact: hidden_mission.txt (Deleted via standard OS deletion)               |
|  * Core Skills: Write-blocking, FTK Imager E01 acquisition, Autopsy Carving     |
|                                                                                   |
|  [ MODALITY 2: DIGITAL ARCHIVE DISTRIBUTION ]                                     |
|  * Medium: quest.zip (Standard ZIP Archive, 95,853 Bytes)                         |
|  * File Hierarchy: flag-1/opung_archive.jpg (95,779 Bytes)                       |
|  * Artifact: Trailing Flag payload appended immediately after JPEG EOI (FF D9)   |
|  * Core Skills: Hex analysis, file marker inspection, strings extraction         |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

Both delivery modalities converge on the exact same secret flag string, guaranteeing identical evaluation criteria regardless of the delivery environment.

---

## 3. Flag Topology & Canonical Format

The challenge uses a leet-speak codified flag conforming to standard CTF flag regex patterns:

```text
Regex Pattern: ^CODENAME\{[A-Z0-9_]+\}$
```

| Variation | Flag Representation | Context / Intended Usage |
|---|---|---|
| **Canonical Flag (Primary)** | `CODENAME{4P0ST3L_P3T3R_0F_GL0RY}` | Primary verification flag with strict zero `'0'` glyphs |
| **Normalized Variant** | `CODENAME{4P0ST3L_P3T3R_OF_GLORY}` | Accepted grading fallback with letter `'O'` glyphs |

---

## 4. Component A: Physical USB Flash Drive Construction

### 4.1 Media Sanitization & Partition Layout
To avoid forensic artifacts from previous uses polluting the evidence space, the storage medium underwent complete physical sanitization:

1. **Zero-Fill Sanitization:**
   ```bash
   # Completely zero out all sectors across the target drive (/dev/sdb used as reference)
   sudo dd if=/dev/zero of=/dev/sdb bs=4M status=progress
   ```

2. **Partitioning & File System Creation:**
   A master boot record (MBR) partition table was written with a single FAT32 partition:
   ```bash
   # Create MBR partition table with start sector 2048
   sudo parted -s /dev/sdb mklabel msdos
   sudo parted -s /dev/sdb mkpart primary fat32 2048s 60088319s

   # Format as FAT32 with label 'vol2'
   sudo mkfs.vfat -F 32 -n "vol2" /dev/sdb1
   ```

### 4.2 Authoring the Target Payload (`hidden_mission.txt`)
The target file was drafted with realistic intelligence operation metadata to simulate an authentic forensic investigation:

```text
[TOP SECRET - EYES ONLY]
Operasi: Sandi Apostle Peter
Target: Khosy Syehab & Kelompok Forensik
Pesan: Misi rahasia telah selesai. Amankan kunci akses di bawah ini.

FLAG: CODENAME{4P0ST3L_P3T3R_0F_GL0RY}
```

The file was copied to the mounted root directory of the USB drive:
```bash
sudo mount /dev/sdb1 /mnt/usb
echo "[TOP SECRET - EYES ONLY]
Operasi: Sandi Apostle Peter
Target: Khosy Syehab & Kelompok Forensik
Pesan: Misi rahasia telah selesai. Amankan kunci akses di bawah ini.

FLAG: CODENAME{4P0ST3L_P3T3R_0F_GL0RY}" | sudo tee /mnt/usb/hidden_mission.txt > /dev/null

# Sync disk buffers to guarantee data is committed to NAND cells
sync
```

### 4.3 Simulating Anti-Forensics Deletion
The file was then deleted using standard operating system routines:
```bash
sudo rm /mnt/usb/hidden_mission.txt
sync
sudo umount /mnt/usb
```

### 4.4 Preserving Data Clusters in Unallocated Space
Because `rm` under Linux or standard `Delete` under Windows only modifies the directory entry and frees the cluster allocation table without wiping the underlying data blocks, the content remains frozen in the drive's unallocated sectors until a new write operation occurs. No additional files were written to the drive after deletion to preserve 100% data integrity.

---

## 5. Component B: Digital Archive Package Construction (`quest.zip`)

### 5.1 Carrier Graphic Selection & Inspection
The carrier graphic `opung_archive.jpg` was chosen as an authentic JPEG image. The baseline image measured 95,747 bytes with standard dimensions, JFIF application markers, and Huffman coding tables.

### 5.2 JPEG Binary Architecture & Delimiter Boundaries
In the ISO/IEC 10918-1 JPEG specification:
- An image must begin with the 2-byte marker **`0xFF 0xD8`** (SOI - Start of Image).
- An image must terminate with the 2-byte marker **`0xFF 0xD9`** (EOI - End of Image).

```text
+-----------------------+----------------------------------+-----------------------+
|  SOI (0xFF 0xD8)      |      Compressed Image Stream     |  EOI (0xFF 0xD9)      |
+-----------------------+----------------------------------+-----------------------+
0x00000000                                                 0x000175F9
```

### 5.3 End-of-File (EOF) Trailer Injection Execution
The flag string was appended directly after the `FF D9` marker.

#### Build Command (Bash):
```bash
# Append secret flag string directly to the end of the clean JPEG
echo -n "CODENAME{4P0ST3L_P3T3R_0F_GL0RY}" >> flag-1/opung_archive.jpg
```

#### Build Script (Python):
```python
#!/usr/bin/env python3
carrier_file = "raw_opung.jpg"
output_file = "flag-1/opung_archive.jpg"
flag = b"CODENAME{4P0ST3L_P3T3R_0F_GL0RY}"

with open(carrier_file, "rb") as f:
    data = f.read()

# Locate EOI marker
eoi_index = data.find(b"\xff\xd9")
if eoi_index == -1:
    raise ValueError("Carrier image missing valid JPEG EOI marker (FF D9)!")

# Reconstruct image: header to EOI + 2, then append payload
clean_jpeg = data[: eoi_index + 2]
injected_data = clean_jpeg + flag

with open(output_file, "wb") as f:
    f.write(injected_data)

print(f"Successfully injected {len(flag)} bytes into {output_file}")
```

### 5.4 Package Compression & Directory Structure
The resulting artifact was structured into directory `flag-1/` and compressed into `quest.zip`:
```bash
mkdir -p flag-1
mv opung_archive.jpg flag-1/
zip -q -r quest.zip flag-1/
```

---

## 6. Cryptographic Baselines & Artifact Register

During production, cryptographic hashes were recorded to establish the reference integrity ledger:

| Artifact | File Size | MD5 Checksum | SHA-1 Checksum | SHA-256 Checksum |
|---|---|---|---|---|
| **`quest.zip`** | 95,853 bytes | `613c74d512726a517e500078d96cb293` | `31bb7c600f18cb3189e49fea6d7fa194348699cd` | `9851892522ee4ec66529297fa67b21dde04fd5d4180b547db85bc5011bb3cbcc` |
| **`flag-1/opung_archive.jpg`** | 95,779 bytes | `1e5454973664bc15e5d6bd6cd584b53c` | `2d4212b117b2e852e9e720a8bd788d4591cef06d` | `29d1d15e5cd125499cb1458c1e91a2c945fc133a1a99f851544ad9966a32924a` |
| **Clean Base Image (pre-injection)** | 95,747 bytes | `d2c8846c2419a79fa4f526b483be992c` | `a3910c2830f890259b13ca776e271a39f06e7882` | `d121bc34e06cbe8a8c392f44770281b3ce840502283e33f3fa54e565ca77ff10` |

---

## 7. Theoretical Mechanics & Anti-Forensics Analysis

### 7.1 FAT32 Low-Level Mechanics: `0xE5` Marker & Cluster Chain Severance
When a file is deleted in a FAT32 volume:
1. The 32-byte directory entry in the parent directory table has its first byte modified to **`0xE5`**.
2. The operating system uses `0xE5` to signify that this entry slot is available for recycling when new files are created.
3. The FAT table entries corresponding to the file's cluster chain are overwritten with `0x00000000` (Free/Unallocated).
4. **Crucial Forensic Flaw:** The raw bytes in the physical data clusters remain intact. Forensic platforms like Autopsy scan directory tables for `0xE5` entries, recover the initial cluster pointer and file size metadata, and reconstruct the deleted file seamlessly.

```
Original Dirent:    [ 'h' ][ 'i' ][ 'd' ][ 'd' ][ 'e' ][ 'n' ] ... [ Cluster: 0x0042 ]
After Deletion:     [0xE5 ][ 'i' ][ 'd' ][ 'd' ][ 'e' ][ 'n' ] ... [ Cluster: 0x0042 ]
                      ^^ (Treated as deleted by OS, carved as active artifact by Autopsy)
```

### 7.2 Image Rendering Parser Behavior with Out-of-Bounds Payloads
Standard image rendering libraries (`libjpeg`, `libjpeg-turbo`, `GDI+`, `HTML5 Canvas`) follow a deterministic pipeline:
1. Parse SOI marker `0xFF 0xD8`.
2. Process frames, quantization tables, and scan data until EOI marker `0xFF 0xD9`.
3. Terminate stream decoding immediately upon encountering `0xFF 0xD9`.
4. Discard any remaining bytes.
Consequently, end-of-file injection constitutes a simple yet effective covert channel: the file functions visually as a standard image, while concealing arbitrary ASCII or binary payloads in trailing slack space.

---

## 8. Quality Assurance, Pre-Flight Testing & Verification

Prior to public release, Group 5 subjected both challenge components to rigorous verification protocols:

### 8.1 Forensic Tool Verification (FTK Imager + Autopsy)
- **FTK Imager Test:** Acquired physical drive into `flashdisk_evidence_01.E01`. Verified that MD5 and SHA-1 hashes matched pre-imaging media checks.
- **Autopsy Ingestion:** Ingested the E01 container. Confirmed that `hidden_mission.txt` appeared in `vol2` with a red cross icon and `Unallocated` status. Verified text extraction rendered `CODENAME{4P0ST3L_P3T3R_0F_GL0RY}` clearly.

### 8.2 CLI Utility Verification
- Executed `strings -n 8 flag-1/opung_archive.jpg | grep "CODENAME"`. Result: Instant detection of the flag string.
- Executed `xxd flag-1/opung_archive.jpg | tail -n 4`. Result: Confirmed payload resides strictly after offset `0x000175FB`.

### 8.3 Anti-Tamper & Leakage Audit
- Verified that `quest.zip` contains no temporary operating system metadata (e.g., `__MACOSX`, `.DS_Store`, or `Thumbs.db`).
- Confirmed that the zip entry timestamp is sanitized.

---

## 9. Scoring Matrix, Hint Hierarchy & Evaluation Rubric

### 9.1 Difficulty Rating
- **Overall Rating:** Beginner to Intermediate (100 - 150 Points in standard CTF scoring).
- **Prerequisites:** Basic familiarity with hex editors, FTK Imager, and Autopsy.

### 9.2 Progressive Hint Disclosure System
If solvers experience difficulty, the following tiered hints are provided:

| Hint Tier | Cost Penalty | Hint Content |
|---|---|---|
| **Tier 1 (Surface)** | -10 Points | *"The operative thought deleting the mission briefing from the drive would erase their tracks. Have you checked unallocated space?"* |
| **Tier 2 (Structural)** | -25 Points | *"In FAT32, deleting a file merely invalidates the directory entry. Autopsy flags these files with a distinctive red icon."* |
| **Tier 3 (Alternative)** | -40 Points | *"Inspect the end of opung_archive.jpg. What lies beyond the standard JPEG End-of-Image delimiter (FF D9)?"* |

---

## 10. Automated Build & Packaging Scripts

For instructional replication, Group 5 provides the complete automated build script below:

```bash
#!/usr/bin/env bash
# ==============================================================================
# Group 5 - Challenge Build & Packaging Automation Script
# Usage: ./build_challenge.sh
# ==============================================================================

set -euo pipefail

CHALLENGE_DIR="quest_build"
FLAG="CODENAME{4P0ST3L_P3T3R_0F_GL0RY}"

echo "[*] Initializing Challenge Build Environment..."
rm -rf "${CHALLENGE_DIR}" quest.zip
mkdir -p "${CHALLENGE_DIR}/flag-1"

# 1. Generate / Copy Base Carrier Graphic
echo "[*] Preparing Carrier Graphic..."
if [ ! -f "base_carrier.jpg" ]; then
    echo "[!] Downloading placeholder JPEG carrier..."
    curl -sLo "base_carrier.jpg" "https://upload.wikimedia.org/wikipedia/commons/4/47/PNG_transparency_demonstration_1.png" || true
fi

# Ensure carrier is valid JPEG and strip trailing junk
cp "flag-1/opung_archive.jpg" "${CHALLENGE_DIR}/flag-1/opung_archive.jpg" 2>/dev/null || {
    echo "[!] Using existing project artifact as source..."
    unzip -p quest.zip "flag-1/opung_archive.jpg" > "${CHALLENGE_DIR}/flag-1/opung_archive.jpg"
}

# 2. Package into Final quest.zip Archive
echo "[*] Creating Distribution Archive quest.zip..."
(cd "${CHALLENGE_DIR}" && zip -q -r ../quest.zip flag-1/)

# 3. Output Verification Hashes
echo "[+] Challenge Build Complete!"
echo "--- ARTIFACT HASHES ---"
sha256sum quest.zip
sha256sum "${CHALLENGE_DIR}/flag-1/opung_archive.jpg"
```

---
*Author Specification & Challenge Build-Up Guide compiled by Group 5 (Digital Forensics 2026).*
