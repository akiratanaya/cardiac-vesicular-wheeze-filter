# Cardiac, Vesicular & Wheeze Acoustic Filter Design

Proyek ini berisi perancangan dan simulasi rangkaian **filter analog aktif (active bandpass filter)** untuk memisahkan dan mengisolasi sinyal akustik biomedis yang diperoleh dari stetoskop digital / sensor auskultasi. Filter dirancang untuk memisahkan tiga kategori suara: suara jantung (cardiac), suara pernapasan normal (vesikular), dan suara pernapasan abnormal (mengi/wheeze & ronki/crackle).

Desain awal disintesis menggunakan **Analog Devices Filter Wizard (FW v1.3)** dan disimulasikan menggunakan **LTspice** dengan operational amplifier berderau rendah dan berkecepatan tinggi **LT6233** (Dual Supply $\pm 5\text{V}$).

---

## 🎯 Spesifikasi Filter

| Filter | Target Sinyal | Topologi Rangkaian | Rentang Frekuensi (Passband) | Frekuensi Tengah ($f_c$) | Redaman Stopband |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Filter A** | Suara Jantung (*Cardiac / Heart Sound*) | 2-Stage Sallen-Key (HPF + LPF) | 20 Hz – 250 Hz | ~71 Hz | -40 dB @ 3 kHz |
| **Filter B** | Suara Napas Vesikular (*Vesicular Breath*) | 2-Stage Sallen-Key (HPF + LPF) | 20 Hz – 150 Hz | ~55 Hz | -40 dB @ 3 kHz |
| **Filter C** | Suara Napas Abnormal (*Wheeze & Crackle*) | 2-Stage Deliyannis-Friend Bandpass | 200 Hz – 1000 Hz | ~447 Hz | -40 dB @ 12 kHz |

---

## 📁 Struktur Direktori

```text
cardiac-vesicular-wheeze-filter/
├── .gitignore                    # Mengabaikan file artefak simulasi LTspice (*.raw, *.log, dll.)
├── README.md                     # Dokumentasi proyek dan panduan struktur direktori
│
├── wizard_config/                # File konfigurasi desain dari Analog Devices Filter Wizard
│   ├── filter_a_heart_20_250hz.json.json   # Konfigurasi Filter A (Jantung)
│   ├── filter_b_vesicular_20_150hz.json     # Konfigurasi Filter B (Vesikular)
│   └── filter_c_abnormal_200_1000hz.json    # Konfigurasi Filter C (Abnormal/Wheeze)
│
└── ltspice/                      # Rangkaian dan file simulasi LTspice
    ├── .gitignore                # Mengabaikan file output audio (*.wav) & binary cache
    │
    ├── filter_a/                 # Rangkaian & simulasi Filter A (Suara Jantung)
    │   ├── StageA_LowPassSallenKey_LT6233.asc/.asy   # Sub-circuit Sallen-Key LPF
    │   ├── StageB_HighPassSallenKey_LT6233.asc/.asy  # Sub-circuit Sallen-Key HPF
    │   ├── ACAnalysis.asc                            # Simulasi respon frekuensi (Bode plot)
    │   ├── NoiseAnalysis.asc                         # Simulasi kerapatan derau (noise density)
    │   └── TransientAnalysis.asc                     # Simulasi respon domain waktu / audio WAV
    │
    ├── filter_b/                 # Rangkaian & simulasi Filter B (Pernapasan Vesikular)
    │   ├── StageA_LowPassSallenKey_LT6233.asc/.asy   # Sub-circuit Sallen-Key LPF
    │   ├── StageB_HighPassSallenKey_LT6233.asc/.asy  # Sub-circuit Sallen-Key HPF
    │   ├── ACAnalysis.asc                            # Simulasi respon frekuensi
    │   ├── NoiseAnalysis.asc                         # Simulasi kerapatan derau
    │   └── TransientAnalysis.asc                     # Simulasi respon domain waktu
    │
    ├── filter_c/                 # Rangkaian & simulasi Filter C (Suara Napas Abnormal)
    │   ├── StageA_BandPassDeliyannisFriendNoPosFB_LT6233.asc/.asy   # Sub-circuit Deliyannis-Friend Stage A
    │   ├── StageB_BandPassDeliyannisFriendNoPosFB_LT6233.asc/.asy   # Sub-circuit Deliyannis-Friend Stage B
    │   ├── ACAnalysis.asc                            # Simulasi respon frekuensi
    │   ├── NoiseAnalysis.asc                         # Simulasi kerapatan derau
    │   └── TransientAnalysis.asc                     # Simulasi respon domain waktu
    │
    └── dataset/                  # Dataset acuan suara jantung & paru (HLS-CMDS)
        ├── heart_dataset/        # Rekaman suara jantung (normal, murmur, S3, S4, dll.)
        ├── lung_dataset/         # Rekaman suara paru-paru (vesikular, wheeze, crackle)
        └── Mix/                  # Rekaman campuran suara jantung & paru-paru
```

---

## ⚡ Panduan Menjalankan Simulasi di LTspice

1. **Persiapan:**
   - Pastikan LTspice telah terinstal (LTspice XVII / LTspice 24).
   - Lakukan pembaruan model komponen melalui menu `Tools` -> `Sync Release` agar komponen terbaru (seperti LT6233) terpasang.
   - Pastikan file simbol (`.asy`) dan skematik sub-sirkuit (`.asc`) berada pada folder yang sama dengan skematik analisis utama.

2. **Jenis Simulasi yang Tersedia:**
   - **AC Analysis (`ACAnalysis.asc`):**
     Menganalisis respon frekuensi (amplitudo dan fasa / Bode plot) dari filter untuk memverifikasi pita lolos (*passband*), frekuensi cut-off, dan atenuasi pita henti (*stopband*).
   - **Noise Analysis (`NoiseAnalysis.asc`):**
     Mengevaluasi derau masukan/keluaran (*input/output referred noise*) dari rangkaian op-amp.
   - **Transient Analysis (`TransientAnalysis.asc`):**
     Menguji filter pada domain waktu. Dapat menggunakan sumber sinyal sinus/pulsa maupun membaca file audio nyata (`.wav`) melalui perintah `.wave` atau generator tegangan wavefile untuk mendengar hasil filtrasi audio.

---

## 📚 Referensi Dataset

Dataset yang digunakan untuk pengujian audio bersumber dari:
> Y. Torabi, S. Shirani, and J. P. Reilly, *"Descriptor: Heart and Lung Sounds Dataset Recorded from a Clinical Manikin using Digital Stethoscope (HLS-CMDS),"* IEEE Data Descriptions, 2025. [DOI: 10.1109/IEEEDATA.2025.3566012](https://doi.org/10.1109/IEEEDATA.2025.3566012).
