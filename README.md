# Trajectory Watermarking Final Demo 2026
**Invisible DCT-QIM + Fourier-Mellin Watermarking for Images and Videos**

---

## 📌 Project Overview
Technical demonstration of an invisible watermarking system using **DCT-QIM** (Discrete Cosine Transform + Quantization Index Modulation) for the readable message, plus an optional **Fourier-Mellin fingerprint** channel that survives geometric attacks (rotation / scaling / cropping).

For detailed information regarding the underlying theory, algorithm architecture, and performance analysis, please refer to the provided **watermark.pdf** file.

## 🎛️ Two Channels & the `--strong` flag
* **Channel A (always active)** — readable message (up to 13 characters), embedded with DCT-QIM + Reed-Solomon + beam-search decoding. Robust to compression and noise, but fragile to geometric attacks.
* **Channel B (optional, `--strong[=X]`)** — a 16-bit fingerprint (first 16 bits of `SHA-256(message)`) embedded in the Fourier-Mellin domain. Rotation becomes an angular shift, scaling a radial shift, translation has no effect → the fingerprint **survives rotation / scaling / cropping**.
* **Default behavior**: without `--strong` only Channel A is embedded, so the image is **not degraded**. Use the same flag/value on extract.
* `X` is the strength (default `1.5`). Larger `X` → more robust but more visible; smaller `X` → cleaner image but weaker. Typical range `1.0`–`2.5`.
* **v2.0 — geometric realignment**: when `--strong` recovers the attack geometry, the image is realigned and **Channel A is retried**, so the readable message can be recovered after rotations that preserve pixels (e.g. 180°; also 90°/270° on near-square images). If the attack interpolated (blur), the fingerprint is reported as fallback.
* **Known limitation — saturated areas**: Channel A is embedded in the *luminance* (YCbCr). On blocks that are pure black, pure white, or fully colour-saturated (any RGB channel at 0 or 255), the QIM pattern's luminance swing is clipped by the RGB round-trip, so those blocks cannot carry the message. Natural photos rarely contain such blocks (the watermark recovers 100% on them); synthetic patterns like ffmpeg's `testsrc`/`smptebars` or solid black/white images do **not** work — use a real photo or a grey+noise test image.

## 📺 Video Demonstration

[![Watch the Watermark Demo](https://img.youtube.com/vi/F7pT-sET_-g/maxresdefault.jpg)](https://www.youtube.com/watch?v=F7pT-sET_-g)

---

### ⚠️ Demo Restrictions
* **Fixed Payload**: Any message provided via `--msg` is internally overridden with: `demo2026`. A notice is printed: `This demo copy only allows writing "demo2026"`.
* **Usage**: Licensed for technical evaluation and personal use only. Reverse engineering and commercial use are strictly prohibited (see `license.txt`).

### 💼 Commercial Licensing
This is a limited demonstration version. For commercial versions, full source code access, or custom implementations, please contact:
**xdaniele.rufox@gmail.com**

---
### Trajectory Watermark GUI PRO

An advanced cryptographic utility for data integrity and trajectory-based watermarking, designed for high-precision data protection on Windows.

[![Gumroad](https://img.shields.io/badge/Gumroad-Get%20Trajectory%20Watermark%20PRO-212121?style=for-the-badge&logo=gumroad)](https://xdrufox.gumroad.com/l/watermark)

---
### 🌐 Official Academic Publication (Zenodo)
The theoretical foundations and algorithmic specifications of this architecture have been officially published and archived on **Zenodo**:

👉 **[Read the official paper on Zenodo](https://zenodo.org/records/20303648)**

**How to cite:**
Rufo, D. (2026). Mathematical Abstract of a DCT-QIM Watermarking System with RS Coding and Soft Beam Decoding. Zenodo. https://doi.org/10.5281/zenodo.20303648

## 🔑 Master Key — Keep It Secret
The watermark protects your photos: the **master key is your verification secret**, used to prove ownership/authenticity. It is **not** an encryption key for the media, and it is **never distributed** with the software.

* **First run**: the program asks for the key and stores it locally in `masterkey.txt` (owner-only permissions, `600` on Linux). It can also be provided via the `TW_MASTER_KEY` environment variable; previous keys can be supplied via `TW_PREV_KEYS` (semicolon-separated).
* **Who has the key can read/verify** the watermark — share it only (privately) with people who must authenticate your files.
* **Without the key**, the watermark cannot be read, located or removed in a targeted way: the block positions are derived from the key. Re-watermarking with a *different* key does **not** erase the original pattern — the two watermarks occupy nearly disjoint, key-derived blocks and the original stays embedded (verified: it remained 100% readable after a low-redundancy second embed). A heavy second embed at the same or higher `--r_rep` (e.g. the default 18) corrupts ~15% of the original's blocks through block collisions and makes it unreadable — that is interference (jamming), not surgical removal.
* **With the key**, an attacker can both *read* the watermark and remove it cleanly (the slot positions are known), so the key is the only thing protecting the mark — keep it secret. Blind geometric attacks (rotation / scaling / cropping) still fail to defeat the Channel B fingerprint.
* **Never commit `masterkey.txt`** to the repository or include it in backups/distributions.

## 💻 System Requirements
* **OS**:
  * **Windows 11** (64-bit) – *Compiled and optimized for this architecture.*
  * **Linux** (x86_64) – *Native executable, statically linked (libstdc++/libgcc).*
* **Dependency**: **FFmpeg** must be installed and added to your system's **PATH**. This is mandatory for all video embedding and extraction features (on both Windows and Linux).

---

## 🚀 Quick Start Examples

### 1. Image Watermarking
**Embedding (Channel A only — image not degraded):**
```
trajectory_watermark_final_demo.exe embed --in file.jpg --out file_out.jpg --msg "demo2026" --r_rep 16
```

**Embedding with geometric robustness (`--strong`):**
```
trajectory_watermark_final_demo.exe embed --in file.jpg --out file_out.jpg --msg "demo2026" --r_rep 16 --strong=1.5
```

**Extraction:**
```
trajectory_watermark_final_demo.exe extract --in file_out.jpg --profile auto --ideal "demo2026" --r_rep 16 --strong=1.5
```

**Linux:**
```
./trajectory_watermark_final_demo embed --in file.jpg --out file_out.jpg --msg "demo2026" --strong=1.5
./trajectory_watermark_final_demo extract --in file_out.jpg --ideal "demo2026" --strong=1.5
```

### 2. Video Watermarking
**Embedding:**
```
trajectory_watermark_final_demo.exe embed --in file.mp4 --out file_out.mp4 --msg "demo2026" --r_rep 5 --frame 50 --strong=1.5
```

**Extraction:**
```
trajectory_watermark_final_demo.exe extract --in file_out.mp4 --profile auto --frames 1 --strong=1.5
```

---

## ⚙️ Parameter Reference

| Parameter | Default | Description |
| :--- | :--- | :--- |
| --strong[=X] | disabled | Enables Channel B (Fourier-Mellin fingerprint) for robustness against geometric attacks. `X` = strength, e.g. `--strong=1.5` (default 1.5). Use the same flag on extract. |
| --r_rep | 18 | Redundancy Factor (1-128). The watermark needs **4 + 128·N** 8×8 blocks (for N=18 that is 2308 blocks ≈ 384×384). If the image has fewer blocks, the same blocks are **reused/overwritten**: the payload is corrupted *and* the image is visibly degraded (measured: 128×96 + N=18 → ~17 dB, unusable; N=1 → ~29 dB, clean). With enough blocks (e.g. 800×600 + N=18 → ~38 dB) there is no reuse and the photo is essentially untouched. So **small images need a small value** (`--r_rep 1` for 128×96), **large images can use a larger value** (18–32). Extraction auto-detects it in [1..32]; for values > 32 pass the same `--r_rep` to extract. |
| --profile | auto | low, high, or auto. Adjusts extraction sensitivity based on expected noise/compression. |
| --ideal | - | Used to calculate the Levenshtein distance (accuracy %) against an expected string. |
| --frame | 1 | (Video only) Sets the frame interval for embedding: `--frame N` marks 1 frame every N+1 (range 1-1000). E.g. `--frame 50` marks about 2% of the frames. |
| --frames | 5 | (Video only) Number of frames to analyze during extraction (range 1-1000). **v2.0**: frames carrying the pilot signal (watermarked frames) are auto-selected and averaged into one single decode, making extraction much faster and more robust on sparsely marked videos. Falls back to the best-of-N per-frame scan if aggregation fails. |
| --version, -v | - | Prints the version: `trajectory_watermark v2.0`. |
| --help, -h | - | Prints this English parameter help. |
| --mkwrite <key> | - | Adds a key to the numbered master-key list (`masterkeys.txt`). Duplicates are not added (existing index is reused). |
| --mkread | - | Lists all stored keys with index, SHA-256 prefix and `(in use)` marker. |
| --mkshow | - | Shows only the key currently in use. |
| --mkset <index> | - | Switches the active key to the given index. |
| --no_sync | - | Disables Channel B (same as omitting `--strong`). Kept for backward compatibility. |

> **Multiple master keys (v2.0)**: `masterkeys.txt` stores a numbered key list. The key-management commands (`--mkwrite`, `--mkread`, `--mkshow`, `--mkset`) can be combined with `embed`/`extract` in a single invocation and run in command order. Extraction tries only the key currently in use (plus `TW_PREV_KEYS`); it never auto-tries the whole list. The active key remains your verification secret and is never distributed.

### 📏 Minimum image size per `--r_rep` (no block reuse)

The watermark needs **4 + 128·N** 8×8 blocks. Stay at or above the minimum size to avoid block reuse; below it, blocks are overwritten and the image degrades.

| `--r_rep` | Blocks needed (4 + 128·N) | Minimum image size (square) |
| :---: | :---: | :---: |
| 1 | 132 | 96×96 |
| 2 | 260 | 136×136 |
| 4 | 516 | 184×184 |
| 8 | 1028 | 264×264 |
| 12 | 1540 | 320×320 |
| 16 | 2052 | 368×368 |
| **18 (default)** | **2308** | **392×392** |
| 24 | 3076 | 448×448 |
| 32 | 4100 | 520×520 |

---

## 📂 Package Contents
* `trajectory_watermark_final_demo.exe` – Main application (Windows 11, 64-bit).
* `trajectory_watermark_final_demo` – Native Linux executable (x86_64).
* `license.txt` – Legal terms and usage restrictions (English).
* `watermark.pdf` – Full technical documentation, algorithm details, and test reports.

---

**Developer:** Daniele Rufo  
**Year:** 2026
---

---

### Support my Research 🚀
If you find this project useful for your benchmarks or academic evaluation, consider supporting my independent research:

[![Donate with PayPal](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://paypal.me/xdanielex272)
[![Donate with BTC](https://img.shields.io/badge/Donate-Bitcoin-orange.svg)](#)
[![Donate with USDT](https://img.shields.io/badge/Donate-Tether-green.svg)](#)

* **Bitcoin (BTC):** `bc1q4l9v8welwr6mp4g6uc2t7ex0n274malynq6yqj`
* **Tether (USDT - TRC20):** `TA3m7pqk1mTgZtFQHf7KufAqnaqsN95kPh`

---
