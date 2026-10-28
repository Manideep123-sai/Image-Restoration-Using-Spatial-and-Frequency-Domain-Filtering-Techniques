# Image Restoration Using Spatial and Frequency Domain Filtering Techniques

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![HTML5 / Vanilla JS](https://img.shields.io/badge/Platform-HTML5%20%7C%20Vanilla%20JS-orange.svg)](#)
[![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero%20(Pure%20Offline)-brightgreen.svg)](#)
[![Academic Project](https://img.shields.io/badge/Course-BCSE403L%20DIP-purple.svg)](#)

An offline, zero-dependency interactive workbench implementing spatial and frequency domain image restoration techniques with real-time quantitative evaluation (MSE, PSNR, SSIM).

Developed as the final course project for **BCSE403L — Digital Image Processing** at **Vellore Institute of Technology (VIT), Vellore**.

- **Author**: Manideep Sai Chinthala (23BCE0737)
- **Faculty Guide**: Prof. A. Srivani

---

## 📌 Executive Summary

Digital images frequently get corrupted by sensor noise during acquisition or optical blur from motion and defocus. While *image enhancement* focuses on subjective aesthetic adjustment (contrast, brightness), *image restoration* aims to reconstruct the original clean scene using an explicit mathematical inverse of the degradation model.

This project delivers a complete, client-side image restoration workbench built entirely in vanilla JavaScript and HTML5 Canvas with **zero external libraries or server backends**. It integrates 10 algorithms spanning the spatial and frequency domains, a custom 2D Fast Fourier Transform engine, and live side-by-side metric benchmarking.

---

## 🚀 Key Features

- **100% Client-Side & Offline**: Runs instantly in any modern browser without npm, Python, OpenCV, or internet access.
- **Radix-2 2D FFT From First Principles**: Implements the Cooley-Tukey row-column 2D Fast Fourier Transform with bit-reversal and precomputed trigonometric tables directly in JavaScript.
- **Physical Degradation Models**:
  - *Gaussian Noise*: Box-Muller transform with configurable standard deviation $\sigma$.
  - *Impulse Noise*: Uniform random salt-and-pepper noise generator.
  - *Gaussian Defocus Blur*: 2D Gaussian point spread function (PSF) kernel.
  - *Motion Blur*: Trigonometric vector PSF simulating linear camera shake.
- **Tri-Viewport Display**: Synchronized viewports displaying:
  1. `f(x, y)`: Ground-truth original reference.
  2. `g(x, y)`: Degraded input signal ($g = f * h + \eta$).
  3. `f̂(x, y)`: Restored output from the active filter.
- **Objective Evaluation Engine**: Computes real-time:
  - **MSE** (Mean Squared Error)
  - **PSNR** (Peak Signal-to-Noise Ratio in dB)
  - **SSIM** (Structural Similarity Index with an $11 \times 11$ Gaussian window, $\sigma = 1.5$)
- **Automated Benchmark Ranking**: One-click *"Compare All 8 Filters"* button that benchmarks and ranks every algorithm on the active degradation.

---

## 🔬 Restoration Algorithms Implemented

| Domain | Algorithm | Best Used For | Key Formula / Mechanism |
| :--- | :--- | :--- | :--- |
| **Spatial** | **Arithmetic Mean** | Mild Gaussian grain | $\hat{f}(x, y) = \frac{1}{\|S\|} \sum_{(s,t) \in S} g(s, t)$ |
| **Spatial** | **Geometric Mean** | Detail-preserving smoothing | $\hat{f}(x, y) = \left[ \prod_{(s,t) \in S} g(s, t) \right]^{\frac{1}{\|S\|}}$ |
| **Spatial** | **Median Filter** | Moderate impulse noise | $\hat{f}(x, y) = \text{median}_{(s,t) \in S} \{ g(s, t) \}$ |
| **Spatial** | **Adaptive Median** | Dense salt & pepper ($>20\%$) | Dynamically expands window up to $S_{\max}$ when median is corrupted |
| **Frequency** | **Direct Inverse Filter** | Pure blur without noise | $\hat{F}(u, v) = \frac{G(u, v)}{H(u, v)}$ for $\|H(u, v)\| \ge T$ |
| **Frequency** | **Wiener Filter** | Blur with additive noise | $\hat{F}(u, v) = \left[ \frac{H^*(u, v)}{\|H(u, v)\|^2 + K} \right] G(u, v)$ |
| **Frequency** | **Gaussian Low-Pass** | High-frequency grain | Smooth Gaussian attenuation $H(u, v) = e^{-D^2(u,v)/(2D_0^2)}$ |
| **Frequency** | **Butterworth Low-Pass** | Adjustable cutoff rolloff | $H(u, v) = \frac{1}{1 + [D(u, v) / D_0]^{2n}}$ |
| **Enhancement** | **Histogram Equalization** | Contrast redistribution | Cumulative distribution function (CDF) intensity mapping |
| **Enhancement** | **Laplacian Sharpening** | Edge crispness recovery | $\hat{f}_{\text{sharp}} = \hat{f} - c \cdot \nabla^2 \hat{f}$ |

---

## 📊 Experimental Results & Benchmarks

The algorithms were rigorously tested across three standard benchmark images (**Cameraman**, **Astronaut**, **Coins**) at $256 \times 256$ resolution using fixed pseudo-random seeds ($seed = 1$).

### Average PSNR Summary (dB)

| Filter / Degradation | Gaussian Noise ($\sigma=20$) | Salt & Pepper ($20\%$) | Gaussian Blur ($\sigma=2$) | Motion Blur ($L=15, \theta=30^\circ$) | Blur + Noise ($\sigma_b=2, \sigma_n=10$) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Degraded Baseline** | 22.41 dB | 11.83 dB | 22.66 dB | 20.21 dB | 21.59 dB |
| **Arithmetic Mean (5×5)** | 23.45 dB | 19.86 dB | 21.84 dB | 20.07 dB | 21.80 dB |
| **Median Filter (5×5)** | 24.60 dB | 24.55 dB | 22.46 dB | 20.22 dB | 22.15 dB |
| **Adaptive Median ($S_{\max}=7$)** | 24.52 dB | **29.96 dB** | 22.68 dB | 20.22 dB | 21.99 dB |
| **Gaussian Low-Pass ($D_0=30\%$)** | **25.43 dB** | – | – | – | 22.02 dB |
| **Butterworth Low-Pass ($n=2$)** | 24.76 dB | – | – | – | 22.12 dB |
| **Inverse Filter ($T=0.01$)** | 22.41 dB | 11.83 dB | **27.42 dB** | 30.38 dB | 8.70 dB *(Failure)* |
| **Wiener Filter ($K=0.001 / 0.01$)** | 22.48 dB | 11.92 dB | 26.62 dB | **30.43 dB** | **22.35 dB** |

> **Key Theoretical Takeaway**: When blur and noise occur simultaneously, the direct inverse filter collapses catastrophically ($8.70\text{ dB}$, SSIM $0.05$) because dividing by near-zero values of $H(u,v)$ amplifies high-frequency noise into severe ringing. The Wiener filter overcomes this by incorporating noise-to-signal parameter $K$, maintaining stable reconstruction ($22.35\text{ dB}$).

---

## 📁 Repository Structure

```
├── image_restoration_tool.html                                # Standalone interactive web workbench
├── DIP_Project_Final_Report.docx                             # Complete project report (VIT faculty format)
├── DIP_Project_Final_Report.pdf                              # Rendered 17-page submission PDF
├── Image-Restoration-Using-Spatial-and-Frequency-Domain-Filtering-Techniques.pptx  # Presentation deck
└── README.md                                                 # Project documentation
```

---

## 💻 How to Run

1. Clone or download this repository:
   ```bash
   git clone https://github.com/Manideep123-sai/Image-Restoration-Using-Spatial-and-Frequency-Domain-Filtering-Techniques.git
   ```
2. Double-click or open `image_restoration_tool.html` in Google Chrome, Microsoft Edge, Mozilla Firefox, or Safari.
3. No installation, server, build step, or dependencies required!

---

## 📚 References

1. Z. Wang, A. C. Bovik, H. R. Sheikh, and E. P. Simoncelli, *"Image quality assessment: From error visibility to structural similarity,"* IEEE Trans. Image Process., vol. 13, no. 4, pp. 600–612, 2004.
2. A. Horé and D. Ziou, *"Image quality metrics: PSNR vs. SSIM,"* in Proc. ICPR, 2010.
3. H. Hwang and R. A. Haddad, *"Adaptive median filters: New algorithms and results,"* IEEE Trans. Image Process., vol. 4, no. 4, pp. 499–502, 1995.
4. P. Bojarczak and Z. Lukasik, *"Image deblurring – Wiener filter versus TSVD approach,"* Adv. Electr. Electron. Eng., vol. 6, no. 2, pp. 86–89, 2007.
5. S. S. Al-amri, N. V. Kalyankar, and S. D. Khamitkar, *"Deblured Gaussian blurred images,"* J. Computing, vol. 2, no. 4, pp. 33–35, 2010.
6. R. C. Gonzalez and R. E. Woods, *Digital Image Processing*, 4th ed., Pearson, 2018.
