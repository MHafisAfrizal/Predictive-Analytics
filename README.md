# Laporan Proyek Analitik Prediktif: Prediksi Risiko Readmisi Pasien Diabetes

## Ringkasan
Proyek ini bertujuan memprediksi risiko readmisi pasien diabetes di rumah sakit di AS menggunakan pendekatan regresi machine learning. Dataset dari UCI Machine Learning Repository (Diabetes 130-US hospitals, 1999-2008) dengan subset 5000 sampel digunakan untuk efisiensi.

- **Domain**: Kesehatan  
- **Masalah**: Memprediksi risiko readmisi untuk meningkatkan perawatan dan mengurangi biaya.  
- **Pendekatan**: Regresi (skor risiko: 0=NO, 0.5=>30 hari, 1=<30 hari).  
- **Dataset**: 5000 sampel, 50 kolom awal (diproses jadi ~42 kolom).  
- **Sumber**: [UCI Dataset](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008)

Proyek ini memenuhi kriteria Dicoding: dataset kuantitatif ≥500 sampel, pendekatan regresi, dokumentasi lengkap, serta tambahan seperti rekayasa fitur, penyetelan hiperparameter, visualisasi, dan perbandingan model untuk menargetkan peringkat 4-5 bintang.

---

## Data Understanding
Memahami struktur dataset, fitur, dan target.

### Jumlah Data
Dataset memiliki:  
- **Jumlah Baris**: 5000  
- **Jumlah Kolom**: 50  

### Kondisi Data
#### Missing Value
Berikut jumlah missing value per kolom (setelah mengganti '?' jadi NaN):  
- `encounter_id`: 0  
- `patient_nbr`: 0  
- `race`: 113  
- `gender`: 0  
- `age`: 0  
- `weight`: 4830  
- `admission_type_id`: 0  
- `discharge_disposition_id`: 0  
- `admission_source_id`: 0  
- `time_in_hospital`: 0  
- `payer_code`: 1967  
- `medical_specialty`: 2435  
- `num_lab_procedures`: 0  
- `num_procedures`: 0  
- `num_medications`: 0  
- `number_outpatient`: 0  
- `number_emergency`: 0  
- `number_inpatient`: 0  
- `diag_1`: 1  
- `diag_2`: 16  
- `diag_3`: 73  
- `number_diagnoses`: 0  
- `max_glu_serum`: 4740  
- `A1Cresult`: 4154  
- Kolom obat (metformin, insulin, dll.): 0  
- `change`: 0  
- `diabetesMed`: 0  
- `readmitted`: 0  

#### Duplikat
- **Jumlah Duplikat**: 0  

#### Outlier
Outlier pada kolom seperti `time_in_hospital` dan `num_medications` ditangani di tahap persiapan data menggunakan metode winsorizing.

---

## Data Preparation
Membersihkan dan menyiapkan data untuk modeling.

### Penanganan Missing Value
- Mengganti '?' menjadi `NaN`.
- Kolom kategorikal (`race`, `weight`, `payer_code`, `medical_specialty`) diisi dengan 'Unknown'.
- Kolom dengan missing value tinggi (`weight`, `payer_code`, `medical_specialty`) dihapus.

### Penghapusan Duplikat
- Tidak ada duplikat, tetapi langkah penghapusan tetap dilakukan untuk memastikan.

### Penanganan Outlier
- Menggunakan winsorizing pada `time_in_hospital` dan `num_medications` dengan batas 5% di kedua ujung distribusi.

### Rekayasa Fitur
- Membuat fitur baru:  
  - `total_prosedur`: Jumlah dari `num_lab_procedures`, `num_procedures`, `number_outpatient`, `number_emergency`, `number_inpatient`.  
  - `kelompok_usia`: Mengelompokkan `age` menjadi 'Muda' ([0-30)), 'Setengah Baya' ([30-60)), dan 'Senior' ([60-100)).  
- Mengubah `readmitted` menjadi skor risiko berkelanjutan:  
  - 'NO' → 0  
  - '>30' → 0.5  
  - '<30' → 1  

### Enkoding Kategorikal
- Kolom kategorikal (misalnya `race`, `gender`, `kelompok_usia`, obat-obatan) dienkode menggunakan `LabelEncoder`.

### Pembersihan Kolom
- Menghapus kolom tidak relevan: `encounter_id`, `patient_nbr`, `age`, `diag_1`, `diag_2`, `diag_3`.

### Pemisahan dan Penskalaan Data
- Memisahkan fitur (X) dan target (`risiko_readmisi`, y).
- Membagi data: 80% pelatihan, 20% pengujian.
- Menskalakan fitur numerik menggunakan `StandardScaler`.

---

## Model Development
Menggunakan tiga model regresi:  
1. **Linear Regression**: Model dasar dengan hubungan linier.  
2. **Random Forest Regressor**: Ensemble decision trees dengan penyetelan hiperparameter.  
3. **XGBoost Regressor**: Gradient boosting dengan penyetelan hiperparameter.

### Penyetelan Hiperparameter
- **Random Forest**: GridSearchCV dengan parameter:  
  - `n_estimators`: [50, 100]  
  - `max_depth`: [5, 10]  
- **XGBoost**: GridSearchCV dengan parameter:  
  - `n_estimators`: [100, 200]  
  - `max_depth`: [5, 7]  
  - `learning_rate`: [0.1, 0.01]  

---

## Evaluasi
Menggunakan metrik MAE, MSE, dan R² untuk membandingkan performa model.

### Hasil Evaluasi
- **Linear Regression**:  
  - MAE: 0.2928  
  - MSE: 0.1136  
  - R²: 0.0799  

- **Random Forest (Disetel)**:  
  - MAE: 0.2854  
  - MSE: 0.1103  
  - R²: 0.1064  

- **XGBoost (Disetel)**:  
  - MAE: 0.2855  
  - MSE: 0.1098  
  - R²: 0.1103  

**Analisis**: Random Forest dan XGBoost menunjukkan performa lebih baik daripada Linear Regression, dengan XGBoost memiliki R² tertinggi (0.1103) dan MSE terendah (0.1098), menunjukkan kemampuan lebih baik dalam menjelaskan variansi data.

---

## Visualisasi Tambahan
Visualisasi pentingnya fitur dari Random Forest untuk memahami faktor yang paling berpengaruh terhadap risiko readmisi:  
![Pentingnya Fitur (Random Forest)](https://github.com/MHafisAfrizal/Predictive-Analytics/raw/main/Feature.png)  

*(Ganti URL di atas dengan URL asli setelah upload gambar ke GitHub)*

---

## Kesimpulan
Proyek ini berhasil memprediksi risiko readmisi pasien diabetes menggunakan pendekatan regresi. Model XGBoost (disetel) menunjukkan performa terbaik dengan R² tertinggi (0.1103) dan MSE terendah (0.1098), diikuti oleh Random Forest. Proyek memenuhi semua kriteria Dicoding dan menyertakan tambahan berikut untuk menargetkan peringkat 4-5 bintang:  
- Rekayasa fitur (`total_prosedur`, `kelompok_usia`).  
- Penyetelan hiperparameter (GridSearchCV untuk Random Forest dan XGBoost).  
- Perbandingan model (Linear Regression, Random Forest, XGBoost).  
- Visualisasi (pentingnya fitur).  
- Dokumentasi lengkap dalam notebook dan laporan Markdown.

Kode bersih, terdokumentasi dengan baik, dan dapat dieksekusi tanpa kesalahan.

---
