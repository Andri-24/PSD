---
title: Klasifikasi Lahan

---

# Klasifikasi Lahan

## Businness Understanding

### Latar Belakang & Masalah
Pemetaan tutupan lahan di wilayah Kabupaten Banyuwangi sangat penting untuk perencanaan wilayah, pemantauan kawasan hutan, dan analisis tata ruang pertanian serta pemukiman. Metode manual melalui visualisasi lapangan membutuhkan waktu lama dan biaya besar. Penggunaan citra satelit resolusi menengah seperti Sentinel-2A memungkinkan otomatisasi pemetaan tutupan lahan secara cepat dan terstruktur.

### Tujuan Proyek
* Membangun sistem klasifikasi tutupan lahan berbasis objek (*Object-Based Image Analysis / OBIA*) menggunakan algoritma **Support Vector Machine (SVM)**.
* Mengklasifikasikan wilayah studi ke dalam 5 kelas utama: **Bangunan**, **Hutan**, **Pertanian**, **Perairan**, dan **Mangrove**.
* Menghasilkan luaran data spasial berformat GeoJSON per kelas yang telah ditransformasi ke standar WGS84 (`EPSG:4326`) agar siap diintegrasikan ke dalam platform Web GIS.

### Pemanfaatan Hasil
Data GeoJSON yang dihasilkan dari model SVM ini digunakan sebagai layer interaktif pada aplikasi Web GIS berbasis Leaflet.js untuk mempermudah visualisasi dan pemantauan tata ruang secara digital.

## Data Understanding

### Dataset Citra Satelit
* **Sumber Data**: Citra Satelit Sentinel-2A (AOI Kabupaten Banyuwangi) dari Copernicus Data Space Ecosystem.
* **Rentang Waktu Akuisisi**: **11 September 2026 s.d. 11 Oktober 2026** (*Time Range Filter* saat *download*).
* **Sistem Proyeksi**: `EPSG:32749` (UTM Zone 49S).
* **Dimensi Citra**: 8235 x 8235 piksel[cite: 9].
* **Resolusi Spasial**: $13.33 \text{ meter/piksel}$.

### Rincian Komposisi & Fungsi Band Sentinel-2A
Penggunaan 6 band spesifik ini dipilih secara terukur untuk menjaga efisiensi computational dan resolusi spasial yang tajam:

* **B02 - Blue (Biru - $490\text{ nm}$)**
  * **Fungsi Utama**: Memisahkan tanah dari vegetasi serta melakukan penetrasi dasar perairan dangkal.
  * **Alasan Penggunaan**: Peka terhadap hamburan atmosfer dan pemetaan kualitas air/kedalaman perairan untuk membedakan kelas **Perairan** dari objek daratan.
* **B03 - Green (Hijau - $560\text{ nm}$)**
  * **Fungsi Utama**: Mengukur puncak reflektansi klorofil pada tumbuhan.
  * **Alasan Penggunaan**: Krusial untuk memisahkan vegetasi dengan objek non-vegetasi serta merupakan komponen penyusun kalkulasi **NDWI**.
* **B04 - Red (Merah - $665\text{ nm}$)**
  * **Fungsi Utama**: Mengukur penyerapan klorofil utama.
  * **Alasan Penggunaan**: Penyerapan kuat pada gelombang merah oleh vegetasi sehat menjadi komponen penyusun Indeks **NDVI** untuk membedakan **Hutan**, **Pertanian**, dan **Bangunan**.
* **B08 - NIR / Near Infrared (Inframerah Dekat - $842\text{ nm}$)**
  * **Fungsi Utama**: Mendeteksi struktur seluler daun dan kerapatan kanopi.
  * **Alasan Penggunaan**: Memantulkan gelombang NIR sangat kuat sehingga vital dalam membedakan kerapatan **Hutan** dari **Mangrove** atau **Pertanian**.
* **B11 - SWIR1 (Shortwave Infrared 1 - $1610\text{ nm}$)**
  * **Fungsi Utama**: Mendeteksi kandungan air pada kanopi tanaman serta tingkat kelembapan tanah dan material bangunan.
  * **Alasan Penggunaan**: Responsif terhadap lahan terbangun dan digunakan bersama NIR untuk menghitung **NDBI** guna memisahkan **Bangunan** dari pertanian.
* **B12 - SWIR2 (Shortwave Infrared 2 - $2190\text{ nm}$)**
  * **Fungsi Utama**: Membedakan kandungan mineral tanah, kelembapan, serta tipe vegetasi berkayu.
  * **Alasan Penggunaan**: Memberikan kontras spektral tambahan untuk memisahkan area **Hutan** lebat dan **Mangrove** dari vegetasi musiman/pertanian kering.

### Fitur Spektral Tambahan (Ekstraksi Fitur OBIA)
Untuk meningkatkan akurasi pemisahan objek, dilakukan kalkulasi 3 indeks spektral dan ekstraksi fitur statistik per poligon:
1. **Indeks Spektral**:
   * $\text{NDVI} = \frac{\text{B08} - \text{B04}}{\text{B08} + \text{B04}}$ (Memisah Vegetasi vs Non-Vegetasi)
   * $\text{NDWI} = \frac{\text{B03} - \text{B08}}{\text{B03} + \text{B08}}$ (Memisah Perairan vs Daratan)
   * $\text{NDBI} = \frac{\text{B11} - \text{B08}}{\text{B11} + \text{B08}}$ (Memisah Bangunan vs Lahan Terbuka/Pertanian)
2. **Statistik Poligon**: Nilai Rata-rata (*Mean*) dan Standar Deviasi (*Standard Deviation*) dari seluruh band dan indeks spektral dihitung menggunakan pendekatan *Zonal Statistics* untuk menangkap tekstur dan heterogenitas objek. Total digunakan **18 fitur spektral & tekstur**.

### Data Poligon ROI (Region of Interest)
Data acuan (*ground truth*) berasal dari file `banyuwangi.geojson` dengan total **4072 Poligon**:
* **Bangunan**: 2085 poligon
* **Hutan**: 1164 poligon
* **Pertanian**: 685 poligon
* **Perairan**: 112 poligon
* **Mangrove**: 26 poligon

## Modeling

<iframe 
  src=" https://andri-24.github.io/klasifikasi_lahan_banyuwangi//" 
  width="100%" 
  height="500px" 
  style="border: 1px solid #ccc; border-radius: 8px;">
</iframe>

## Evaluasi

```
# 2. Tentukan Path File
folder_path = '/content/drive/MyDrive/tugas_psd'
raster_path = os.path.join(folder_path, 'sentinel2_banyuwangi_aoi.tif')
geojson_path = os.path.join(folder_path, 'banyuwangi.geojson')

tif_files = glob.glob(os.path.join(folder_path, '*.tif'))
if tif_files:
    raster_path = tif_files[0]

print("="*60)
print("              INFORMASI METADATA & DATASET")
print("="*60)

# 3. Baca Informasi Citra & Band
with rasterio.open(raster_path) as src:
    bands_count = src.count
    height = src.height
    width = src.width
    crs_raster = src.crs
    res = src.res
    transform = src.transform
    img_data = src.read().astype(np.float32)

print(f"File Raster       : {os.path.basename(raster_path)}")
print(f"Dimensi Peta      : {width} x {height} piksel")
print(f"Jumlah Band Utama : {bands_count} Band")
print("Daftar Band Digunakan:")
print("  • Band 1: B02 (Blue)")
print("  • Band 2: B03 (Green)")
print("  • Band 3: B04 (Red)")
print("  • Band 4: B08 (NIR - Near Infrared)")
print("  • Band 5: B11 (SWIR1 - Shortwave Infrared 1)")
print("  • Band 6: B12 (SWIR2 - Shortwave Infrared 2)")
print("  • Indeks Tambahan: NDVI, NDWI, NDBI (Total 9 Fitur Spektral)")
print(f"Sistem Koordinat  : {crs_raster}")
print(f"Resolusi Spasial  : {res[0]} x {res[1]} meter/piksel")

# 4. Baca Informasi GeoJSON & Kelas
gdf = gpd.read_file(geojson_path)
if gdf.crs != crs_raster:
    gdf = gdf.to_crs(crs_raster)

name_col = 'class_name' if 'class_name' in gdf.columns else ('REMARK' if 'REMARK' in gdf.columns else gdf.columns[0])

print("\n" + "-"*60)
print("              INFORMASI SAMPLING POLIGON (ROI)")
print("-"*60)
print(f"File Vector       : {os.path.basename(geojson_path)}")
print(f"Total Poligon ROI : {len(gdf)} Poligon")
print("\nSebaran Poligon per Kelas:")
class_counts = gdf[name_col].value_counts()
for cls_name, count in class_counts.items():
    print(f"  • {cls_name:<25}: {count} poligon")

# 5. Hitung Indeks Spektral & Ekstraksi Fitur (Zonal Stats)
eps = 1e-10
b03, b04, b08, b11 = img_data[1], img_data[2], img_data[3], img_data[4]
ndvi = (b08 - b04) / (b08 + b04 + eps)
ndwi = (b03 - b08) / (b03 + b08 + eps)
ndbi = (b11 - b08) / (b11 + b08 + eps)

stacked_img = np.vstack([img_data, ndvi[np.newaxis, :, :], ndwi[np.newaxis, :, :], ndbi[np.newaxis, :, :]])
total_bands = stacked_img.shape[0]

print("\n" + "="*60)
print("            MEMPROSES EVALUASI HASIL AKURASI")
print("="*60)

stats_per_band = []
for b in range(total_bands):
    stats = zonal_stats(gdf, stacked_img[b], affine=transform, stats=['mean', 'std'])
    stats_per_band.append(stats)

X_list, valid_indices = [], []
for idx in range(len(gdf)):
    c_name = gdf.iloc[idx][name_col]
    if pd.isna(c_name): continue

    feat = []
    has_nan = False
    for b in range(total_bands):
        m = stats_per_band[b][idx]['mean']
        s = stats_per_band[b][idx]['std']
        if m is None or s is None or np.isnan(m) or np.isnan(s):
            has_nan = True
            break
        feat.extend([m, s])

    if not has_nan:
        X_list.append(feat)
        valid_indices.append(idx)

X = np.array(X_list)
gdf_valid = gdf.iloc[valid_indices].copy()
y = gdf_valid[name_col].values

# 6. Split, Train, & Evaluasi Model SVM
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
model = Pipeline([('scaler', StandardScaler()), ('svc', SVC(kernel='rbf', C=10.0, random_state=42))])
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

acc = accuracy_score(y_test, y_pred) * 100
print(f"\nHASIL AKURASI MODEL (SVM): {acc:.2f}%")
print("\nLaporan Performa per Kelas (Classification Report):")
print(classification_report(y_test, y_pred))
```

### Performa Model SVM
Model yang digunakan adalah **Support Vector Machine (SVM)** dengan arsitektur pipeline:
* **Preprocessing**: `StandardScaler` (Penskalaan nilai fitur agar seimbang).
* **Kernel**: Radial Basis Function (`RBF`).
* **Parameter**: $C = 10.0$, $\gamma = \text{'scale'}$.

### Hasil Akurasi
* **Overall Accuracy**: **72.66%**[cite: 9]

### Analisis Performa & Confusion Matrix
1. **Peningkatan Performa**: Penggunaan pendekatan berbasis poligon (*OBIA*) yang dikombinasikan dengan fitur tekstur (*std*) dan penskalaan fitur (`StandardScaler`) terbukti efektif memisahkan area yang bertekstur heterogen seperti Bangunan dari area yang homogen seperti Perairan dan Hutan.
2. **Ketidakseimbangan Data (*Class Imbalance*)**: Sebagian besar kesalahan klasifikasi (*misclassification*) terjadi pada kelas minoritas seperti Mangrove (26 poligon) dan Perairan (112 poligon) yang terprediksi sebagai Hutan atau Pertanian akibat dominasi sampel kelas Bangunan dan Hutan.
3. **Efisiensi Waktu**: Pemrosesan fitur berbasis *Zonal Stats* berhasil memangkas waktu ekstraksi dari puluhan menit menjadi hanya hitungan detik tanpa memicu masalah kapasitas memori (RAM).

```
============================================================
              INFORMASI METADATA & DATASET
============================================================
File Raster       : sentinel2_banyuwangi_aoi.tif
Dimensi Peta      : 8235 x 8235 piksel
Jumlah Band Utama : 6 Band
Daftar Band Digunakan:
  • Band 1: B02 (Blue)
  • Band 2: B03 (Green)
  • Band 3: B04 (Red)
  • Band 4: B08 (NIR - Near Infrared)
  • Band 5: B11 (SWIR1 - Shortwave Infrared 1)
  • Band 6: B12 (SWIR2 - Shortwave Infrared 2)
  • Indeks Tambahan: NDVI, NDWI, NDBI (Total 9 Fitur Spektral)
Sistem Koordinat  : EPSG:32749
Resolusi Spasial  : 13.333333333333334 x 13.333333333333334 meter/piksel

------------------------------------------------------------
              INFORMASI SAMPLING POLIGON (ROI)
------------------------------------------------------------
File Vector       : banyuwangi.geojson
Total Poligon ROI : 4072 Poligon

Sebaran Poligon per Kelas:
  • Bangunan                 : 2085 poligon
  • Hutan                    : 1164 poligon
  • Pertanian                : 685 poligon
  • Perairan                 : 112 poligon
  • Mangrove                 : 26 poligon

============================================================
            MEMPROSES EVALUASI HASIL AKURASI
============================================================
/usr/local/lib/python3.13/dist-packages/rasterstats/io.py:437: NodataWarning: Setting nodata to -999; specify nodata explicitly
  warnings.warn(

HASIL AKURASI MODEL (SVM): 72.66%
```