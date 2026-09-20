---
title: Clustering

---

# Clustering

## 1. Pengertian Clustering

**Clustering** (Pengelompokan) adalah salah satu teknik utama dalam *Unsupervised Machine Learning* yang bertujuan untuk membagi himpunan data (*dataset*) menjadi beberapa kelompok (*cluster*). Data dalam satu kelompok yang sama memiliki tingkat kemiripan (*similarity*) yang tinggi, sedangkan data antar-kelompok yang berbeda memiliki tingkat perbedaan (*dissimilarity*) yang signifikan.

Berbeda dengan *Supervised Learning* (seperti Klasifikasi), *Clustering* tidak membutuhkan label atau target variabel sebelumnya.

### Kegunaan & Contoh Aplikasi:

* **Segmentasi Pasar/Pelanggan:** Membagi pelanggan berdasarkan pola pembelian atau demografi.

* **Deteksi Anomali:** Mengidentifikasi transaksi keuangan yang mencurigakan (outlier).

* **Kompresi Gambar:** Mengelompokkan warna-warna serupa untuk mengurangi ukuran berkas.

* **Sistem Rekomendasi:** Mengelompokkan pengguna dengan selera film atau produk yang mirip.

## 2. Clustering Menggunakan K-Means

**K-Means Clustering** adalah salah satu algoritma pengelompokan berbasis jarak (*distance-based*) yang paling populer dan efisien. Parameter $K$ mewakili jumlah kelompok (*cluster*) yang ditentukan secara eksplisit oleh pengguna sebelum proses pelatihan dimulai.

### Konsep Dasar:

* **Centroid:** Titik pusat dari suatu *cluster*.

* **Variansi Internal (*Inertia*):** Total jarak kuadrat antara setiap data poin dengan *centroid* kelompoknya. Tujuan K-Means adalah meminimalkan variansi ini.

## 3. Algoritma Langkah demi Langkah K-Means

Proses iteratif K-Means bekerja melalui tahapan berikut:

1. **Inisialisasi:** Tentukan jumlah $K$ *cluster* dan pilih $K$ titik secara acak dari data sebagai *centroid* awal.

2. **Alokasi Cluster (Assignment Step):** Hitung jarak setiap titik data ke seluruh *centroid*. Masukkan data poin ke *cluster* dengan *centroid* terdekat.

3. **Pembaruan Centroid (Update Step):** Hitung ulang posisi *centroid* untuk setiap *cluster* dengan mengambil rata-rata (*mean*) posisi seluruh data poin yang terikat pada *cluster* tersebut.

4. **Konvergensi:** Ulangi langkah 2 dan 3 sampai salah satu kondisi terpenuhi:

   * Posisi *centroid* tidak lagi berubah (atau perubahannya di bawah toleransi).

   * Keanggotaan *cluster* pada setiap titik data tidak berubah.

   * Mencapai jumlah iterasi maksimum.

## 4. Persamaan Matematis (Rumus)

### A. Jarak Euclidean (Euclidean Distance)

Metrik standar untuk mengukur jarak antara data poin $p = (x_1, y_1)$ dan centroid $c = (x_2, y_2)$ dalam ruang 2-dimensi:

$$
d(p, c) = \sqrt{(x_1 - x_2)^2 + (y_1 - y_2)^2}
$$

Untuk data dengan $n$-dimensi:

$$
d(p, c) = \sqrt{\sum_{i=1}^{n} (p_i - c_i)^2}
$$

### B. Pembaruan Centroid

Posisi baru dari centroid $\mu_k$ untuk cluster $C_k$ dihitung dengan rata-rata aritmetika seluruh titik $x_i$ dalam kelompok tersebut:

$$
\mu_k = \frac{1}{\vert{}C_k\vert{}} \sum_{x_i \in C_k} x_i
$$

Di mana $\vert{}C_k\vert{}$ adalah jumlah total titik data dalam cluster $k$.

## 5. Contoh Perhitungan Manual (Studi Kasus Sederhana)

Misalkan kita memiliki 4 data pelanggan berdasarkan dua fitur: **Usia (**$X$**)** dan **Skor Belanja (**$Y$**)**.

| Data | Pelanggan | Usia ($X$) | Skor Belanja ($Y$) | 
 | ----- | ----- | ----- | ----- | 
| **A** | Pelanggan 1 | 2 | 2 | 
| **B** | Pelanggan 2 | 2 | 6 | 
| **C** | Pelanggan 3 | 8 | 6 | 
| **D** | Pelanggan 4 | 8 | 8 | 

Kita tentukan jumlah kelompok $K = 2$.

### **Iterasi 1**

#### **Langkah 1: Inisialisasi Centroid**

Secara acak, kita pilih **A** dan **C** sebagai centroid awal:

* Centroid 1 ($C_1$) = A = $(2, 2)$

* Centroid 2 ($C_2$) = C = $(8, 6)$

#### **Langkah 2: Hitung Jarak Euclidean & Alokasi Cluster**

1. **Untuk Titik A** $(2, 2)$**:**

   * $d(A, C_1) = \sqrt{(2-2)^2 + (2-2)^2} = \sqrt{0 + 0} = 0$

   * $d(A, C_2) = \sqrt{(2-8)^2 + (2-6)^2} = \sqrt{(-6)^2 + (-4)^2} = \sqrt{36 + 16} = \sqrt{52} \approx 7.21$

   * **Hasil:** Dekat ke $C_1 \rightarrow$ **Cluster 1**

2. **Untuk Titik B** $(2, 6)$**:**

   * $d(B, C_1) = \sqrt{(2-2)^2 + (6-2)^2} = \sqrt{0 + 16} = 4$

   * $d(B, C_2) = \sqrt{(2-8)^2 + (6-6)^2} = \sqrt{(-6)^2 + 0} = 6$

   * **Hasil:** Dekat ke $C_1 \rightarrow$ **Cluster 1**

3. **Untuk Titik C** $(8, 6)$**:**

   * $d(C, C_1) = \sqrt{(8-2)^2 + (6-2)^2} = \sqrt{36 + 16} = 7.21$

   * $d(C, C_2) = \sqrt{(8-8)^2 + (6-6)^2} = 0$

   * **Hasil:** Dekat ke $C_2 \rightarrow$ **Cluster 2**

4. **Untuk Titik D** $(8, 8)$**:**

   * $d(D, C_1) = \sqrt{(8-2)^2 + (8-2)^2} = \sqrt{36 + 36} = \sqrt{72} \approx 8.49$

   * $d(D, C_2) = \sqrt{(8-8)^2 + (8-6)^2} = \sqrt{0 + 4} = 2$

   * **Hasil:** Dekat ke $C_2 \rightarrow$ **Cluster 2**

**Hasil Anggota Cluster Iterasi 1:**

* **Cluster 1:** $\{A, B\}$

* **Cluster 2:** $\{C, D\}$

#### **Langkah 3: Hitung Ulang Centroid**

* **Centroid Baru** $C_1$ (Rata-rata titik A $(2,2)$ dan B $(2,6)$):
  

  $$
  C_1 = \left( \frac{2+2}{2}, \frac{2+6}{2} \right) = (2, 4)
  $$

* **Centroid Baru** $C_2$ (Rata-rata titik C $(8,6)$ dan D $(8,8)$):
  

  $$
  C_2 = \left( \frac{8+8}{2}, \frac{6+8}{2} \right) = (8, 7)
  $$

### **Iterasi 2**

Centroid terkini: $C_1 = (2, 4)$ dan $C_2 = (8, 7)$

1. **Titik A** $(2, 2)$**:**

   * $d(A, C_1) = \sqrt{(2-2)^2 + (2-4)^2} = \sqrt{4} = 2$

   * $d(A, C_2) = \sqrt{(2-8)^2 + (2-7)^2} = \sqrt{36 + 25} = \sqrt{61} \approx 7.81$

   * **Cluster 1**

2. **Titik B** $(2, 6)$**:**

   * $d(B, C_1) = \sqrt{(2-2)^2 + (6-4)^2} = \sqrt{4} = 2$

   * $d(B, C_2) = \sqrt{(2-8)^2 + (6-7)^2} = \sqrt{36 + 1} = \sqrt{37} \approx 6.08$

   * **Cluster 1**

3. **Titik C** $(8, 6)$**:**

   * $d(C, C_1) = \sqrt{(8-2)^2 + (6-4)^2} = \sqrt{36 + 4} = \sqrt{40} \approx 6.32$

   * $d(C, C_2) = \sqrt{(8-8)^2 + (6-7)^2} = \sqrt{1} = 1$

   * **Cluster 2**

4. **Titik D** $(8, 8)$**:**

   * $d(D, C_1) = \sqrt{(8-2)^2 + (8-4)^2} = \sqrt{36 + 16} = \sqrt{52} \approx 7.21$

   * $d(D, C_2) = \sqrt{(8-8)^2 + (8-7)^2} = \sqrt{1} = 1$

   * **Cluster 2**

**Hasil Anggota Cluster Iterasi 2:**

* **Cluster 1:** $\{A, B\}$

* **Cluster 2:** $\{C, D\}$

Karena keanggotaan cluster pada **Iterasi 2** sama persis dengan **Iterasi 1**, maka algoritma telah **KONVERGEN** dan perhitungan dihentikan.

## 6. Penentuan Jumlah Cluster Optimal (Elbow Method)

Salah satu tantangan utama pada K-Means adalah memilih nilai $K$ terbaik. Teknik paling populer adalah **Elbow Method**:

1. Jalankan K-Means untuk serangkaian nilai $K$ (misal $K = 1$ hingga $K = 10$).

2. Hitung nilai **WCSS** (*Within-Cluster Sum of Squares*) untuk setiap $K$:
   

   $$
   WCSS = \sum_{k=1}^{K} \sum_{x_i \in C_k} d(x_i, \mu_k)^2
   $$

3. Buat grafik antara Nilai $K$ (Sumbu X) vs WCSS (Sumbu Y).

4. Pilih nilai $K$ pada titik di mana grafik membentuk "siku" (*elbow*), yaitu ketika penurunan WCSS mulai melambat secara signifikan.

## 7. Kelebihan dan Kekurangan K-Means

### Kelebihan:

* Sederhana, mudah dipahami, dan diimplementasikan.

* Sangat cepat dan efisien secara komputasi untuk data berukuran besar.

* Mudah beradaptasi dengan sampel data baru.

### Kekurangan:

* Sensitif terhadap pemilihan centroid awal (dapat diatasi dengan algoritma **K-Means++**).

* Sensitif terhadap **Outlier** (pencilan data dapat menggeser posisi centroid).

* Sulit menangani *cluster* dengan bentuk non-spherical (bukan lingkaran/bola) atau ukuran yang bervariasi tajam.

* Memerlukan penentuan nilai $K$ secara manual di awal.