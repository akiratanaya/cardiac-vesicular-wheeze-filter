# Cardiac, Vesicular & Wheeze Acoustic Filter Design

Proyek ini memuat perancangan dan simulasi rangkaian filter analog aktif (*active bandpass filter*) berbasis op-amp **LT6233** (dual-supply $\pm 5\text{V}$) untuk memisahkan komponen akustik auskultasi stetoskop digital menjadi tiga kanal frekuensi:
1. **Filter A (Cardiac):** Suara detak jantung normal dan murmur (20 Hz – 250 Hz).
2. **Filter B (Vesikular):** Suara napas normal paru-paru (20 Hz – 150 Hz).
3. **Filter C (Wheeze & Crackle):** Suara pernapasan abnormal seperti mengi dan ronki (200 Hz – 1000 Hz).

Sintesis nilai awal komponen diturunkan menggunakan bantuan **Analog Devices Filter Wizard (v1.3)** dengan optimasi *low noise* pada deret resistor/kapasitor standar (E12/E24), kemudian diverifikasi perilakunya pada domain frekuensi, waktu, dan derau menggunakan **LTspice**.

---

## 1. Spesifikasi Teknis Filter

| Parameter | Filter A (Jantung) | Filter B (Vesikular) | Filter C (Abnormal / Wheeze) |
| :--- | :--- | :--- | :--- |
| **Kategori Suara** | Lub-dub ($S_1, S_2$), murmur, gallop | Suara aliran udara bronkial/vesikular normal | Wheezing (mengi), crackles (ronki) |
| **Topologi Rangkaian** | Kaskade Sallen-Key Orde 4 (HPF + LPF) | Kaskade Sallen-Key Orde 4 (HPF + LPF) | 2-Stage Deliyannis-Friend Bandpass (MFB) |
| **Rentang Lolos (Passband)** | 20 Hz – 250 Hz | 20 Hz – 150 Hz | 200 Hz – 1000 Hz |
| **Frekuensi Karakteristik ($f_c$)** | ~71 Hz | ~55 Hz | ~447 Hz |
| **Gain Passband** | 0 dB | 0 dB | ~0 dB |
| **Atenuasi Stopband** | -40 dB @ 3 kHz | -40 dB @ 3 kHz | -40 dB @ 12 kHz |
| **Op-Amp Digunakan** | LT6233 ($GBW = 60\text{ MHz}$, $e_n = 1.9\text{ nV}/\sqrt{\text{Hz}}$) | LT6233 | LT6233 |
| **Tegangan Kerja** | $V_p = +5\text{V}$, $V_n = -5\text{V}$, $V_{ref} = 0\text{V}$ | $V_p = +5\text{V}$, $V_n = -5\text{V}$, $V_{ref} = 0\text{V}$ | $V_p = +5\text{V}$, $V_n = -5\text{V}$, $V_{ref} = 0\text{V}$ |

---

## 2. Rincian Rangkaian dan Nilai Komponen

### Filter A – Suara Jantung (20 Hz – 250 Hz)
Kaskade dua tingkat Sallen-Key orde 2:
- **Stage B (HPF Sallen-Key Orde 2):** Menahan komponen DC dan artefak gerakan transduser di bawah 20 Hz.
  - $R_1 = 6.8\text{ k}\Omega$, $R_2 = 15\text{ k}\Omega$
  - $C_1 = 820\text{ nF}$, $C_2 = 820\text{ nF}$
- **Stage A (LPF Sallen-Key Orde 2):** Membatasi batas atas pita jantung di 250 Hz.
  - $R_1 = 5.6\text{ k}\Omega$, $R_2 = 12\text{ k}\Omega$
  - $C_1 = 100\text{ nF}$, $C_2 = 43\text{ nF}$

### Filter B – Suara Napas Vesikular (20 Hz – 150 Hz)
Struktur identik dengan Filter A, tetapi cut-off LPF digeser lebih rendah untuk mengisolasi komponen frekuensi rendah napas normal:
- **Stage B (HPF Sallen-Key Orde 2):**
  - $R_1 = 6.8\text{ k}\Omega$, $R_2 = 13\text{ k}\Omega$
  - $C_1 = 1\,\mu\text{F}$, $C_2 = 1\,\mu\text{F}$
- **Stage A (LPF Sallen-Key Orde 2):**
  - $R_1 = 10\text{ k}\Omega$, $R_2 = 22\text{ k}\Omega$
  - $C_1 = 100\text{ nF}$, $C_2 = 47\text{ nF}$

### Filter C – Suara Napas Abnormal (200 Hz – 1000 Hz)
Berbeda dari Filter A dan B, Filter C menggunakan topologi **Deliyannis-Friend Bandpass tanpa positive feedback** (*Multiple Feedback / MFB Bandpass*). 

Kaskade terdiri dari dua stage resonansi terpisah (*stagger-tuned bandpass*):
- **Stage B (Lower Resonance):**
  - $R_1 = 3.9\text{ k}\Omega$, $R_5 = 12\text{ k}\Omega$
  - $C_1 = 100\text{ nF}$, $C_2 = 150\text{ nF}$
  - Resonansi alami: $f_{0B} \approx 190\text{ Hz}$
- **Stage A (Upper Resonance):**
  - $R_1 = 680\,\Omega$, $R_5 = 2.2\text{ k}\Omega$
  - $C_1 = 100\text{ nF}$, $C_2 = 150\text{ nF}$
  - Resonansi alami: $f_{0A} \approx 1062\text{ Hz}$

Kombinasi kedua stage membentuk respon *flat-top bandpass* orde 4 dengan frekuensi pusat geometrik $\sqrt{190 \times 1062} \approx 449\text{ Hz}$ dan batas $-3\text{ dB}$ pada rentang ~175 Hz hingga ~1180 Hz, menutup target 200 Hz – 1000 Hz secara presisi.

#### Alasan Pemilihan Deliyannis-Friend / MFB untuk Filter C
1. **Sensitivitas Komponen:** Pada rasio frekuensi batas atas dan bawah yang relatif dekat (faktor $Q$ pita lolos sedang), kaskade HPF dan LPF terpisah cenderung menunjukkan atenuasi pasband berlebih di titik potong. Topologi MFB menjaga gain pasband tetap stabil di sekitar 0 dB.
2. **Karakteristik Roll-Off Simetris:** Penurunan di stopband sisi rendah (<200 Hz) dan sisi tinggi (>1 kHz) berlangsung dengan kemiringan yang seimbang (-40 dB/dekade per sisi).
3. **Resistensi terhadap Derau:** Topologi MFB inverting membatasi pengaruh derau mode bersama (*common-mode noise*) dari sensor stetoskop.

---

## 3. Catatan Teknis Simulasi & Troubleshooting

### Penyelesaian Bug Parameter `{R2}` pada Filter C
Pada saat skematik hasil ekspor Analog Devices Filter Wizard dijalankan pertama kali di LTspice, simulasi AC Analysis berhenti dengan pesan galat:

```text
ACAnalysis.net(16): No such parameter defined.
R2 N001 REF {R2}
            ^^
```

**Analisis Masalah:**
- Pada topologi Deliyannis-Friend / MFB umum, resistor shunt $R_2$ dari summing node ke ground digunakan untuk menggeser frekuensi resonansi $f_0$ ke atas tanpa mengubah nilai penguatan.
- Namun untuk konfigurasi Filter C (di mana rasio komponen dan nilai $Q$ yang dibutuhkan sudah terpenuhi langsung oleh kombinasi $R_1$, $R_5$, $C_1$, dan $C_2$), nilai teoritis $R_2$ bernilai tak terhingga ($\infty$) alias **tidak dipasang (Do Not Populate / open-circuit)**.
- Generator ekspor Filter Wizard gagal mengeliminasi simbol komponen tersebut dan meninggalkan placeholder teks `{R2}` tanpa baris `.param R2=...`. LTspice menganggapnya sebagai variabel yang hilang.

**Solusi:**
Resistor $R_2$ pada subcircuit `StageA_BandPassDeliyannisFriendNoPosFB_LT6233.asc` dan `StageB_BandPassDeliyannisFriendNoPosFB_LT6233.asc` dihapus dari skematik. Setelah jalur open-circuit tersebut dirapikan, netlist terkompilasi bersih dan simulasi berjalan sesuai respon teoritis.

---

## 4. Panduan Menjalankan Simulasi di LTspice

Setiap folder filter (`filter_a`, `filter_b`, `filter_c`) memiliki tiga jenis file simulasi siap pakai:

1. **AC Analysis (`ACAnalysis.asc`):**
   - Perintah: `.AC DEC 100 <f_start> <f_stop>`
   - Menghasilkan kurva respon magnitudo dan fasa (Bode plot) untuk memverifikasi pita passband dan atenuasi stopband.
   - Node pantau utama: `V(IN)`, `V(OUT1)` (keluaran stage 1), dan `V(OUT)` (keluaran akhir).

2. **Transient Analysis (`TransientAnalysis.asc`):**
   - Menguji respon waktu rangkaian. Tersedia mode uji respon tangga/pulsa (`PULSE`) maupun sinyal audio riil (`.wave`).
   - Untuk memproses audio hasil rekaman stetoskop:
     1. Masukkan file `.wav` (dari folder `dataset/`) ke direktori simulasi.
     2. Konfigurasikan sumber tegangan `VIN` menggunakan: `wavefile="nama_file.wav" chan=0`.
     3. Tambahkan direktif ekspor audio: `.wave "output_filtered.wav" 16 44.1k V(OUT)`.

3. **Noise Analysis (`NoiseAnalysis.asc`):**
   - Perintah: `.NOISE V(OUT) VNOISE DEC 100 <f_start> <f_stop>`
   - Mengukur kerapatan derau masukan dan keluaran (*input/output referred noise density* in $\text{V}/\sqrt{\text{Hz}}$) serta derau terintegrasi (*total RMS noise*).

> **Catatan:** Pastikan model op-amp `LT6233` tersedia pada instalasi LTspice Anda. Jalankan menu `Tools -> Sync Release` jika komponen tidak dikenali.

---

## 5. Struktur Direktori

```text
cardiac-vesicular-wheeze-filter/
├── .gitignore
├── README.md
│
├── wizard_config/                     # File konfigurasi sintesis ADI Filter Wizard
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
└── dataset/                           # Dataset sinyal auskultasi stetoskop (HLS-CMDS)
    ├── heart_dataset/
    ├── lung_dataset/
    └── Mix/
```

---

## 6. Referensi Dataset

Data audio untuk pengujian transien bersumber dari:
> Y. Torabi, S. Shirani, and J. P. Reilly, *"Descriptor: Heart and Lung Sounds Dataset Recorded from a Clinical Manikin using Digital Stethoscope (HLS-CMDS),"* IEEE Data Descriptions, 2025. [DOI: 10.1109/IEEEDATA.2025.3566012](https://doi.org/10.1109/IEEEDATA.2025.3566012).
