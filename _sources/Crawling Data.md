---
title: Crawling Data

---

# Crawling Data
## 1. Business Understanding
## 1.1 Mengamati Kualitas Udara
### 1.1.1 Deskripsi Indeks Kualitas Udara
Indeks Kualitas Udara (*Air Quality Index* / AQI atau di Indonesia dikenal sebagai ISPU - Indeks Standar Pencemar Udara) adalah indikator kuantitatif terstandardisasi yang digunakan untuk mengomunikasikan tingkat kebersihan atau polusi udara ambien kepada publik dan pemangku kebijakan. 

Dalam perspektif *data science* dan *environmental analytics*, AQI berfungsi menyederhanakan data konsentrasi multivariat polutan kimia kompleks (seperti partikulat mikroskopis dan gas reaktif) menjadi skala numerik tunggal yang intuitif, umumnya disertai kode warna dan kategori dampak kesehatan (misalnya: *Baik*, *Sedang*, *Tidak Sehat*, hingga *Berbahaya*). 

Pengamatan kualitas udara berbasis data penginderaan jauh (*remote sensing*) satelit seperti **Copernicus Sentinel-5P (TROPOMI)** memungkinkan estimasi densitas kolom gas vertikal troposfer (*tropospheric vertical column density*) secara berkala, membantu mendeteksi tren spasial dan temporal dispersi emisi di suatu wilayah geografis.

---

### 1.1.2 Unsur-Unsur Penentu Kualitas Udara

Kualitas udara di suatu daerah dipengaruhi oleh konsentrasi berbagai polutan atmosfer primer dan sekunder. Karakteristik kimia, sumber emisi, serta dampak dari masing-masing unsur polutan dijelaskan sebagai berikut:

#### a. Nitrogen Dioksida ($\text{NO}_2$)
* **Deskripsi Kimiawi:** Gas reaktif berwarna cokelat kemerahan dengan bau menyengat, tergolong dalam kelompok nitrogen oksida ($\text{NO}_x$).
* **Sumber Utama Emisi:** Proses pembakaran pada suhu tinggi, terutama emisi kendaraan bermotor berbahan bakar fosil (transportasi jalan), pembangkit listrik tenaga uap/termal (PLTU), dan kawasan industri manufaktur.
* **Dampak Kualitas Udara & Kesehatan:** Merupakan prekursor utama pembentukan ozon troposferik ($\text{O}_3$) dan aerosol nitrat melalui reaksi fotokimia. Paparan $\text{NO}_2$ konsentrasi tinggi dapat mengiritasi saluran pernapasan, memperburuk kondisi asma, dan memicu penyakit paru obstruktif kronis (PPOK).

#### b. Karbon Monoksida ($\text{CO}$)
* **Deskripsi Kimiawi:** Gas tidak berwarna, tidak berbau, dan tidak berasa yang terbentuk dari oksidasi karbon yang tidak sempurna.
* **Sumber Utama Emisi:** Pembakaran bahan bakar fosil yang tidak sempurna pada kendaraan transportasi, mesin industri, serta peristiwa pembakaran biomassa skala besar seperti kebakaran hutan dan lahan (karhutla).
* **Dampak Kualitas Udara & Kesehatan:** Memiliki waktu hidup (*atmospheric lifetime*) yang relatif panjang (sekitar beberapa minggu hingga bulan), menjadikannya *tracer* (pelacak) yang sangat baik untuk pergerakan polusi lintas wilayah (*transboundary pollution*). Di dalam tubuh manusia, CO mengikat hemoglobin jauh lebih kuat daripada oksigen, menurunkan kapasitas pengangkutan oksigen dalam darah.

#### c. Sulfur Dioksida ($\text{SO}_2$)
* **Deskripsi Kimiawi:** Gas tidak berwarna dengan aroma menyengat tajam yang sangat reaktif di atmosfer.
* **Sumber Utama Emisi:** Pembakaran bahan bakar fosil yang mengandung sulfur tinggi (khususnya batu bara dan minyak bumi berat) pada PLTU, proses pemurnian minyak (*refinery*), industri peleburan logam, serta sumber alami berupa aktivitas vulkanik gunung berapi.
* **Dampak Kualitas Udara & Kesehatan:** Oksidasi $\text{SO}_2$ di atmosfer menghasilkan aerosol sulfat dan asam sulfat ($\text{H}_2\text{SO}_4$), yang merupakan komponen utama fenomena hujan asam (*acid rain*). Gas ini menyebabkan bronkokonstriksi, iritasi mukosa, dan gangguan pernapasan akut.

#### d. Metana ($\text{CH}_4$)
* **Deskripsi Kimiawi:** Hidrokarbon paling sederhana berbentuk gas tanpa warna dan bau, merupakan salah satu gas rumah kaca (*Greenhouse Gas* / GHG) dengan potensi pemanasan global (*Global Warming Potential*) sekitar 28–36 kali lebih tinggi dibanding $\text{CO}_2$ dalam rentang 100 tahun.
* **Sumber Utama Emisi:** Aktivitas ekstraksi dan kebocoran distribusi gas alam/minyak bumi, sektor pertanian (fermentasi enterik peternakan), penanaman padi lahan basah, serta dekomposisi limbah organik di Tempat Pemrosesan Akhir (TPA).
* **Dampak Kualitas Udara & Lingkungan:** Meskipun tidak beracun secara langsung pada konsentrasi ambien normal, metana berperan krusial sebagai pendorong utama pembentukan ozon di lapisan troposfer melalui reaksi fotokimia global, selain mempercepat perubahan iklim dan kenaikan suhu permukaan bumi.

## 2.  Data Understanding

### 2.1 Collecting Data

#### 2.1.1 Hubungkan Data ke Web Copernicus

```
!pip install openeo
import openeo
```

```
import openeo
connection = openeo.connect("openeo.dataspace.copernicus.eu").authenticate_oidc()
```

```
Visit https://identity.dataspace.copernicus.eu/auth/realms/CDSE/device?user_code=MSAU-FFTF 📋 to authenticate.
✅ Authorized successfully
Authenticated using device code flow.
```

#### 2.1.2 Setting Area Of Interest(AOI)

```
aoi = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "nama": "Kecamatan Trowulan",
                "kabupaten": "Mojokerto",
                "provinsi": "Jawa Timur",
            },
            "geometry": {
                "type": "Polygon",
                "coordinates": [
                    [
                        [112.355, -7.525],
                        [112.385, -7.520],
                        [112.415, -7.535],
                        [112.420, -7.560],
                        [112.410, -7.595],
                        [112.390, -7.605],
                        [112.365, -7.595],
                        [112.350, -7.575],
                        [112.345, -7.545],
                        [112.355, -7.525],
                    ]
                ],
            },
        }
    ],
}
```

#### 2.1.3 Load Data

- Data Karbon Monoksida ($\text{CO}$)



```
# 1. Load data koleksi Sentinel-5P L2
s5_co = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.345,
        "south": -7.605,
        "east": 112.420,
        "north": -7.520,
    },
    bands=["CO"],
)

# 2. Agregasi temporal harian
s5_co = s5_co.aggregate_temporal_period(
    period="day",
    reducer="mean"
)

# 3. Agregasi spasial sesuai dengan AOI yang sudah kamu definisikan sebelumnya
s5_co = s5_co.aggregate_spatial(
    geometries=aoi,
    reducer="mean"
)

# 4. Eksekusi batch job untuk menyimpan ke file NetCDF (.nc)
job_co = s5_co.execute_batch(
    title="Trowulan CO Extraction",
    outputfile="trowulan_co.nc"
)
```

```
0:00:00 Job 'j-26100216401647c99714542575ec9423': send 'start'
0:00:03 Job 'j-26100216401647c99714542575ec9423': queued (progress 0%)
0:00:08 Job 'j-26100216401647c99714542575ec9423': queued (progress 0%)
0:00:14 Job 'j-26100216401647c99714542575ec9423': queued (progress 0%)
0:00:22 Job 'j-26100216401647c99714542575ec9423': queued (progress 0%)
0:00:32 Job 'j-26100216401647c99714542575ec9423': queued (progress 0%)
0:00:45 Job 'j-26100216401647c99714542575ec9423': queued (progress 0%)
0:01:00 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:01:20 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:01:44 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:02:14 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:02:51 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:03:38 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:04:36 Job 'j-26100216401647c99714542575ec9423': running (progress N/A)
0:05:37 Job 'j-26100216401647c99714542575ec9423': finished (progress 100%)
```

- Data Nitrogen Dioksida ($\text{NO}_2$)

```
# 1. Load data koleksi Sentinel-5P L2
s5_no2 = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.345,
        "south": -7.605,
        "east": 112.420,
        "north": -7.520,
    },
    bands=["NO2"],
)

# 2. Agregasi temporal harian
s5_no2 = s5_no2.aggregate_temporal_period(
    period="day",
    reducer="mean"
)

# 3. Agregasi spasial sesuai dengan AOI yang sudah kamu definisikan sebelumnya
s5_no2 = s5_no2.aggregate_spatial(
    geometries=aoi,
    reducer="mean"
)

# 4. Eksekusi batch job untuk menyimpan ke file NetCDF (.nc)
job_no2 = s5_no2.execute_batch(
    title="Trowulan NO2 Extraction",
    outputfile="trowulan_no2.nc"
)
```

```
0:00:00 Job 'j-26100216521846ecbd562346c1320c90': send 'start'
0:00:03 Job 'j-26100216521846ecbd562346c1320c90': queued (progress 0%)
0:00:08 Job 'j-26100216521846ecbd562346c1320c90': queued (progress 0%)
0:00:15 Job 'j-26100216521846ecbd562346c1320c90': queued (progress 0%)
0:00:23 Job 'j-26100216521846ecbd562346c1320c90': queued (progress 0%)
0:00:33 Job 'j-26100216521846ecbd562346c1320c90': queued (progress 0%)
0:00:45 Job 'j-26100216521846ecbd562346c1320c90': queued (progress 0%)
0:01:01 Job 'j-26100216521846ecbd562346c1320c90': running (progress N/A)
0:01:20 Job 'j-26100216521846ecbd562346c1320c90': running (progress N/A)
0:01:44 Job 'j-26100216521846ecbd562346c1320c90': running (progress N/A)
0:02:14 Job 'j-26100216521846ecbd562346c1320c90': running (progress N/A)
0:02:51 Job 'j-26100216521846ecbd562346c1320c90': running (progress N/A)
0:03:38 Job 'j-26100216521846ecbd562346c1320c90': running (progress N/A)
0:04:37 Job 'j-26100216521846ecbd562346c1320c90': finished (progress 100%)
```

- Data Sulfur Dioksida ($\text{SO}_2$)

```
# 1. Load data koleksi Sentinel-5P L2
s5_so2 = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.345,
        "south": -7.605,
        "east": 112.420,
        "north": -7.520,
    },
    bands=["SO2"],
)

# 2. Agregasi temporal harian
s5_so2 = s5_so2.aggregate_temporal_period(
    period="day",
    reducer="mean"
)

# 3. Agregasi spasial sesuai dengan AOI yang sudah kamu definisikan sebelumnya
s5_so2 = s5_so2.aggregate_spatial(
    geometries=aoi,
    reducer="mean"
)

# 4. Eksekusi batch job untuk menyimpan ke file NetCDF (.nc)
job_so2 = s5_so2.execute_batch(
    title="Trowulan SO2 Extraction",
    outputfile="trowulan_so2.nc"
)
```

```
0:00:00 Job 'j-2610021707244e95823bf06f61f78ad6': send 'start'
0:00:04 Job 'j-2610021707244e95823bf06f61f78ad6': queued (progress 0%)
0:00:09 Job 'j-2610021707244e95823bf06f61f78ad6': queued (progress 0%)
0:00:15 Job 'j-2610021707244e95823bf06f61f78ad6': queued (progress 0%)
0:00:24 Job 'j-2610021707244e95823bf06f61f78ad6': queued (progress 0%)
0:00:34 Job 'j-2610021707244e95823bf06f61f78ad6': queued (progress 0%)
0:00:46 Job 'j-2610021707244e95823bf06f61f78ad6': queued (progress 0%)
0:01:01 Job 'j-2610021707244e95823bf06f61f78ad6': running (progress N/A)
0:01:21 Job 'j-2610021707244e95823bf06f61f78ad6': running (progress N/A)
0:01:45 Job 'j-2610021707244e95823bf06f61f78ad6': running (progress N/A)
0:02:15 Job 'j-2610021707244e95823bf06f61f78ad6': running (progress N/A)
0:02:52 Job 'j-2610021707244e95823bf06f61f78ad6': running (progress N/A)
0:03:39 Job 'j-2610021707244e95823bf06f61f78ad6': running (progress N/A)
0:04:38 Job 'j-2610021707244e95823bf06f61f78ad6': finished (progress 100%)
```

- Data Metana ($\text{CH}_4$)

```
# 1. Load data koleksi Sentinel-5P L2
s5_ch4 = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.345,
        "south": -7.605,
        "east": 112.420,
        "north": -7.520,
    },
    bands=["CH4"],
)

# 2. Agregasi temporal harian
s5_ch4 = s5_ch4.aggregate_temporal_period(
    period="day",
    reducer="mean"
)

# 3. Agregasi spasial sesuai dengan AOI yang sudah kamu definisikan sebelumnya
s5_ch4 = s5_ch4.aggregate_spatial(
    geometries=aoi,
    reducer="mean"
)

# 4. Eksekusi batch job untuk menyimpan ke file NetCDF (.nc)
job_ch4 = s5_ch4.execute_batch(
    title="Trowulan CH4 Extraction",
    outputfile="trowulan_ch4.nc"
)
```

```
0:00:00 Job 'j-2610021717474f46ac98038264c7bede': send 'start'
0:00:02 Job 'j-2610021717474f46ac98038264c7bede': created (progress 0%)
0:00:08 Job 'j-2610021717474f46ac98038264c7bede': queued (progress 0%)
0:00:14 Job 'j-2610021717474f46ac98038264c7bede': queued (progress 0%)
0:00:22 Job 'j-2610021717474f46ac98038264c7bede': queued (progress 0%)
0:00:32 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:00:45 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:01:00 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:01:19 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:01:43 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:02:14 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:02:51 Job 'j-2610021717474f46ac98038264c7bede': running (progress N/A)
0:03:38 Job 'j-2610021717474f46ac98038264c7bede': finished (progress 100%)
```

## 2.2 Eksplorasi Data
### 2.2.1 Visualisasi Data
#### Gambar Peta

<iframe 
  src="https://andri-24.github.io/peta-aoi-kbtpnmjkrt/peta_aoi_trowulan.html" 
  width="100%" 
  height="500px" 
  style="border: 1px solid #ccc; border-radius: 8px;">
</iframe>

#### Deskripsi Wilayah: Area of Interest (AOI) Kecamatan Trowulan

Peta interaktif di atas merepresentasikan batas spasial Area of Interest (AOI) yang digunakan sebagai acuan pemrosesan data penginderaan jauh atmosfer dari satelit Sentinel-5P Level-2 (TROPOMI) melalui platform openEO Copernicus Data Space Ecosystem.
- Nama Wilayah: Kecamatan Trowulan, Kabupaten Mojokerto, Provinsi Jawa Timur, Indonesia
- Geometri Spasial: Poligon batas administratif tertutup (9 titik koordinat verteks)
- Cakupan Bounding Box ($spatial\_extent$):
Bujur (Longitude): $112.340^\circ \text{ BT} - 112.420^\circ \text{ BT}$
Lintang (Latitude): $7.525^\circ \text{ LS} - 7.615^\circ \text{ LS}$
- Karakteristik Wilayah:Wilayah kajian didominasi oleh topografi dataran rendah aluvial yang relatif datar di bagian barat daya Kabupaten Mojokerto (berbatasan langsung dengan Kabupaten Jombang). Karakteristik tutupan lahannya merupakan perpaduan antara lahan pertanian irigasi intensif, pemukiman pedesaan-suburban, koridor jalur arteri transportasi nasional (jalan nasional Surabaya–Madiun), serta kawasan cagar budaya dan situs arkeologi peninggalan era Majapahit dengan keberadaan sentra industri batu bata lokal.

### 2.2.2 Visualisasi Data Grafik
#### CO

- Gambar Grafik

![image](https://hackmd.io/_uploads/Bk54Obgjzl.png)

#### NO2
- Gambar Grafik

![image](https://hackmd.io/_uploads/rJZPdZeizg.png)

#### SO2
- Gambar Grafik

![image](https://hackmd.io/_uploads/S1Hud-eoMg.png)

#### CH4
- Gambar Grafik

![image](https://hackmd.io/_uploads/H19K_bljGg.png)


## 2.2.3 Identifikasi Missing Value

#### CO

```
import pandas as pd

# 1. Tentukan nama file
file_path = 'trowulan_co_clean.csv'

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Hitung jumlah missing value
missing_per_col = df.isnull().sum()
total_missing = df.isnull().sum().sum()

# 3. Tampilkan hasil
print("=== JUMLAH MISSING VALUE ===")
print(missing_per_col)
print("-" * 30)
print(f"Total seluruh missing value: {total_missing}")
```

```
=== JUMLAH MISSING VALUE ===
Tanggal      0
CO         156
dtype: int64
------------------------------
Total seluruh missing value: 156
```

#### NO2

```
import pandas as pd

# 1. Tentukan nama file
file_path = 'trowulan_no2_clean.csv'

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Hitung jumlah missing value
missing_per_col = df.isnull().sum()
total_missing = df.isnull().sum().sum()

# 3. Tampilkan hasil
print("=== JUMLAH MISSING VALUE ===")
print(missing_per_col)
print("-" * 30)
print(f"Total seluruh missing value: {total_missing}")
```

```
=== JUMLAH MISSING VALUE ===
Tanggal      0
NO2        207
dtype: int64
------------------------------
Total seluruh missing value: 207
```

#### SO2

```
import pandas as pd

# 1. Tentukan nama file
file_path = 'trowulan_so2_clean.csv'

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Hitung jumlah missing value
missing_per_col = df.isnull().sum()
total_missing = df.isnull().sum().sum()

# 3. Tampilkan hasil
print("=== JUMLAH MISSING VALUE ===")
print(missing_per_col)
print("-" * 30)
print(f"Total seluruh missing value: {total_missing}")
```

```
=== JUMLAH MISSING VALUE ===
Tanggal      0
SO2        155
dtype: int64
------------------------------
Total seluruh missing value: 155
```

#### CH4

```
import pandas as pd

# 1. Tentukan nama file
file_path = 'trowulan_ch4_clean.csv'

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Hitung jumlah missing value
missing_per_col = df.isnull().sum()
total_missing = df.isnull().sum().sum()

# 3. Tampilkan hasil
print("=== JUMLAH MISSING VALUE ===")
print(missing_per_col)
print("-" * 30)
print(f"Total seluruh missing value: {total_missing}")
```

```
=== JUMLAH MISSING VALUE ===
Tanggal      0
CH4        351
dtype: int64
------------------------------
Total seluruh missing value: 351
```

## 2.2.3 Handling Missing Value dengan Imputasi Polinomial

### Pengertian Imputasi Polinomial

### Grafik setelah diimputasi

#### CO

![image](https://hackmd.io/_uploads/ByLT_Wgofg.png)

#### NO2

![image](https://hackmd.io/_uploads/Sy67FbeiGg.png)

#### SO2

![image](https://hackmd.io/_uploads/BytftWgoMe.png)

#### CH4

![image](https://hackmd.io/_uploads/HyQ_tWlsGx.png)


### 2.2.6 Identifikasi Outlier dengan Ensemble (LOF + iForest + Z-Score)

#### CO

![image](https://hackmd.io/_uploads/rkonKWeszx.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| Tanggal | Nilai CO | Vote iForest | Vote LOF | Vote Z-Score | Total Votes |
|---|---|---|---|---|---|
| 2025-09-11 | 0.022415 | 1 | 1 | 0 | 2 |
| 2025-10-09 | 0.045807 | 1 | 1 | 1 | 3 |
| 2026-01-24 | 0.045807 | 0 | 1 | 1 | 2 |
| 2026-01-25 | 0.045807 | 0 | 1 | 1 | 2 |
| 2026-01-26 | 0.045807 | 0 | 1 | 1 | 2 |
| 2026-01-27 | 0.045807 | 0 | 1 | 1 | 2 |
| 2026-01-28 | 0.045807 | 0 | 1 | 1 | 2 |
| 2026-01-29 | 0.045807 | 0 | 1 | 1 | 2 |
| 2026-03-05 | 0.017936 | 1 | 1 | 0 | 2 |


#### NO2

![image](https://hackmd.io/_uploads/Hy35qZlsGg.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| Tanggal | Nilai NO2 | Vote iForest | Vote LOF | Vote Z-Score | Total Votes |
|---|---|---|---|---|---|
| 2025-08-25 | 0.000048 | 1 | 1 | 0 | 2 |
| 2025-08-27 | 0.000090 | 1 | 0 | 1 | 2 |
| 2025-08-28 | 0.000090 | 1 | 0 | 1 | 2 |
| 2026-07-25 | 0.000083 | 1 | 1 | 0 | 2 |
| 2026-08-13 | 0.000090 | 1 | 1 | 1 | 3 |


#### SO2

![image](https://hackmd.io/_uploads/ryEyjbgjGx.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| Tanggal | Nilai SO2 | Vote iForest | Vote LOF | Vote Z-Score | Total Votes |
|---|---|---|---|---|---|
| 2025-08-31 | -0.000969 | 1 | 1 | 1 | 3 |
| 2026-05-07 | 0.000022 | 1 | 1 | 0 | 2 |
| 2026-07-01 | 0.001138 | 1 | 0 | 1 | 2 |
| 2026-07-17 | -0.000673 | 1 | 1 | 0 | 2 |
| 2026-07-28 | -0.000994 | 1 | 1 | 1 | 3 |
| 2026-07-29 | -0.000994 | 1 | 1 | 1 | 3 |
| 2026-08-02 | -0.000696 | 1 | 1 | 0 | 2 |


#### CH4

![image](https://hackmd.io/_uploads/Sy0MjWeife.png)

## 2.2.6 Handling Outlier dengan menggunakan Interpolasi Temporal

#### CO

![image](https://hackmd.io/_uploads/rkcSjbljGl.png)

#### NO2

![image](https://hackmd.io/_uploads/SJvUoZxsMl.png)

#### SO2

![image](https://hackmd.io/_uploads/BJ_wi-ejMl.png)

### 2.2.3 Pengertian Statistik Properti pada KNIME

1.  Column

Merupakan nama atau label pengenal dari atribut (variabel) di dalam dataset. Fungsinya adalah sebagai penanda identitas data agar analis dapat membedakan variabel yang sedang dipelajari serta mempermudah referensi pada tahapan transformasi data berikutnya.

2.  Min

Nilai terendah atau batas bawah numerik yang tercatat pada kolom tersebut. Fungsinya untuk mengetahui rentang data paling dasar serta mendeteksi potensi anomali nilai minimum, seperti angka negatif pada fitur yang seharusnya bernilai non-negatif.

3.  Max

Nilai tertinggi atau batas atas numerik yang tercatat pada kolom tersebut. Fungsinya untuk melihat jangkauan nilai maksimum data serta membantu mendeteksi keberadaan pencilan (outlier) ekstrem atas.

4.  Mean

Nilai rata-rata hitung aritmatika dari seluruh data yang valid. Fungsinya adalah memberikan gambaran nilai tipikal atau titik pusat data secara umum jika sebaran data diasumsikan simetris.

5.  Standard Deviation

Ukuran dispersi yang menunjukkan seberapa jauh variasi setiap titik data menyimpang dari nilai rata-ratanya (mean). Fungsinya untuk menilai tingkat volatilitas data dan menentukan apakah data mengumpul rapat di sekitar rata-rata atau menyebar luas.

6.  Variance

Kuadrat dari nilai Standard Deviation yang menggambarkan rata-rata kuadrat deviasi data dari nilai mean. Fungsinya untuk mengukur total variabilitas data secara matematis; metrik ini menjadi dasar perhitungan dalam berbagai metode analisis lanjutan seperti ANOVA atau Principal Component Analysis (PCA).

7.  Skewness

Ukuran asimetri atau derajat kemencengan kurva distribusi data terhadap titik pusatnya. Fungsinya untuk mendeteksi arah kemiringan data; nilai mendekati 0 berarti simetris, nilai positif menunjukkan kemiringan ke kanan (ekor panjang di sisi kanan), dan nilai negatif menunjukkan kemiringan ke kiri.

8.  Kurtosis

Ukuran keruncingan kurva distribusi serta ketebalan ekor data (heavy-tailedness) dibandingkan dengan distribusi normal standar. Fungsinya untuk mendeteksi apakah data memiliki risiko ekstrem yang tinggi (leptokurtik / ekor tebal) atau sebaliknya memiliki sebaran yang lebih datar dan minim nilai ekstrem (platikurtik).

9.  Overall Sum

Total penjumlahan kumulatif dari semua nilai numerik yang ada di kolom tersebut. Fungsinya untuk melihat volume agregat beban keseluruhan dalam periode pengamatan, seperti total beban emisi gas atau akumulasi total volume.

10.  No. Missings

Jumlah baris yang sama sekali tidak memiliki nilai (null atau blank cell). Fungsinya untuk mengukur integritas dan kelengkapan baris data sehingga analis dapat menentukan langkah pembersihan seperti penghapusan baris (drop) atau imputasi nilai.

11.  No. NaNs

Jumlah nilai yang tercatat sebagai Not a Number (NaN), biasanya terjadi akibat kegagalan komputasi matematika seperti pembagian dengan angka nol atau kesalahan type casting. Fungsinya untuk mendeteksi adanya korupsi data teknis pada level kalkulasi numerik.

12.  No. +∞s

Jumlah kemunculan nilai tak hingga positif (positive infinity). Fungsinya untuk menemukan kegagalan pembagian dengan angka nol yang mendekati nol positif atau overflow komputasi yang dapat merusak perhitungan algoritma machine learning jika dibiarkan.

13.  No. -∞s

Jumlah kemunculan nilai tak hingga negatif (negative infinity). Fungsinya untuk mendeteksi kesalahan numerik ekstrem batas bawah (underflow matematis atau fungsi logaritma bernilai nol) yang perlu dinormalisasi atau dibersihkan.

14.  Median

Nilai tengah dari dataset setelah seluruh nilai diurutkan dari yang terkecil hingga terbesar. Fungsinya adalah sebagai ukuran pemusatan (central tendency) yang sangat tangguh (robust) terhadap pengaruh pencilan (outlier) ekstrem jika dibandingkan dengan nilai mean.

15.  Row Count

Total keseluruhan jumlah baris data yang ada pada dataset untuk kolom tersebut. Fungsinya untuk mengetahui ukuran sampel data yang sedang diproses dan menjadi angka pembagi dasar dalam menghitung rasio missing values atau persentase validitas.

16.  Histogram

Representasi grafis dalam bentuk baris atau balok frekuensi yang membagi rentang data ke dalam beberapa interval (bins). Fungsinya untuk melihat bentuk riil dari distribusi data secara visual, mendeteksi modalitas (apakah data memiliki satu puncak, dua puncak, atau seragam), serta mengonfirmasi pola sebaran secara cepat.