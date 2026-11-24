
# Logbook - CPMK 3: Instance Segmentation

## Informasi Proyek

- **Mata Kuliah:** Visi Komputer
- **Tugas:** Segmentasi Instance pada Lingkungan Indoor Nyata
- **Dataset:** Panoptic Indoor Segmentation v5
- **Dataset URL:** https://universe.roboflow.com/panopticindoor/panoptic-indoor-segmentation/dataset/5
- **Model:** YOLO11n-seg, YOLOv8n-seg, Mask R-CNN
- **Variabel Eksperimen:** Ukuran input (320, 640, 960)
- **Digit Terakhir NRP:** 1
- **Environment:** Google Colab (GPU)

---

## 1. Hipotesis Eksperimen

**Tanggal:** 18 November 2026

**Status:** Hipotesis sebelum training

### Hipotesis

Saya memperkirakan Mask R-CNN yang akan menghasilkan performa instance segmentation yang paling baik diantara YOLO11n-seg dan YOLOv8n-seg, dikarenakan Mask R-CNN menggunakan arsitektur two-stage, yang melakukan deteksi region objek sebelum menghasilkan mask segmentasi.

Kelas dengan ukuran kecil, bentuk kompleks, atau mengalami occlusion akibat tertutup objek lain, saya perkirakan menjadi kelas yg sulit disegmentasi.

Berdasarkan eksperimen digit terakhir NRP, ukuran input 960 × 960 saya perkirakan bisa menghasilkan performa segmentasi yang paling baik dibandingkan 320 × 320 dan 640 × 640 karena bisa mempertahankan lebih banyak detail gambar. tapi sepertinya ukuran input yang tinggi membutuhkan waktu komputasi dan memori GPU lebih tinggi.

### Alasan Hipotesis

1. Mask R-CNN menggunakan pendekatan two-stage dengan prediksi mask pada setiap region objek.
2. YOLO menggunakan pendekatan one-stage yang dirancang untuk melakukan prediksi secara efisien.
3. Objek kecil dan objek yang saling menutupi berpotensi lebih sulit disegmentasi.
4. Resolusi input yang lebih tinggi berpotensi mempertahankan detail objek, tetapi meningkatkan kebutuhan komputasi.

---

## 2. Logbook Aktivitas

### Aktivitas 1 - Persiapan Dataset

**Tanggal:** 17 November 2026

**Aktivitas:**
- Mengunduh dataset Panoptic Indoor Segmentation v5 melalui Roboflow.
- Mengunduh dataset dalam format YOLOv8 Segmentation.
- Mengunduh dataset dalam format COCO Segmentation untuk Mask R-CNN.
- Memeriksa jumlah gambar dan anotasi.
- Memvisualisasikan ground truth instance segmentation.

**Hasil:**
- Training set: 397 gambar.
- Validation set: 166 gambar.
- Test set: 359 gambar.
- Jumlah kelas: 74.
- Anotasi berbentuk polygon/mask berhasil divisualisasikan.

**Kendala:** Tidak ada

**Keputusan:** Menggunakan dataset versi 5 yang sama untuk seluruh model, dengan format anotasi sesuai kebutuhan masing-masing arsitektur.

---

### Aktivitas 2 - Training YOLO11n-seg

**Tanggal:** 18 November 2026

**Aktivitas:**
- Melakukan training YOLO11n-seg menggunakan pretrained weights.
- Menggunakan tiga ukuran input: 320, 640, dan 960.
- Menetapkan 30 epoch, batch size 8, dan seed 42 sebagai konfigurasi awal.
- Menyimpan checkpoint dan training log di Google Drive.

**Hasil Training:**

| Input Size | Epoch Selesai | Waktu GPU (menit) | Keterangan |
|------------|---------------|-------------------|------------|
| 320 | [ISI] | [ISI] | [ISI] |
| 640 | [ISI] | [ISI] | [ISI] |
| 960 | [ISI] | [ISI] | [ISI] |

**Kendala:** [ISI / Tidak ada]

**Keputusan:** [ISI KEPUTUSAN YANG BENAR-BENAR DILAKUKAN]

**Lokasi Log:** `results/YOLO11n_*/results.csv`

---

### Aktivitas 3 - Training YOLOv8n-seg

**Tanggal:** [ISI]

**Aktivitas:**
- Melakukan training YOLOv8n-seg menggunakan pretrained weights.
- Menggunakan ukuran input 320, 640, dan 960.
- Menggunakan konfigurasi awal 30 epoch, batch size 8, dan seed 42.
- Menyimpan checkpoint dan training log.

**Hasil Training:**

| Input Size | Epoch Selesai | Waktu GPU (menit) | Keterangan |
|------------|---------------|-------------------|------------|
| 320 | [ISI] | [ISI] | [ISI] |
| 640 | [ISI] | [ISI] | [ISI] |
| 960 | [ISI] | [ISI] | [ISI] |

**Kendala:** [ISI / Tidak ada]

**Keputusan:** [ISI]

**Lokasi Log:** `results/YOLOv8n_*/results.csv`

---

### Aktivitas 4 - Training Mask R-CNN

**Tanggal:** [ISI]

**Aktivitas:**
- Menggunakan dataset format COCO Segmentation.
- Membuat custom Dataset dan DataLoader menggunakan PyTorch.
- Menggunakan Mask R-CNN ResNet-50 FPN dengan pretrained weights.
- Mengganti classification head dan mask prediction head sesuai jumlah kelas.
- Melatih model dengan input 320, 640, dan 960.
- Menyimpan checkpoint serta training loss setiap epoch.

**Konfigurasi Awal:**
- Epoch maksimum: 30.
- Optimizer: AdamW.
- Learning rate: 0.0001.
- Batch size: 2 untuk 320 dan 640, serta 1 untuk 960.

**Hasil Training:**

| Input Size | Epoch Selesai | Waktu GPU (menit) | Keterangan |
|------------|---------------|-------------------|------------|
| 320 | [ISI] | [ISI] | [ISI] |
| 640 | [ISI] | [ISI] | [ISI] |
| 960 | [ISI] | [ISI] | [ISI] |

**Kendala:** [ISI / Tidak ada]

**Keputusan:** [ISI]

**Lokasi Log:** `results/MaskRCNN_*/results.csv`

---

### Aktivitas 5 - Pembuatan Test Set Sendiri

**Tanggal:** [ISI]

**Aktivitas:**
- Mengambil minimal 40 foto ruangan nyata menggunakan kamera HP.
- Mengambil minimal 10 foto dalam kondisi sulit.
- Melakukan anotasi instance segmentation melalui Roboflow.
- Menggunakan kelas yang sama dengan dataset utama.
- Menyimpan bukti anotasi serta metadata EXIF foto.

**Hasil:**
- Jumlah foto: [ISI].
- Foto kondisi sulit: [ISI].
- Jumlah foto selesai dianotasi: [ISI].

**Kendala:** [ISI]

**Keputusan:** [ISI]

**Bukti:** [ISI LOKASI SCREENSHOT DAN METADATA]

---

### Aktivitas 6 - Evaluasi Model

**Tanggal:** [ISI]

**Aktivitas:**
- Mengevaluasi setiap model pada test set asli.
- Mengevaluasi setiap model pada test set buatan sendiri.
- Menghitung metrik instance segmentation.
- Membandingkan pengaruh input size terhadap performa.
- Menganalisis domain shift.

**Metrik Evaluasi:**
- Mask mAP50.
- Mask mAP50-95.
- Precision dan recall (jika tersedia).
- Waktu inferensi.

**Hasil:** [ISI SETELAH EVALUASI]

**Kendala:** [ISI]

**Keputusan:** [ISI]

---

### Aktivitas 7 - Analisis Kegagalan

**Tanggal:** [ISI]

**Aktivitas:**
- Memilih 5 foto dari test set sendiri yang menunjukkan kegagalan model.
- Membandingkan ground truth dan prediksi.
- Mengidentifikasi kesalahan deteksi, klasifikasi, atau segmentasi.
- Menghubungkan kesalahan dengan mekanisme arsitektur model.
- Melakukan eksperimen kecil untuk menguji salah satu dugaan penyebab kegagalan.

**Hasil:** [ISI]

**Kendala:** [ISI]

**Keputusan:** [ISI]

---

## 3. Ringkasan Eksperimen Ukuran Input

| Model | Input Size | Mask mAP50 Test Asli | Mask mAP50 Test Sendiri |
|-------|------------|----------------------|-------------------------|
| YOLO11n-seg | 320 | [ISI] | [ISI] |
| YOLO11n-seg | 640 | [ISI] | [ISI] |
| YOLO11n-seg | 960 | [ISI] | [ISI] |
| YOLOv8n-seg | 320 | [ISI] | [ISI] |
| YOLOv8n-seg | 640 | [ISI] | [ISI] |
| YOLOv8n-seg | 960 | [ISI] | [ISI] |
| Mask R-CNN | 320 | [ISI] | [ISI] |
| Mask R-CNN | 640 | [ISI] | [ISI] |
| Mask R-CNN | 960 | [ISI] | [ISI] |

**Analisis:** [ISI SETELAH EVALUASI]

---

## 4. Deklarasi Penggunaan AI

**AI yang digunakan:** ChatGPT

**Tujuan penggunaan:**
- Membantu memahami instruksi tugas CPMK 3.
- Membantu menyusun kode download dan pemeriksaan dataset.
- Membantu membuat kode training YOLO dan Mask R-CNN.
- Membantu memahami konfigurasi model dan metrik evaluasi.
- Membantu menyusun dokumentasi logbook.

**Prompt utama:**
- "Berikan step step untuk mengerjakan tugas CPMK 3 dan jelaskan maksud tugasnya. Digit belakang NRP ku 1."
- "Formatnya apa?"
- "API key di mana?"
- "Oke sekarang lanjut ke YOLOv8."
- "Berarti logbook.md isinya gimana? Tolong berikan kodenya."

**Kasus saran AI yang salah/tidak cocok:**

- Saran awal AI: [ISI]
- Permasalahan yang ditemukan: [ISI]
- Bukti error atau ketidaksesuaian: [ISI]
- Perbaikan yang dilakukan: [ISI]
- Hasil setelah diperbaiki: [ISI]

---

## 5. Kesimpulan Sementara

Training tiga arsitektur/varian model telah dilakukan menggunakan ukuran input 320, 640, dan 960 sebagai variabel eksperimen.

Evaluasi selanjutnya akan digunakan untuk menentukan pengaruh ukuran input terhadap performa segmentasi, membandingkan hasil antar model, dan menganalisis domain shift antara test set asli dan test set buatan sendiri.

Kesimpulan akhir akan ditentukan berdasarkan hasil evaluasi kuantitatif dan analisis kegagalan.

---

## 6. Referensi Bukti

- Repository GitHub: [ISI URL]
- Log training: [ISI PATH]
- Checkpoint model: [ISI PATH]
- Screenshot anotasi: [ISI PATH]
- Metadata EXIF: [ISI PATH]
- Grafik evaluasi: [ISI PATH]
- Notebook demo: [ISI PATH]
