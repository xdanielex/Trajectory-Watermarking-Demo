# Trajectory Watermarking Final Demo 2026
**Invisible DCT-QIM Watermarking for Images and Videos**

---

## 📌 Project Overview
Technical demonstration of an invisible watermarking system using **DCT-QIM** (Discrete Cosine Transform + Quantization Index Modulation). 

For detailed information regarding the underlying theory, algorithm architecture, and performance analysis, please refer to the provided **watermark.pdf** file.

### ⚠️ Demo Restrictions
* **Fixed Payload**: Any message provided via `--msg` is internally overridden with: `test2026`.
* **Usage**: Licensed for technical evaluation and personal use only. Reverse engineering and commercial use are strictly prohibited (see `license.txt`).

### 💼 Commercial Licensing
This is a limited demonstration version. For commercial versions, full source code access, or custom implementations, please contact:
**xdaniele.rufox@gmail.com**

---

## 💻 System Requirements
* **OS**: Windows 11 (64-bit) – *Compiled and optimized for this architecture.*
* **Dependency**: **FFmpeg** must be installed and added to your system's **PATH**. This is mandatory for all video embedding and extraction features.

---

## 🚀 Quick Start Examples

### 1. Image Watermarking
**Embedding:**
trajectory_watermark_final_demo.exe embed --in file.jpg --out file_out.jpg --msg "test2026" --r_rep 16

**Extraction:**
trajectory_watermark_final_demo.exe extract --in file_out.jpg --profile auto --ideal "test2026" --r_rep 16

### 2. Video Watermarking
**Embedding:**
trajectory_watermark_final_demo.exe embed --in file.mp4 --out file_out.mp4 --msg "test2026" --r_rep 5 --frame 50

**Extraction:**
trajectory_watermark_final_demo.exe extract --in file_out.mp4 --profile auto --frames 1

---

## ⚙️ Parameter Reference

| Parameter | Default | Description |
| :--- | :--- | :--- |
| --r_rep | 18 | Redundancy Factor (1-128). Higher values increase resistance to compression but increase processing time. |
| --profile | auto | low, high, or auto. Adjusts extraction sensitivity based on expected noise/compression. |
| --ideal | - | Used to calculate the Levenshtein distance (accuracy %) against an expected string. |
| --frame | 1 | (Video only) Sets the frame interval for embedding (e.g., --frame 50 marks every 50th frame). |
| --frames | 5 | (Video only) Number of frames to analyze during extraction. |

---

## 📂 Package Contents
* trajectory_watermark_final_demo.exe: Main application.
* license.txt: Legal terms and usage restrictions (English).
* watermark.pdf: Full technical documentation, algorithm details, and test reports.

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
