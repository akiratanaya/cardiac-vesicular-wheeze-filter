# Cardiac, Vesicular & Wheeze Acoustic Filter Design

Rangkaian filter analog aktif (*active bandpass filter*) berbasis op-amp **LT6233** (dual-supply $\pm 5\text{V}$) untuk memisahkan komponen akustik auskultasi stetoskop digital menjadi tiga kanal frekuensi:
1. **Filter A (Cardiac):** Suara jantung normal dan murmur (20 Hz – 250 Hz).
2. **Filter B (Vesikular):** Suara napas normal paru-paru (20 Hz – 150 Hz).
3. **Filter C (Wheeze & Crackle):** Suara pernapasan abnormal / wheezing & crackles (200 Hz – 1000 Hz).

Desain disintesis menggunakan **Analog Devices Filter Wizard (v1.3)** dan disimulasikan menggunakan **LTspice**.

---

## Spesifikasi Filter

| Parameter | Filter A (Jantung) | Filter B (Vesikular) | Filter C (Abnormal / Wheeze) |
| :--- | :--- | :--- | :--- |
| **Target Sinyal** | Lub-dub ($S_1, S_2$), murmur | Suara napas vesikular normal | Wheezing (mengi), crackles (ronki) |
| **Topologi** | Kaskade Sallen-Key Orde 4 (HPF + LPF) | Kaskade Sallen-Key Orde 4 (HPF + LPF) | 2-Stage Deliyannis-Friend Bandpass (MFB) |
| **Passband (-3 dB)** | 20 Hz – 250 Hz | 20 Hz – 150 Hz | 200 Hz – 1000 Hz |
| **Frekuensi Karakteristik ($f_c$)** | ~71 Hz | ~55 Hz | ~447 Hz |
| **Gain Passband** | 0 dB | 0 dB | ~0 dB |
| **Atenuasi Stopband** | -40 dB @ 3 kHz | -40 dB @ 3 kHz | -40 dB @ 12 kHz |
| **Op-Amp** | LT6233 | LT6233 | LT6233 |
| **Tegangan Kerja** | $V_p = +5\text{V}$, $V_n = -5\text{V}$, $V_{ref} = 0\text{V}$ | $V_p = +5\text{V}$, $V_n = -5\text{V}$, $V_{ref} = 0\text{V}$ | $V_p = +5\text{V}$, $V_n = -5\text{V}$, $V_{ref} = 0\text{V}$ |

---

## Struktur Direktori

```text
cardiac-vesicular-wheeze-filter/
├── .gitignore
├── README.md
│
├── wizard_config/                     # File konfigurasi desain ADI Filter Wizard
│   ├── filter_a_heart_20_250hz.json.json
│   ├── filter_b_vesicular_20_150hz.json
│   └── filter_c_abnormal_200_1000hz.json
│
├── ltspice/
│   ├── filter_a/                      # Kanal Suara Jantung (20 - 250 Hz)
│   │   ├── StageA_LowPassSallenKey_LT6233.asc/.asy
│   │   ├── StageB_HighPassSallenKey_LT6233.asc/.asy
│   │   ├── ACAnalysis.asc
│   │   ├── NoiseAnalysis.asc
│   │   └── TransientAnalysis.asc
│   │
│   ├── filter_b/                      # Kanal Suara Napas Vesikular (20 - 150 Hz)
│   │   ├── StageA_LowPassSallenKey_LT6233.asc/.asy
│   │   ├── StageB_HighPassSallenKey_LT6233.asc/.asy
│   │   ├── ACAnalysis.asc
│   │   ├── NoiseAnalysis.asc
│   │   └── TransientAnalysis.asc
│   │
│   └── filter_c/                      # Kanal Wheeze & Crackle (200 - 1000 Hz)
│       ├── StageA_BandPassDeliyannisFriendNoPosFB_LT6233.asc/.asy
│       ├── StageB_BandPassDeliyannisFriendNoPosFB_LT6233.asc/.asy
│       ├── ACAnalysis.asc
│       ├── NoiseAnalysis.asc
│       └── TransientAnalysis.asc
│
└── dataset/                           # Dataset acuan suara stetoskop (HLS-CMDS)
    ├── heart_dataset/
    ├── lung_dataset/
    └── Mix/
```

---

## Simulasi di LTspice

Setiap subfolder filter di dalam `ltspice/` menyediakan skematik simulasi berikut:

- **AC Analysis (`ACAnalysis.asc`):** Evaluasi kurva respon frekuensi magnitudo dan fasa (Bode plot) untuk memverifikasi rentang passband dan atenuasi stopband.
- **Transient Analysis (`TransientAnalysis.asc`):** Uji respon domain waktu terhadap sinyal input pulsa/step maupun file audio riil (`.wav`).
- **Noise Analysis (`NoiseAnalysis.asc`):** Evaluasi kerapatan derau masukan/keluaran (*input/output referred noise*) dari rangkaian op-amp.

> **Catatan:** Pastikan model op-amp `LT6233` sudah terpasang di LTspice (dapat diperbarui melalui menu `Tools -> Sync Release`).

---

## Referensi Dataset

Data audio untuk pengujian simulasi transien bersumber dari:
> Y. Torabi, S. Shirani, and J. P. Reilly, *"Descriptor: Heart and Lung Sounds Dataset Recorded from a Clinical Manikin using Digital Stethoscope (HLS-CMDS),"* IEEE Data Descriptions, 2025. [DOI: 10.1109/IEEEDATA.2025.3566012](https://doi.org/10.1109/IEEEDATA.2025.3566012).
