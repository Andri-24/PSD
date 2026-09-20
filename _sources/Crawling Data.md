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

### 2.1.2 Setting Area Of Interest(AOI)

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

### 2.1.3 Load Data

- Data Karbon Monoksida ($\text{CO}$)



```
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

s5_co = s5_co.aggregate_temporal_period(reducer="mean", period="day")
s5_co = s5_co.aggregate_spatial(reducer="mean", geometries=aoi)

job_co = s5_co.execute_batch(
    title="Trowulan CO Extraction",
    outputfile="trowulan_co.nc"
)
```

```
0:00:00 Job 'j-2609171341534a33a490e2b8585224ef': send 'start'
0:00:04 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:00:10 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:00:16 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:00:24 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:00:35 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:00:47 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:01:02 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:01:22 Job 'j-2609171341534a33a490e2b8585224ef': queued (progress 0%)
0:01:46 Job 'j-2609171341534a33a490e2b8585224ef': running (progress N/A)
0:02:16 Job 'j-2609171341534a33a490e2b8585224ef': running (progress N/A)
0:02:54 Job 'j-2609171341534a33a490e2b8585224ef': running (progress N/A)
0:03:40 Job 'j-2609171341534a33a490e2b8585224ef': finished (progress 100%)
```

- Data Nitrogen Dioksida ($\text{NO}_2$)

```
s5_co = connection.load_collection(
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

s5_co = s5_co.aggregate_temporal_period(reducer="mean", period="day")
s5_co = s5_co.aggregate_spatial(reducer="mean", geometries=aoi)

job_co = s5_co.execute_batch(
    title="Trowulan NO2 Extraction",
    outputfile="trowulan_no2.nc"
)
```

```
0:00:00 Job 'j-260917134625496091f98277863518d5': send 'start'
0:00:04 Job 'j-260917134625496091f98277863518d5': created (progress 0%)
0:00:10 Job 'j-260917134625496091f98277863518d5': queued (progress 0%)
0:00:16 Job 'j-260917134625496091f98277863518d5': queued (progress 0%)
0:00:24 Job 'j-260917134625496091f98277863518d5': queued (progress 0%)
0:00:34 Job 'j-260917134625496091f98277863518d5': queued (progress 0%)
0:00:47 Job 'j-260917134625496091f98277863518d5': queued (progress 0%)
0:01:02 Job 'j-260917134625496091f98277863518d5': running (progress N/A)
0:01:22 Job 'j-260917134625496091f98277863518d5': running (progress N/A)
0:01:46 Job 'j-260917134625496091f98277863518d5': running (progress N/A)
0:02:16 Job 'j-260917134625496091f98277863518d5': running (progress N/A)
0:02:54 Job 'j-260917134625496091f98277863518d5': running (progress N/A)
0:03:41 Job 'j-260917134625496091f98277863518d5': running (progress N/A)
0:04:39 Job 'j-260917134625496091f98277863518d5': finished (progress 100%)
```

- Data Sulfur Dioksida ($\text{SO}_2$)

```
s5_co = connection.load_collection(
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

s5_co = s5_co.aggregate_temporal_period(reducer="mean", period="day")
s5_co = s5_co.aggregate_spatial(reducer="mean", geometries=aoi)

job_co = s5_co.execute_batch(
    title="Trowulan SO2 Extraction",
    outputfile="trowulan_so2.nc"
)
```

```
0:00:00 Job 'j-2609171352034c4bbf2bad8cff40991e': send 'start'
0:00:03 Job 'j-2609171352034c4bbf2bad8cff40991e': created (progress 0%)
0:00:08 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:00:15 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:00:23 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:00:33 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:00:46 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:01:01 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:01:20 Job 'j-2609171352034c4bbf2bad8cff40991e': queued (progress 0%)
0:01:45 Job 'j-2609171352034c4bbf2bad8cff40991e': running (progress N/A)
0:02:15 Job 'j-2609171352034c4bbf2bad8cff40991e': running (progress N/A)
0:02:52 Job 'j-2609171352034c4bbf2bad8cff40991e': running (progress N/A)
0:03:39 Job 'j-2609171352034c4bbf2bad8cff40991e': running (progress N/A)
0:04:38 Job 'j-2609171352034c4bbf2bad8cff40991e': finished (progress 100%)
```

- Data Metana ($\text{CH}_4$)

```
s5_co = connection.load_collection(
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

s5_co = s5_co.aggregate_temporal_period(reducer="mean", period="day")
s5_co = s5_co.aggregate_spatial(reducer="mean", geometries=aoi)

job_co = s5_co.execute_batch(
    title="Trowulan CH4 Extraction",
    outputfile="trowulan_ch4.nc"
)
```

```
0:00:00 Job 'j-2609171357004e41a50e46a4073ce9a3': send 'start'
0:00:04 Job 'j-2609171357004e41a50e46a4073ce9a3': queued (progress 0%)
0:00:09 Job 'j-2609171357004e41a50e46a4073ce9a3': queued (progress 0%)
0:00:16 Job 'j-2609171357004e41a50e46a4073ce9a3': queued (progress 0%)
0:00:24 Job 'j-2609171357004e41a50e46a4073ce9a3': queued (progress 0%)
0:00:34 Job 'j-2609171357004e41a50e46a4073ce9a3': queued (progress 0%)
0:00:46 Job 'j-2609171357004e41a50e46a4073ce9a3': queued (progress 0%)
0:01:02 Job 'j-2609171357004e41a50e46a4073ce9a3': running (progress N/A)
0:01:21 Job 'j-2609171357004e41a50e46a4073ce9a3': running (progress N/A)
0:01:46 Job 'j-2609171357004e41a50e46a4073ce9a3': running (progress N/A)
0:02:16 Job 'j-2609171357004e41a50e46a4073ce9a3': running (progress N/A)
0:02:53 Job 'j-2609171357004e41a50e46a4073ce9a3': running (progress N/A)
0:03:40 Job 'j-2609171357004e41a50e46a4073ce9a3': finished (progress 100%)
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

![image](https://hackmd.io/_uploads/Byx8lX9tze.png)


#### NO2
- Gambar Grafik

![image](https://hackmd.io/_uploads/Bkuvg75Kfl.png)

#### SO2
- Gambar Grafik

![image](https://hackmd.io/_uploads/Bk0OgmqYMg.png)


#### CH4
- Gambar Grafik

![image](https://hackmd.io/_uploads/BJ1oe7cKfl.png)


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


### 2.2.4 Hasil Statistik
#### 2.2.4.1 CO

| row ID  | Column  | Min         | Max         | Mean        | Std. deviation | Variance    | Skewness    | Kurtosis    | Overall sum | No. missings | No. NaNs | No. +$\infty$s | No. -$\infty$s | Median      | Row count |
|---------|---------|-------------|-------------|-------------|----------------|-------------|-------------|-------------|-------------|--------------|----------|-----------|-----------|-------------|-----------|
| feature | feature | 0           | 0           | 0           | 0              | 0           | 0           | 0           | 0           | 0            | 0        | 0         | 0         | 0           | 311       |
| co      | co      | 0.016392469 | 0.043462717 | 0.028582905 | 0.00352191     | 1.24038E-05 | 0.282370379 | 1.244630237 | 8.889283606 | 0            | 0        | 0         | 0         | 0.028294643 | 311       |
| lat     | lat     | -7.5525     | -7.5525     | -7.5525     | 0              | 0           | 0           | 0           | -2348.8275  | 0            | 0        | 0         | 0         | -7.5525     | 311       |
| lon     | lon     | 112.4663501 | 112.4663501 | 112.4663501 | 0              | 0           | 0           | 0           | 34977.03488 | 0            | 0        | 0         | 0         | 112.4663501 | 311       |


#### 2.2.4.2 NO2

| row ID  | Column  | Min         | Max         | Mean        | Std. deviation | Variance    | Skewness | Kurtosis    | Overall sum | No. missings | No. NaNs | No. +$\infty$s | No. -$\infty$s | Median      | Row count |
|---------|---------|-------------|-------------|-------------|----------------|-------------|----------|-------------|-------------|--------------|----------|-----------|-----------|-------------|-----------|
| feature | feature | 0           | 0           | 0           | 0              | 0           | 0        | 0           | 0           | 0            | 0        | 0         | 0         | 0           | 259       |
| no2     | no2     | 4.32534E-06 | 9.69946E-05 | 4.00385E-05 | 1.46836E-05    | 2.15608E-10 | 0.571741 | 0.624996046 | 0.010369981 | 0            | 0        | 0         | 0         | 3.96572E-05 | 259       |
| lat     | lat     | -7.5525     | -7.5525     | -7.5525     | 0              | 0           | 0        | 0           | -1956.0975  | 0            | 0        | 0         | 0         | -7.5525     | 259       |
| lon     | lon     | 112.4663501 | 112.4663501 | 112.4663501 | 0              | 0           | 0        | 0           | 29128.78468 | 0            | 0        | 0         | 0         | 112.4663501 | 259       |


#### 2.2.4.3 SO2

| row ID  | Column  | Min         | Max         | Mean        | Std. deviation | Variance    | Skewness    | Kurtosis    | Overall sum | No. missings | No. NaNs | No. +$\infty$s | No. -$\infty$s | Median      | Row count |
|---------|---------|-------------|-------------|-------------|----------------|-------------|-------------|-------------|-------------|--------------|----------|-----------|-----------|-------------|-----------|
| feature | feature | 0           | 0           | 0           | 0              | 0           | 0           | 0           | 0           | 0            | 0        | 0         | 0         | 0           | 291       |
| so2     | so2     | -0.0003511  | 0.001175025 | 0.000139075 | 0.000199723    | 3.98891E-08 | 1.776471117 | 4.941146291 | 0.040470966 | 0            | 0        | 0         | 0         | 9.17392E-05 | 291       |
| lat     | lat     | -7.5525     | -7.5525     | -7.5525     | 0              | 0           | 0           | 0           | -2197.7775  | 0            | 0        | 0         | 0         | -7.5525     | 291       |
| lon     | lon     | 112.4663501 | 112.4663501 | 112.4663501 | 0              | 0           | 0           | 0           | 32727.70788 | 0            | 0        | 0         | 0         | 112.4663501 | 291       |


#### 2.2.4.4 CH4

| row ID  | Column  | Min         | Max         | Mean        | Std. deviation | Variance    | Skewness     | Kurtosis    | Overall sum | No. missings | No. NaNs | No. +$\infty$s | No. -$\infty$s | Median      | Row count |
|---------|---------|-------------|-------------|-------------|----------------|-------------|--------------|-------------|-------------|--------------|----------|-----------|-----------|-------------|-----------|
| feature | feature | 0           | 0           | 0           | 0              | 0           | 0            | 0           | 0           | 0            | 0        | 0         | 0         | 0           | 62        |
| ch4     | ch4     | 1834.913489 | 1935.768311 | 1893.221991 | 18.18395154    | 330.6560935 | -0.966599216 | 1.681297023 | 117379.7635 | 0            | 0        | 0         | 0         | 1894.209357 | 62        |
| lat     | lat     | -7.5525     | -7.5525     | -7.5525     | 0              | 0           | 0            | 0           | -468.255    | 0            | 0        | 0         | 0         | -7.5525     | 62        |
| lon     | lon     | 112.4663501 | 112.4663501 | 112.4663501 | 0              | 0           | 0            | 0           | 6972.913707 | 0            | 0        | 0         | 0         | 112.4663501 | 62        |


### 2.2.5 Identifikasi Missing Value

#### CO

```
import pandas as pd

# 1. Tentukan nama file yang ada di folder Google Colab kamu
file_path = 'trowulan_co.xlsx'  # Ganti dengan nama file kamu (misal: 'data.xlsx' atau 'data.csv')

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Cek ringkasan jumlah missing value per kolom
print("=== RINGKASAN MISSING VALUE PER KOLOM ===")
missing_summary = df.isnull().sum()
missing_summary = missing_summary[missing_summary > 0]

if missing_summary.empty:
    print("Dataset bersih! Tidak ditemukan missing value.")
else:
    print(missing_summary)
    print("\n" + "="*45 + "\n")

    # 3. Identifikasi lokasi persis (Baris & Kolom) dari data yang kosong
    missing_matrix = df.isna().stack()
    missing_locations = missing_matrix[missing_matrix].index.tolist()

    print("=== DETAIL LOKASI MISSING VALUE (BARIS & KOLOM) ===")
    for row_idx, col_name in missing_locations:
        # Menampilkan index baris (0-indexed) dan nama kolomnya
        print(f"Baris (index): {row_idx:<5} | Kolom: '{col_name}'")
```

```
=== RINGKASAN MISSING VALUE PER KOLOM ===
Dataset bersih! Tidak ditemukan missing value.
```

#### NO2

```
import pandas as pd

# 1. Tentukan nama file yang ada di folder Google Colab kamu
file_path = 'trowulan_no2.xlsx'  # Ganti dengan nama file kamu (misal: 'data.xlsx' atau 'data.csv')

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Cek ringkasan jumlah missing value per kolom
print("=== RINGKASAN MISSING VALUE PER KOLOM ===")
missing_summary = df.isnull().sum()
missing_summary = missing_summary[missing_summary > 0]

if missing_summary.empty:
    print("Dataset bersih! Tidak ditemukan missing value.")
else:
    print(missing_summary)
    print("\n" + "="*45 + "\n")

    # 3. Identifikasi lokasi persis (Baris & Kolom) dari data yang kosong
    missing_matrix = df.isna().stack()
    missing_locations = missing_matrix[missing_matrix].index.tolist()

    print("=== DETAIL LOKASI MISSING VALUE (BARIS & KOLOM) ===")
    for row_idx, col_name in missing_locations:
        # Menampilkan index baris (0-indexed) dan nama kolomnya
        print(f"Baris (index): {row_idx:<5} | Kolom: '{col_name}'")
```

```
=== RINGKASAN MISSING VALUE PER KOLOM ===
Dataset bersih! Tidak ditemukan missing value.
```

#### SO2

```
import pandas as pd

# 1. Tentukan nama file yang ada di folder Google Colab kamu
file_path = 'trowulan_so2.xlsx'  # Ganti dengan nama file kamu (misal: 'data.xlsx' atau 'data.csv')

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Cek ringkasan jumlah missing value per kolom
print("=== RINGKASAN MISSING VALUE PER KOLOM ===")
missing_summary = df.isnull().sum()
missing_summary = missing_summary[missing_summary > 0]

if missing_summary.empty:
    print("Dataset bersih! Tidak ditemukan missing value.")
else:
    print(missing_summary)
    print("\n" + "="*45 + "\n")

    # 3. Identifikasi lokasi persis (Baris & Kolom) dari data yang kosong
    missing_matrix = df.isna().stack()
    missing_locations = missing_matrix[missing_matrix].index.tolist()

    print("=== DETAIL LOKASI MISSING VALUE (BARIS & KOLOM) ===")
    for row_idx, col_name in missing_locations:
        # Menampilkan index baris (0-indexed) dan nama kolomnya
        print(f"Baris (index): {row_idx:<5} | Kolom: '{col_name}'")
```

```
=== RINGKASAN MISSING VALUE PER KOLOM ===
Dataset bersih! Tidak ditemukan missing value.
```

#### CH4

```
import pandas as pd

# 1. Tentukan nama file yang ada di folder Google Colab kamu
file_path = 'trowulan_ch4.xlsx'  # Ganti dengan nama file kamu (misal: 'data.xlsx' atau 'data.csv')

# Load data secara otomatis berdasarkan ekstensi file
if file_path.endswith(('.xlsx', '.xls')):
    df = pd.read_excel(file_path)
else:
    df = pd.read_csv(file_path)

# 2. Cek ringkasan jumlah missing value per kolom
print("=== RINGKASAN MISSING VALUE PER KOLOM ===")
missing_summary = df.isnull().sum()
missing_summary = missing_summary[missing_summary > 0]

if missing_summary.empty:
    print("Dataset bersih! Tidak ditemukan missing value.")
else:
    print(missing_summary)
    print("\n" + "="*45 + "\n")

    # 3. Identifikasi lokasi persis (Baris & Kolom) dari data yang kosong
    missing_matrix = df.isna().stack()
    missing_locations = missing_matrix[missing_matrix].index.tolist()

    print("=== DETAIL LOKASI MISSING VALUE (BARIS & KOLOM) ===")
    for row_idx, col_name in missing_locations:
        # Menampilkan index baris (0-indexed) dan nama kolomnya
        print(f"Baris (index): {row_idx:<5} | Kolom: '{col_name}'")
```

```
=== RINGKASAN MISSING VALUE PER KOLOM ===
Dataset bersih! Tidak ditemukan missing value.
```


### 2.2.6 Identifikasi Outlier dengan Ensemble (LOF + iForest + KNN)

#### CO

![image](https://hackmd.io/_uploads/rkjZKIqFzl.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| No | Waktu (t) | Nilai CO | LOF | iForest | KNN | Total Votes |
|---|---|---|---|---|---|---|
| 11 | 2025-09-10 | 0.024729 | 1 | 1 | 1 | 3 |
| 30 | 2025-10-04 | 0.041510 | 0 | 1 | 1 | 2 |
| 34 | 2025-10-09 | 0.045807 | 1 | 1 | 1 | 3 |
| 35 | 2025-10-10 | 0.033770 | 1 | 1 | 1 | 3 |
| 84 | 2026-03-05 | 0.017936 | 1 | 1 | 1 | 3 |
| 87 | 2026-03-12 | 0.021963 | 0 | 1 | 1 | 2 |
| 92 | 2026-03-21 | 0.020576 | 1 | 1 | 1 | 3 |


#### NO2

![image](https://hackmd.io/_uploads/r1gOU9LqFzx.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| No | Waktu (t) | Nilai NO2 | LOF | iForest | KNN | Total Votes |
|---|---|---|---|---|---|---|
| 1 | 2025-08-26 | 0.000078 | 1 | 1 | 1 | 3 |
| 43 | 2025-12-16 | 0.000011 | 1 | 1 | 1 | 3 |
| 141 | 2026-07-25 | 0.000083 | 1 | 1 | 1 | 3 |
| 152 | 2026-08-13 | 0.000090 | 1 | 1 | 1 | 3 |
| 153 | 2026-08-14 | 0.000048 | 1 | 1 | 1 | 3 |


#### SO2

![image](https://hackmd.io/_uploads/BkPcq89FMe.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| No | Waktu (t) | Nilai SO2 | LOF | iForest | KNN | Total Votes |
|---|---|---|---|---|---|---|
| 5 | 2025-08-31 | -0.000969 | 1 | 1 | 1 | 3 |
| 6 | 2025-09-01 | 0.000068 | 1 | 0 | 1 | 2 |
| 164 | 2026-07-01 | 0.001138 | 0 | 1 | 1 | 2 |
| 178 | 2026-07-17 | -0.000673 | 1 | 1 | 1 | 3 |
| 189 | 2026-07-28 | -0.000994 | 1 | 1 | 1 | 3 |
| 190 | 2026-08-01 | -0.000122 | 1 | 1 | 1 | 3 |
| 191 | 2026-08-02 | -0.000696 | 1 | 1 | 1 | 3 |


#### CH4

![image](https://hackmd.io/_uploads/Hk3C9U9Yze.png)

=== TABEL DETAIL VOTING ENSEMBLE OUTLIER ===
| No | Waktu (t) | Nilai CH4 | LOF | iForest | KNN | Total Votes |
|---|---|---|---|---|---|---|
| 1 | 2025-09-03 | 1855.129883 | 0 | 1 | 1 | 2 |


### Handling Outlier dengan menggunakan Rolling Median Imputation

#### CO

![image](https://hackmd.io/_uploads/HJ8hyOoFfx.png)

#### NO2

![image](https://hackmd.io/_uploads/Hyd-guoKMe.png)

#### SO2

![image](https://hackmd.io/_uploads/H1fNe_sFGg.png)

#### CH4

![image](https://hackmd.io/_uploads/ryaSlujFMg.png)

