# 🌿 Preprocessing Dataset Klasifikasi Kondisi Daun

Pipeline preprocessing dataset gambar untuk klasifikasi kondisi daun — **daun_bagus** vs **daun_layu** — menggunakan Python dan PIL.

---

## 📋 Deskripsi

Notebook ini mengimplementasikan pipeline preprocessing lengkap yang mencakup cleaning, standarisasi format, dan augmentasi data untuk keperluan training model klasifikasi gambar.

**Dataset awal:** 128 gambar (64 per kelas)  
**Dataset akhir:** 728 gambar setelah augmentasi (392 daun_bagus · 336 daun_layu)

---

## 🗂️ Struktur Folder

```
├── Preprocessing_Dataset_Daun.ipynb   # Notebook utama
├── dataset_processed/                 # Output setelah cleaning
│   ├── daun_bagus/
│   └── daun_layu/
├── dataset_augmented/                 # Output setelah augmentasi
│   ├── daun_bagus/
│   └── daun_layu/
└── preprocessing_report.png          # Laporan visual
```

---

## ⚙️ Pipeline

| Step | Tahap | Deskripsi |
|------|-------|-----------|
| 1 | **Setup** | Mount Google Drive, konfigurasi parameter |
| 2 | **Analisis Awal** | Hitung jumlah gambar, cek keseimbangan kelas |
| 3 | **Deteksi Duplikat** | MD5 hash (persis) + perceptual hash (hampir sama) |
| 4 | **Filter Kualitas** | Hapus gambar blur, gelap, terang, rusak |
| 5 | **Standarisasi** | Resize 224×224, RGB, normalisasi \[0,1\] |
| 6 | **Augmentasi** | 6× per gambar dengan 8 teknik |
| 7 | **Laporan Visual** | Grafik statistik hasil preprocessing |

---

## 🔧 Parameter Konfigurasi

```python
# Standarisasi
TARGET_SIZE    = (224, 224)
COLOR_MODE     = "RGB"
NORM_RANGE     = (0, 1)

# Threshold kualitas
BLUR_THRESHOLD   = 5.0    # Laplacian variance
DARK_THRESHOLD   = 10.0   # Mean brightness minimum
BRIGHT_THRESHOLD = 240.0  # Mean brightness maksimum
MIN_FILE_SIZE_KB = 2

# Augmentasi
AUG_PER_IMAGE    = 6
ROTATION_RANGE   = 20     # derajat
ZOOM_RANGE       = 0.15
BRIGHTNESS_RANGE = (0.7, 1.3)
CONTRAST_RANGE   = (0.7, 1.3)
CROP_FRACTION    = 0.1
```

---

## 📊 Hasil

| Kelas | Awal | Dihapus | Setelah Cleaning | Setelah Augmentasi |
|-------|------|---------|------------------|--------------------|
| daun_bagus | 64 | 8 (blur) | 56 | 392 |
| daun_layu | 64 | 16 (blur + gelap) | 48 | 336 |
| **Total** | **128** | **24** | **104** | **728** |

---

## 🔄 Teknik Augmentasi

- Rotasi ±20°
- Horizontal flip (50%)
- Vertical flip (25%)
- Zoom ±15%
- Crop tepi 10%
- Brightness 0.7–1.3×
- Contrast 0.7–1.3×
- Sharpness 0.5–2.0× (25%)

---

## 🚀 Cara Penggunaan

1. Upload dataset ke Google Drive dengan struktur:
   ```
   Dataset_redi/
   ├── daun_bagus/
   └── daun_layu/
   ```

2. Buka notebook di Google Colab

3. Sesuaikan `DATASET_DIR` di cell konfigurasi:
   ```python
   DATASET_DIR = "/content/drive/MyDrive/nama_folder_kamu"
   ```

4. Jalankan semua cell secara berurutan (`Runtime → Run all`)

---

## 📦 Dependencies

```
Pillow
numpy
scipy
matplotlib
```

Semua sudah tersedia di Google Colab tanpa instalasi tambahan.

---

## 📌 Catatan

- Augmentasi **hanya diterapkan pada training set** — validasi set menggunakan gambar asli
- Threshold blur diturunkan dari default 50.0 → **5.0** karena tekstur daun secara natural memiliki Laplacian variance rendah
- Disarankan split data **80% train / 20% val** dengan `stratify=y`
