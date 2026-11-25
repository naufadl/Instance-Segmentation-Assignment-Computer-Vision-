# Logbook - CPMK 3: Instance Segmentation

## Informasi Proyek

- **Mata kuliah:** Visi Komputer
- **Tugas:** Segmentasi Instance pada Lingkungan Indoor Nyata
- **Dataset utama:** Panoptic Indoor Segmentation v5
- **URL dataset:** https://universe.roboflow.com/panopticindoor/panoptic-indoor-segmentation/dataset/5
- **Model:** YOLO11n-seg, YOLOv8n-seg, Mask R-CNN (ResNet-50 FPN)
- **Variabel NRP (digit akhir 1):** Ukuran input 320, 640, dan 960 piksel
- **Lingkungan:** Google Colab, Python, Ultralytics, PyTorch/Torchvision, Roboflow
- **GPU training yang terdeteksi di notebook:** Tesla T4 (14,56 GB)
- **Sumber angka:** Output tersimpan pada `VISKOM_Instance_Segmentation.ipynb`, terutama tabel `FINAL_RETEST40_all_results.csv` yang tercetak dalam notebook. Angka dibulatkan.

---

## 1. Hipotesis Eksperimen

**Tanggal yang dicatat:** 18 November 2026

### Hipotesis

Saya memperkirakan Mask R-CNN dapat memberikan hasil instance segmentation yang lebih baik dibandingkan YOLO11n-seg dan YOLOv8n-seg, terutama ketika objek saling menutupi. Mask R-CNN memakai pendekatan *two-stage*, yaitu mencari region objek terlebih dahulu sebelum menghasilkan mask.

Saya juga memperkirakan objek yang kecil, bentuknya rumit, atau tertutup sebagian oleh objek lain akan lebih sulit disegmentasi.

Untuk eksperimen ukuran input, saya memperkirakan 960 × 960 dapat menghasilkan segmentasi yang lebih baik daripada 320 × 320 dan 640 × 640 karena detail gambar lebih terjaga. Namun, ukuran yang lebih besar juga diperkirakan memerlukan memori GPU dan waktu komputasi lebih banyak.

**Evaluasi hipotesis setelah eksperimen:** Pada tabel hasil, konfigurasi dengan mAP50-95 tertinggi untuk test asli adalah YOLO11n-seg 640 (0,1057), sedangkan pada 40 foto sendiri adalah YOLOv8n-seg 640 (0,0748). Jadi, data yang tercatat tidak mendukung dugaan bahwa konfigurasi 960 selalu paling baik atau bahwa Mask R-CNN otomatis unggul.

---

## 2. Logbook Aktivitas

### Aktivitas 1 — Persiapan Dataset Utama

**Tanggal:** 17 November 2026.

**Aktivitas:**
- Mengunduh Panoptic Indoor Segmentation v5 melalui Roboflow dalam format YOLO segmentation dan COCO Segmentation.
- Memeriksa jumlah gambar, label, jumlah kelas, dan contoh polygon/mask.
- Mengaktifkan GPU Google Colab dan menyiapkan konfigurasi `data_colab.yaml`.

**Hasil:**
- Train: **397 gambar / 397 file label**.
- Validation: **166 gambar / 166 file label**.
- Test asli: **359 gambar / 359 file label**.
- Dataset YOLO: **74 kelas**.
- Visualisasi anotasi polygon berhasil ditampilkan.
- GPU terdeteksi: **Tesla T4, 14,56 GB**.

**Kendala:** Tidak ada.

**Keputusan:** Menggunakan dataset utama versi 5 yang sama untuk semua model, dengan format anotasi sesuai arsitektur.

### Aktivitas 2 — Training YOLO11n-seg

**Tanggal:** 18 November 2026

**Konfigurasi:** Pretrained `yolo11n-seg.pt`, 30 epoch, batch 8, seed 42, input 320/640/960, checkpoint dan log disimpan ke Google Drive.

| Input | Epoch | Durasi training (menit) | Hasil |
|---|---:|---:|---|
| 320 | 30 | 8,59 | Training selesai |
| 640 | 30 | 10,94 | Training selesai |
| 960 | 30 | 13,65 | Training selesai |

**Kendala:** Tidak ada.

**Keputusan:** Memulai masing-masing konfigurasi dari pretrained model yang sama agar perbedaan input dapat dibandingkan.

**Bukti:** `results/YOLO11n_320/`, `YOLO11n_640/`, `YOLO11n_960/` (termasuk `weights/best.pt`, `results.csv`).

### Aktivitas 3 — Training YOLOv8n-seg

**Tanggal:** 19 November 2026.

**Konfigurasi:** Pretrained `yolov8n-seg.pt`, 30 epoch, batch 8, seed 42, input 320/640/960.

| Input | Epoch | Durasi training (menit) | Hasil |
|---|---:|---:|---|
| 320 | 30 | 8,41 | Training selesai |
| 640 | 30 | 10,01 | Training selesai |
| 960 | 30 | 14,23 | Training selesai |

**Kendala:** Tidak ada.

**Keputusan:** Menggunakan format YOLO segmentation yang sama dengan YOLO11 untuk konsistensi data.

**Bukti:** `CPMK3/results/YOLOv8n_320/`, `YOLOv8n_640/`, `YOLOv8n_960/`.

### Aktivitas 4 — Training Mask R-CNN

**Tanggal:** 22 November 2026.

**Aktivitas:**
- Mengunduh dataset COCO Segmentation.
- Membuat `IndoorCOCODataset`, `ResizeSegmentationDataset`, dan `DataLoader` PyTorch.
- Menggunakan Mask R-CNN ResNet-50 FPN pretrained, mengganti kepala klasifikasi dan prediksi mask, lalu melatih dengan AdamW (learning rate 0,0001, weight decay 0,0005).
- Melatih dengan 30 epoch. Batch 2 pada 320/640 dan batch 1 pada 960.
- Menggunakan resize dengan rasio aspek dipertahankan dan padding ke ukuran persegi.

| Input | Epoch | Durasi training (menit) | Hasil |
|---|---:|---:|---|
| 320 | 30 | 32,27 | Training selesai |
| 640 | 30 | 47,71 | Training selesai |
| 960 | 30 | 77,95 | Training selesai |

**Keputusan:** Menyimpan `last.pth` dan `results.csv` setelah training.

### Aktivitas 5 — Pembuatan Test Set Sendiri

**Aktivitas:** Mengambil foto indoor menggunakan HP, melakukan anotasi sendiri di Roboflow, mengekspor COCO Segmentation dan YOLO segmentation, kemudian menyesuaikan ID kelas custom dengan dataset training.

**Hasil dari notebook:**
- **40 gambar** pada test set sendiri.
- **219 anotasi objek** pada COCO custom.
- COCO export memuat **20 entri kategori**, termasuk kategori tambahan `anotasi-viskom` (ID 0) yang tidak masuk pemetaan objek; terdapat **19 kelas objek yang cocok** dengan dataset utama.
- YOLO export custom benar berisi **40 gambar, 40 file label, 19 kelas**, kemudian kelas/ID label dipetakan ke konfigurasi 74 kelas untuk evaluasi YOLO.

**Kondisi sulit:** 

**EXIF dan screenshot anotasi:** 

**Kendala:** Folder export dan variabel path sempat tertukar sehingga folder yang dianggap custom menampilkan **359 gambar/74 kelas** dari dataset asli, bukan 40 foto custom.

**Perbaikan/keputusan:** Memeriksa sumber dataset dengan jumlah gambar dan label; memakai export custom yang benar dan membuat konversi ID kelas, lalu mengevaluasi ulang enam konfigurasi YOLO pada 40 foto (RETEST40). Tidak melakukan training ulang.

### Aktivitas 6 — Evaluasi pada Dua Test Set

**Aktivitas:** Evaluasi 9 konfigurasi pada test asli (359 gambar) dan test sendiri (40 gambar); menyimpan metrik mask mAP50 dan mAP50-95. Hasil custom YOLO yang pernah tercampur **tidak dipakai** untuk ringkasan final.

**Sumber angka final:** Output cell `FINAL_RETEST40_all_results.csv`, memakai folder retest YOLO `RETEST40_verified/20261125_003507_881f71` serta hasil Mask R-CNN dan test asli yang tercatat.

| Model | Input | mAP50 asli | mAP50 custom | mAP50-95 asli | mAP50-95 custom |
|---|---:|---:|---:|---:|---:|
| YOLO11n-seg | 320 | 0,1161 | 0,1279 | 0,0649 | 0,0678 |
| YOLO11n-seg | 640 | 0,1640 | 0,1048 | 0,1057 | 0,0571 |
| YOLO11n-seg | 960 | 0,1456 | 0,0838 | 0,0898 | 0,0531 |
| YOLOv8n-seg | 320 | 0,1028 | 0,1019 | 0,0562 | 0,0559 |
| YOLOv8n-seg | 640 | 0,1342 | 0,1334 | 0,0823 | 0,0748 |
| YOLOv8n-seg | 960 | 0,1192 | 0,1012 | 0,0719 | 0,0643 |
| Mask R-CNN | 320 | 0,0978 | 0,1044 | 0,0520 | 0,0686 |
| Mask R-CNN | 640 | 0,1047 | 0,1027 | 0,0592 | 0,0667 |
| Mask R-CNN | 960 | 0,0896 | 0,0889 | 0,0514 | 0,0615 |

**Ringkasan:**
- Skor mAP50-95 tertinggi pada test asli dalam tabel: **YOLO11n-seg 640 = 0,1057**.
- Skor mAP50-95 tertinggi pada test custom dalam tabel: **YOLOv8n-seg 640 = 0,0748**.
- Ukuran 960 **tidak** menjadi konfigurasi terbaik pada kedua test set.

### Aktivitas 7 — Analisis Domain Shift

**Aktivitas:** Menghitung selisih `mAP50-95 custom − mAP50-95 original`, menyimpan grafik perbandingan ukuran input dan grafik selisih.

| Model | Input | Gap mAP50-95 (custom − original) |
|---|---:|---:|
| YOLO11n-seg | 320 | +0,0029 |
| YOLO11n-seg | 640 | −0,0486 |
| YOLO11n-seg | 960 | −0,0366 |
| YOLOv8n-seg | 320 | −0,0003 |
| YOLOv8n-seg | 640 | −0,0075 |
| YOLOv8n-seg | 960 | −0,0076 |
| Mask R-CNN | 320 | +0,0165 |
| Mask R-CNN | 640 | +0,0076 |
| Mask R-CNN | 960 | +0,0101 |

**Interpretasi:** Selisih berbeda-beda antar model dan ukuran. Penurunan cukup besar terlihat pada YOLO11n-seg 640 dan 960, tetapi Mask R-CNN pada tabel memiliki gap positif. Hal ini **tidak cukup untuk menyimpulkan domain shift sebagai satu-satunya penyebab**, karena test custom berisi 19 kelas objek yang cocok sedangkan dataset utama mencakup lebih banyak kelas dan evaluator antararsitektur berbeda.

### Aktivitas 8 — Analisis Lima Foto Kegagalan

**Model yang digunakan untuk visualisasi:** YOLO11n-seg checkpoint 640, confidence threshold 0,25.

**Foto yang dipilih:** Nomor **7, 29, 34, 37, 39** dari daftar 40 foto yang telah diurutkan; hasil disimpan sebagai `FAILURE_07.png`, `FAILURE_29.png`, `FAILURE_34.png`, `FAILURE_37.png`, dan `FAILURE_39.png`.

| Nomor foto | Temuan visual | Dugaan penyebab (belum terbukti) | Saran pemeriksaan/perbaikan |
|---|---|---|---|
| 7 | Sebagian kursi tidak dikenali/terpisah dengan baik | Kursi berdekatan, saling menutupi, dan detail rangka tipis | Bandingkan hasil antarukuran input dan mask per instance |
| 29 | Meja/objek pada meja salah dikenali | Penutup kain serta bentuk/tekstur objek berbeda dari contoh pelatihan | Tinjau kelas ground truth dan bandingkan prediksi pada berbagai ukuran |
| 34 | Tanaman/pot tidak dikenali sesuai ground truth | Daun atau objek bertumpuk dan bentuk tanaman kompleks | Bandingkan prediksi per resolusi, cek variasi data tanaman |
| 37 | Remote tidak dikenali sebagai `remote` | Objek relatif kecil; bentuk mirip ponsel/komponen dinding | Eksperimen inferensi 320/640/960 dengan checkpoint sama |
| 39 | Kotak penyimpanan tidak dikenali sesuai ground truth | Sudut kamera, beberapa kotak berdampingan, serta konteks latar | Bandingkan prediksi per resolusi, pertimbangkan data variasi sudut |

**Catatan:** Dugaan penyebab adalah interpretasi dari visualisasi kegagalan.

### Aktivitas 9 — Eksperimen Kecil: Foto Remote (Perbaikan Eksperimen)

**Pengaturan eksperimen:** Checkpoint **YOLO11n-seg 640** yang sama, confidence **0,25**, ukuran inferensi **320, 640, dan 960**; bobot model tidak diubah.

| Ukuran inferensi | Prediksi yang tercatat dan confidence | Remote benar? |
|---|---|---|
| 320 | `wall-other-merged` 0,667; `plug` 0,301; `plug` 0,254 | Tidak |
| 640 | `wall-other-merged` 0,420; `cell phone` 0,263 | Tidak |
| 960 | `plug` 0,690 | Tidak |

**Hasil dan kesimpulan:** Tidak muncul prediksi `remote` yang benar pada ketiga ukuran. Perubahan ukuran inferensi mengubah kelas/confidence yang terdeteksi, tetapi **tidak memperbaiki deteksi remote pada foto ini**. Karena itu, dugaan bahwa resolusi inferensi lebih tinggi saja sudah cukup **tidak didukung**.

---

## 3. Ringkasan Hasil Eksperimen

Berdasarkan output `FINAL_RETEST40`, konfigurasi dengan skor mAP50-95 tertinggi pada kedua test set sama-sama memiliki ukuran input **640**, tetapi modelnya berbeda: YOLO11n-seg untuk test asli dan YOLOv8n-seg untuk foto sendiri. Dengan demikian, **hipotesis bahwa 960 akan paling baik tidak sesuai dengan hasil yang tercatat**. Durasi training juga umumnya meningkat seiring ukuran input.

---

## 4. Deklarasi Penggunaan AI

**AI yang digunakan:** ChatGPT.

**Penggunaan:** Membantu memahami instruksi CPMK 3, membuat pipeline training/evaluasi YOLO dan Mask R-CNN, konversi ID kelas, dan membuat template logbook.md.

**prompt:**
- “jelaskan aku tugas ini dan apa yang harus aku lakukan”
- “karna kemaren hasilnya kecampur, gabungkan test semua yolo.”

### Kasus saran AI yang tidak sesuai

**Masalah 1 — Dataset custom tertukar.** Kode yang diberikan sebelumnya memakai variabel/path yang tidak konsisten. Akibatnya folder yang dianggap berisi 40 foto custom pernah berisi **359 gambar dan 74 kelas** dari dataset asli. Masalah teridentifikasi saat melihat foto yang bukan milik sendiri dan mengecek jumlah gambar.

**Perbaikan:** Memeriksa path, jumlah gambar, dan ID kelas secara eksplisit; memakai export 40 foto yang benar; lalu mengulang enam evaluasi YOLO custom sebagai `RETEST40`. Tabel final mengambil hasil retest, bukan CSV lama.

**Masalah 2 — Eksperimen memakai kolase, bukan foto mentah.** Saran awal menggunakan `FAILURE_37.png` untuk inferensi. Gambar tersebut merupakan hasil gabungan Original–Ground Truth–Prediction.

**Perbaikan:** Mengambil foto asli nomor 37 dan mengulang inferensi 320/640/960 dengan checkpoint sama.
