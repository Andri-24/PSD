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

### 2.1.1 Data Karbon Monoksida ($\text{CO}$)

#### Load Data

```
s5_co = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.31,
        "south": -7.785,
        "east": 112.62,
        "north": -7.362,
    },
    bands=["CO"],
)

s5_co = s5_co.aggregate_temporal_period(reducer="mean", period="day")
s5_co = s5_co.aggregate_spatial(reducer="mean", geometries=aoi)

job_co = s5_co.execute_batch(
    title="Mojokerto CO Extraction", outputfile="mojokerto_co.nc"
)
```

```
0:00:00 Job 'j-2608301938404fb2a82fa5c953f9df3a': send 'start'
0:00:03 Job 'j-2608301938404fb2a82fa5c953f9df3a': created (progress 0%)
0:00:08 Job 'j-2608301938404fb2a82fa5c953f9df3a': queued (progress 0%)
0:00:15 Job 'j-2608301938404fb2a82fa5c953f9df3a': queued (progress 0%)
0:00:23 Job 'j-2608301938404fb2a82fa5c953f9df3a': queued (progress 0%)
0:00:33 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:00:46 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:01:01 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:01:21 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:01:45 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:02:15 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:02:53 Job 'j-2608301938404fb2a82fa5c953f9df3a': running (progress N/A)
0:03:40 Job 'j-2608301938404fb2a82fa5c953f9df3a': finished (progress 100%)
```

### 2.1.2 Data Nitrogen Dioksida ($\text{NO}_2$)

#### Load Data

```
s5_no2 = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.31,
        "south": -7.785,
        "east": 112.62,
        "north": -7.362,
    },
    bands=["NO2"],
)

s5_no2 = s5_no2.aggregate_temporal_period(reducer="mean", period="day")
s5_no2 = s5_no2.aggregate_spatial(reducer="mean", geometries=aoi)

job_no2 = s5_no2.execute_batch(
    title="Mojokerto NO2 Extraction", outputfile="mojokerto_no2.nc"
)
```

```
0:00:00 Job 'j-2608301948074764bee9dd2f709210d7': send 'start'
0:00:02 Job 'j-2608301948074764bee9dd2f709210d7': created (progress 0%)
0:00:07 Job 'j-2608301948074764bee9dd2f709210d7': queued (progress 0%)
0:00:14 Job 'j-2608301948074764bee9dd2f709210d7': queued (progress 0%)
0:00:22 Job 'j-2608301948074764bee9dd2f709210d7': queued (progress 0%)
0:00:32 Job 'j-2608301948074764bee9dd2f709210d7': queued (progress 0%)
0:00:45 Job 'j-2608301948074764bee9dd2f709210d7': queued (progress 0%)
0:01:00 Job 'j-2608301948074764bee9dd2f709210d7': queued (progress 0%)
0:01:19 Job 'j-2608301948074764bee9dd2f709210d7': running (progress N/A)
0:01:44 Job 'j-2608301948074764bee9dd2f709210d7': running (progress N/A)
0:02:14 Job 'j-2608301948074764bee9dd2f709210d7': running (progress N/A)
0:02:51 Job 'j-2608301948074764bee9dd2f709210d7': running (progress N/A)
0:03:38 Job 'j-2608301948074764bee9dd2f709210d7': finished (progress 100%)
```

### 2.1.3 Data Sulfur Dioksida ($\text{SO}_2$)

#### Load Data

```
s5_so2 = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.31,
        "south": -7.785,
        "east": 112.62,
        "north": -7.362,
    },
    bands=["SO2"],
)

s5_so2 = s5_so2.aggregate_temporal_period(reducer="mean", period="day")
s5_so2 = s5_so2.aggregate_spatial(reducer="mean", geometries=aoi)

job_so2 = s5_so2.execute_batch(
    title="Mojokerto SO2 Extraction", outputfile="mojokerto_so2.nc"
)
```

```
0:00:00 Job 'j-2608301942294709b0c347d9b0bca5e4': send 'start'
0:00:02 Job 'j-2608301942294709b0c347d9b0bca5e4': created (progress 0%)
0:00:07 Job 'j-2608301942294709b0c347d9b0bca5e4': queued (progress 0%)
0:00:14 Job 'j-2608301942294709b0c347d9b0bca5e4': queued (progress 0%)
0:00:22 Job 'j-2608301942294709b0c347d9b0bca5e4': queued (progress 0%)
0:00:32 Job 'j-2608301942294709b0c347d9b0bca5e4': queued (progress 0%)
0:00:44 Job 'j-2608301942294709b0c347d9b0bca5e4': running (progress N/A)
0:01:00 Job 'j-2608301942294709b0c347d9b0bca5e4': running (progress N/A)
0:01:20 Job 'j-2608301942294709b0c347d9b0bca5e4': running (progress N/A)
0:01:44 Job 'j-2608301942294709b0c347d9b0bca5e4': running (progress N/A)
0:02:14 Job 'j-2608301942294709b0c347d9b0bca5e4': running (progress N/A)
0:02:52 Job 'j-2608301942294709b0c347d9b0bca5e4': finished (progress 100%)
```

### 2.1.4 Data Metana ($\text{CH}_4$)

#### Load Data

```
s5_ch4 = connection.load_collection(
    "SENTINEL_5P_L2",
    temporal_extent=["2025-08-24", "2026-08-24"],
    spatial_extent={
        "west": 112.31,
        "south": -7.785,
        "east": 112.62,
        "north": -7.362,
    },
    bands=["CH4"],
)

s5_ch4 = s5_ch4.aggregate_temporal_period(reducer="mean", period="day")
s5_ch4 = s5_ch4.aggregate_spatial(reducer="mean", geometries=aoi)

job_ch4 = s5_ch4.execute_batch(
    title="Mojokerto CH4 Extraction", outputfile="mojokerto_ch4.nc"
)
```

```
0:00:00 Job 'j-26083019531846959aa92af1ad13992b': send 'start'
0:00:02 Job 'j-26083019531846959aa92af1ad13992b': queued (progress 0%)
0:00:08 Job 'j-26083019531846959aa92af1ad13992b': queued (progress 0%)
0:00:14 Job 'j-26083019531846959aa92af1ad13992b': queued (progress 0%)
0:00:22 Job 'j-26083019531846959aa92af1ad13992b': queued (progress 0%)
0:00:32 Job 'j-26083019531846959aa92af1ad13992b': queued (progress 0%)
0:00:45 Job 'j-26083019531846959aa92af1ad13992b': queued (progress 0%)
0:01:01 Job 'j-26083019531846959aa92af1ad13992b': running (progress N/A)
0:01:20 Job 'j-26083019531846959aa92af1ad13992b': running (progress N/A)
0:01:44 Job 'j-26083019531846959aa92af1ad13992b': running (progress N/A)
0:02:14 Job 'j-26083019531846959aa92af1ad13992b': running (progress N/A)
0:02:52 Job 'j-26083019531846959aa92af1ad13992b': finished (progress 100%)
```

## 2.2 Eksplorasi Data
### 2.2.1 Visualisasi Data
#### Kode Python Menampilkan Peta

```
import folium

# 1. Definisi AOI Kabupaten Mojokerto
aoi = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "nama": "Kabupaten Mojokerto",
                "provinsi": "Jawa Timur",
            },
            "geometry": {
                "type": "Polygon",
                "coordinates": [
                    [
                        [112.445, -7.362],
                        [112.512, -7.375],
                        [112.57, -7.42],
                        [112.605, -7.51],
                        [112.62, -7.62],
                        [112.595, -7.725],
                        [112.535, -7.785],
                        [112.47, -7.76],
                        [112.395, -7.68],
                        [112.33, -7.595],
                        [112.31, -7.49],
                        [112.345, -7.415],
                        [112.4, -7.38],
                        [112.445, -7.362],
                    ]
                ],
            },
        }
    ],
}

# 2. Inisialisasi peta interaktif di titik tengah Mojokerto (Lat: -7.55, Lon: 112.45)
m = folium.Map(
    location=[-7.55, 112.45],
    zoom_start=11,
    tiles="CartoDB positron",  # Pilihan basemap: "OpenStreetMap", "CartoDB positron", atau "OpenTopoMap"
)

# 3. Poligon AOI ke Peta
folium.GeoJson(
    aoi,
    name="Batas AOI",
    style_function=lambda feature: {
        "fillColor": "#ff7800",
        "color": "#e65100",
        "weight": 2.5,
        "fillOpacity": 0.35,
    },
    tooltip=folium.GeoJsonTooltip(
        fields=["nama", "provinsi"], aliases=["Wilayah:", "Provinsi:"]
    ),
).add_to(m)


folium.LayerControl().add_to(m)
```

#### Gambar Peta

<iframe 
  src="https://andri-24.github.io/peta-aoi-kbtpnmjkrt/peta_aoi_mojokerto.html" 
  width="100%" 
  height="500px" 
  style="border: 1px solid #ccc; border-radius: 8px;">
</iframe>

#### Deskripsi Wilayah: Area of Interest (AOI) Kabupaten Mojokerto

Peta interaktif di atas merepresentasikan batas spasial Area of Interest (AOI) yang digunakan sebagai acuan pemrosesan data penginderaan jauh atmosfer dari satelit Sentinel-5P Level-2 (TROPOMI) melalui platform openEO Copernicus Data Space Ecosystem.
- Nama Wilayah: Kabupaten Mojokerto, Provinsi Jawa Timur, Indonesia
- Geometri Spasial: Poligon batas administratif tertutup (14 titik koordinat verteks)
- Cakupan Bounding Box ($spatial\_extent$):
-- Bujur (Longitude): $112.31^\circ \text{ BT} - 112.62^\circ \text{ BT}$
-- Lintang (Latitude): $7.362^\circ \text{ LS} - 7.785^\circ \text{ LS}$
- Karakteristik Wilayah:
Wilayah kajian mencakup topografi yang bervariasi, mulai dari dataran rendah industri dan permukiman di sisi utara/tengah yang berbatasan langsung dengan poros ekonomi Surabaya–Sidoarjo, hingga kawasan dataran tinggi dan pegunungan di sisi selatan (lereng Gunung Arjuno-Welirang dan Anjasmoro).

### 2.2.2 Visualisasi Data Grafik(selama 30 hari)
#### CO
- Gambar Grafik

![image](https://hackmd.io/_uploads/ry3xbGGOfe.png)

- Tampilan CSV (20 Data teratas)

```
Konversi berhasil! Berikut 20 data teratas:
            t  feature        CO     lat        lon feature_names
0  2025-08-24        0  0.031929 -7.5525  112.46635     feature_0
1  2025-08-25        0  0.032055 -7.5525  112.46635     feature_0
2  2025-08-26        0  0.030631 -7.5525  112.46635     feature_0
3  2025-08-27        0  0.030621 -7.5525  112.46635     feature_0
4  2025-08-28        0  0.025088 -7.5525  112.46635     feature_0
5  2025-08-29        0  0.029343 -7.5525  112.46635     feature_0
6  2025-08-30        0  0.027159 -7.5525  112.46635     feature_0
7  2025-08-31        0  0.023147 -7.5525  112.46635     feature_0
8  2025-09-01        0  0.028392 -7.5525  112.46635     feature_0
9  2025-09-02        0  0.022924 -7.5525  112.46635     feature_0
10 2025-09-03        0  0.025745 -7.5525  112.46635     feature_0
11 2025-09-04        0  0.025416 -7.5525  112.46635     feature_0
12 2025-09-05        0  0.030017 -7.5525  112.46635     feature_0
13 2025-09-06        0  0.029885 -7.5525  112.46635     feature_0
14 2025-09-07        0  0.030773 -7.5525  112.46635     feature_0
15 2025-09-08        0  0.025960 -7.5525  112.46635     feature_0
16 2025-09-09        0  0.032209 -7.5525  112.46635     feature_0
17 2025-09-10        0  0.029978 -7.5525  112.46635     feature_0
18 2025-09-11        0  0.026548 -7.5525  112.46635     feature_0
19 2025-09-12        0  0.030527 -7.5525  112.46635     feature_0
```

#### NO2
- Gambar Grafik

![grafik CO](https://hackmd.io/_uploads/SJV1WQfuMe.png)

- Tampilan CSV (20 Data teratas)

```
Konversi berhasil! Berikut 20 data teratas:
            t  feature       NO2     lat        lon feature_names
0  2025-08-24        0  0.000037 -7.5525  112.46635     feature_0
1  2025-08-25        0  0.000050 -7.5525  112.46635     feature_0
2  2025-08-26        0  0.000081 -7.5525  112.46635     feature_0
3  2025-08-27        0  0.000050 -7.5525  112.46635     feature_0
4  2025-08-30        0  0.000036 -7.5525  112.46635     feature_0
5  2025-08-31        0  0.000021 -7.5525  112.46635     feature_0
6  2025-09-01        0  0.000034 -7.5525  112.46635     feature_0
7  2025-09-02        0  0.000018 -7.5525  112.46635     feature_0
8  2025-09-03        0  0.000025 -7.5525  112.46635     feature_0
9  2025-09-04        0  0.000027 -7.5525  112.46635     feature_0
10 2025-09-05        0  0.000050 -7.5525  112.46635     feature_0
11 2025-09-06        0  0.000045 -7.5525  112.46635     feature_0
12 2025-09-07        0  0.000080 -7.5525  112.46635     feature_0
13 2025-09-08        0  0.000097 -7.5525  112.46635     feature_0
14 2025-09-10        0  0.000050 -7.5525  112.46635     feature_0
15 2025-09-11        0  0.000028 -7.5525  112.46635     feature_0
16 2025-09-12        0  0.000047 -7.5525  112.46635     feature_0
17 2025-09-13        0  0.000047 -7.5525  112.46635     feature_0
18 2025-09-14        0  0.000047 -7.5525  112.46635     feature_0
19 2025-09-15        0  0.000056 -7.5525  112.46635     feature_0
```


#### SO2
- Gambar Grafik

![image](https://hackmd.io/_uploads/B1bSWGzuze.png)

- Tampilan CSV (20 Data teratas)

```
Konversi berhasil! Berikut 20 data teratas:
            t  feature       SO2     lat        lon feature_names
0  2025-08-24        0  0.000332 -7.5525  112.46635     feature_0
1  2025-08-25        0  0.000856 -7.5525  112.46635     feature_0
2  2025-08-26        0  0.000899 -7.5525  112.46635     feature_0
3  2025-08-27        0  0.000344 -7.5525  112.46635     feature_0
4  2025-08-29        0  0.000012 -7.5525  112.46635     feature_0
5  2025-08-30        0  0.000188 -7.5525  112.46635     feature_0
6  2025-08-31        0  0.000101 -7.5525  112.46635     feature_0
7  2025-09-01        0  0.000227 -7.5525  112.46635     feature_0
8  2025-09-02        0  0.000066 -7.5525  112.46635     feature_0
9  2025-09-03        0  0.000080 -7.5525  112.46635     feature_0
10 2025-09-04        0  0.000321 -7.5525  112.46635     feature_0
11 2025-09-05        0  0.000292 -7.5525  112.46635     feature_0
12 2025-09-06        0  0.000084 -7.5525  112.46635     feature_0
13 2025-09-07        0 -0.000097 -7.5525  112.46635     feature_0
14 2025-09-08        0  0.000373 -7.5525  112.46635     feature_0
15 2025-09-09        0  0.000003 -7.5525  112.46635     feature_0
16 2025-09-10        0  0.000208 -7.5525  112.46635     feature_0
17 2025-09-11        0  0.000129 -7.5525  112.46635     feature_0
18 2025-09-12        0  0.000259 -7.5525  112.46635     feature_0
19 2025-09-13        0  0.000159 -7.5525  112.46635     feature_0


```

#### CH4
- Gambar Grafik

![image](https://hackmd.io/_uploads/BkbLWMMdfx.png)

- Tampilan CSV (20 Data teratas)

```
Konversi berhasil! Berikut 20 data teratas:
            t  feature          CH4     lat        lon feature_names
0  2025-08-30        0  1890.787406 -7.5525  112.46635     feature_0
1  2025-09-02        0  1887.104040 -7.5525  112.46635     feature_0
2  2025-09-03        0  1873.186188 -7.5525  112.46635     feature_0
3  2025-09-04        0  1856.093628 -7.5525  112.46635     feature_0
4  2025-09-13        0  1887.547485 -7.5525  112.46635     feature_0
5  2025-09-19        0  1905.868782 -7.5525  112.46635     feature_0
6  2025-09-28        0  1843.422748 -7.5525  112.46635     feature_0
7  2025-10-05        0  1892.076318 -7.5525  112.46635     feature_0
8  2025-10-14        0  1895.504198 -7.5525  112.46635     feature_0
9  2025-10-15        0  1892.995190 -7.5525  112.46635     feature_0
10 2025-10-16        0  1890.634595 -7.5525  112.46635     feature_0
11 2025-10-17        0  1855.296387 -7.5525  112.46635     feature_0
12 2026-02-04        0  1889.200663 -7.5525  112.46635     feature_0
13 2026-04-05        0  1908.433716 -7.5525  112.46635     feature_0
14 2026-04-13        0  1889.421997 -7.5525  112.46635     feature_0
15 2026-04-14        0  1884.481179 -7.5525  112.46635     feature_0
16 2026-04-15        0  1899.279175 -7.5525  112.46635     feature_0
17 2026-04-16        0  1876.674805 -7.5525  112.46635     feature_0
18 2026-04-25        0  1834.913489 -7.5525  112.46635     feature_0
19 2026-04-26        0  1903.294922 -7.5525  112.46635     feature_0
```

### 2.2.3 Indetifikasi Outlier
### 2.2.4 Indetifikasi Missing Value
### 2.2.5 Identifikasi Noise Data