---
title: Fitur-Fitur TSFEL

---

# Fitur-Fitur TSFEL

## 1. Temporal Domain

- Absolute Energy
Mengukur total energi sinyal absolut tanpa normalisasi panjang data.

$$\text{Energy} = \sum_{i=0}^{N-1} x_i^2$$

- Area Under the Curve (AUC)
Menghitung luas total di bawah kurva sinyal menggunakan aturan trapesium numerik dengan interval $\Delta t = 1/f_s$.

$$\text{AUC} = \sum_{i=0}^{N-2} \frac{x_i + x_{i+1}}{2} \Delta t$$

- Autocorrelation
Mengukur korelasi sinyal dengan dirinya sendiri pada lag $k$ tertentu.

$$R(k) = \frac{1}{N-k} \sum_{i=0}^{N-1-k} (x_i - \mu)(x_{i+k} - \mu)$$

- Centroid (Temporal)
Menentukan titik pusat massa sinyal dalam domain waktu.

$$C_t = \frac{\sum_{i=0}^{N-1} i \cdot x_i}{\sum_{i=0}^{N-1} x_i}$$

- Cumulative Sum (Cumsum)
Fitur yang merepresentasikan deret akumulasi titik data: $S_k = \sum_{i=0}^{k} x_i$. Metrik turunan dari kurva akumulasi ini mencakup kemiringan (slope) atau luasnya.

- Distance
Menghitung total panjang lintasan atau perpindahan 1D yang dilalui oleh sinyal antar titik berurutan.

$$D = \sum_{i=0}^{N-2} \vert{}x_{i+1} - x_i\vert{}$$

- Entropy (Temporal / Approximate / Sample)
Mengukur kompleksitas atau ketidakteraturan sinyal. Sebagai representasi Shannon Entropy pada probabilitas diskret $p(x_i)$:

$$H(x) = -\sum_{i} p(x_i) \log_2 p(x_i)$$

- Mean Absolute Diff (MADiff)
Rata-rata selisih absolut antara sampel data yang berurutan.

$$\text{MADiff} = \frac{1}{N-1} \sum_{i=0}^{N-2} \vert{}x_{i+1} - x_i\vert{}$$

- Mean Differences (MDiff)
Rata-rata perubahan sinyal berurutan (mencerminkan tren derivatif pertama).

$$\text{MDiff} = \frac{1}{N-1} \sum_{i=0}^{N-2} (x_{i+1} - x_i) = \frac{x_{N-1} - x_0}{N-1}$$

- Median Absolute Diff (MedADiff)
Nilai tengah dari selisih mutlak berurutan:

$$\text{MedADiff} = \text{median}(\vert{}x_{i+1} - x_i\vert{}), \quad i \in [0, N-2]$$

- Median Differences (MedDiff)
Nilai tengah dari perubahan berurutan:

$$\text{MedDiff} = \text{median}(x_{i+1} - x_i), \quad i \in [0, N-2]$$

- Negative / Positive Turning Points
Jumlah pembalikan arah lokal sinyal.
Puncak positif: $x_{i-1} < x_i$ dan $x_i > x_{i+1}$
Lembah negatif: $x_{i-1} > x_i$ dan $x_i < x_{i+1}$

- Peak to Peak Distance
Rentang dinamis antara amplitudo maksimum dan minimum sinyal.

$$\text{PTP} = \max(x) - \min(x)$$

- Root Mean Square (RMS)
Nilai efektif besaran magnitudo sinyal.

$$\text{RMS} = \sqrt{\frac{1}{N} \sum_{i=0}^{N-1} x_i^2}$$

- Slope
Kemiringan garis regresi linier terhadap waktu ($t = [0, 1, \dots, N-1]$).$$\beta = \frac{\sum_{i=0}^{N-1} (t_i - \bar{t})(x_i - \mu)}{\sum_{i=0}^{N-1} (t_i - \bar{t})^2}$$

- Zero Crossing Rate (ZCR)Frekuensi berapa kali sinyal memotong atau melewati nilai nol.

$$\text{ZCR} = \frac{1}{2(N-1)} \sum_{i=0}^{N-2} \vert{}\text{sgn}(x_{i+1}) - \text{sgn}(x_i)\vert{}$$

- Mean Crossing Rate (MCR)
Frekuensi sinyal memotong nilai rata-ratanya ($\mu$). Dihitung dengan rumus yang sama seperti ZCR terhadap sinyal terpusat $(x_i - \mu)$.

## 2. Statistical Domain

Domain statistik merangkum distribusi amplitudo dan probabilitas data deret waktu tanpa mempertimbangkan urutan temporalnya.

- Mean ($\mu$)
Nilai rata-rata aritmetika.

$$\mu = \frac{1}{N} \sum_{i=0}^{N-1} x_i$$

- Standard Deviation ($\sigma$) & Variance ($\sigma^2$)
Tingkat dispersi nilai di sekitar rata-rata.

$$\sigma^2 = \frac{1}{N-1} \sum_{i=0}^{N-1} (x_i - \mu)^2, \quad \sigma = \sqrt{\sigma^2}$$

- Skewness
Asimetri kurva distribusi probabilitas terhadap rata-rata.

$$\text{Skewness} = \frac{\frac{1}{N} \sum_{i=0}^{N-1} (x_i - \mu)^3}{\sigma^3}$$

- Kurtosis
Ketajaman puncak dan ketebalan ekor (tailedness) distribusi data.

$$\text{Kurtosis} = \frac{\frac{1}{N} \sum_{i=0}^{N-1} (x_i - \mu)^4}{\sigma^4}$$

- Median
Titik tengah data setelah diurutkan secara menaik ($x_{(0)} \le x_{(1)} \le \dots \le x_{(N-1)}$).

- Maximum & Minimum
Nilai ekstrem dari deret waktu: $x_{\max} = \max(x)$ dan $x_{\min} = \min(x)$.

- Interquartile Range (IQR)Dispersi statistik yang merepresentasikan rentang 50% data tengah.

$$\text{IQR} = Q_3 - Q_1$$

- Quantiles / Percentiles
Nilai ambang $q \in [0, 1]$ yang membagi fraksi data di bawahnya.Mean Absolute Deviation (MAD)Rata-rata deviasi absolut terhadap nilai mean.

$$\text{MAD} = \frac{1}{N} \sum_{i=0}^{N-1} \vert{}x_i - \mu\vert{}$$

- Median Absolute Deviation (MedAD)Ukuran dispersi yang tahan terhadap pencilan (outliers).

$$\text{MedAD} = \text{median}(\vert{}x_i - \text{median}(x)\vert{})$$

- Histogram FeaturesFrekuensi relatif atau kerapatan data dalam $B$ bins interval amplitudo.

## 3. Spectral Domain

Domain spektral mengubah sinyal ke representasi frekuensi melalui transformasi Fourier ($X_k = \text{FFT}(x)$) dengan magnitudo spektrum daya $S_k = \vert{}X_k\vert{}^2$ pada frekuensi $f_k$.

- Fundamental Frequency ($f_0$)Frekuensi dasar sinyal periodik (biasanya puncak spektral signifikan pertama atau via autokorelasi).Spectral CentroidTitik pusat massa dari spektrum frekuensi (mencerminkan brightness atau sebaran frekuensi tinggi/rendah).

$$C_s = \frac{\sum_{k} f_k S_k}{\sum_{k} S_k}$$

- Spectral Spread / VarianceLebar sebaran frekuensi di sekitar centroid spektral.$$\text{Spread} = \sqrt{\frac{\sum_{k} (f_k - C_s)^2 S_k}{\sum_{k} S_k}}$$

- Spectral Skewness & KurtosisMomen ke-3 dan ke-4 spektrum frekuensi yang dinormalisasi oleh simpangan baku spektral.

$$\text{Skewness}_s = \frac{\sum_{k} \left(\frac{f_k - C_s}{\text{Spread}}\right)^3 S_k}{\sum_{k} S_k}, \quad \text{Kurtosis}_s = \frac{\sum_{k} \left(\frac{f_k - C_s}{\text{Spread}}\right)^4 S_k}{\sum_{k} S_k}$$

- Spectral Roll-offFrekuensi $f_R$ di mana akumulasi daya spektral mencapai persentase tertentu (umumnya 85% atau 95%) dari total daya.

$$\sum_{k=0}^{R} S_k = \alpha \sum_{k} S_k, \quad \alpha \in [0.85, 0.95]$$

- Spectral DecreasePenurunan energi spektrum secara progresif terhadap kenaikan frekuensi.

$$\text{Decrease} = \frac{1}{\sum_{k=1}^{M-1} S_k} \sum_{k=1}^{M-1} \frac{S_k - S_0}{k}$$

- Spectral Slope
Kemiringan garis regresi linier dari magnitudo spektral terhadap frekuensi.Spectral Flatness (Wiener Entropy)Rasio rata-rata geometrik terhadap rata-rata aritmetika dari spektrum daya. Bernilai mendekati 1 untuk white noise dan mendekati 0 untuk nada murni.

$$\text{Flatness} = \frac{\exp\left(\frac{1}{M} \sum_{k=0}^{M-1} \ln S_k\right)}{\frac{1}{M} \sum_{k=0}^{M-1} S_k}$$

- Spectral Entropy
Mengukur keragaman distribusi spektrum daya dinormalisasi $P_k = \frac{S_k}{\sum_j S_j}$.

$$H_s = -\sum_{k} P_k \log_2 P_k$$

- Spectral Distance
Jarak kumulatif antar komponen magnitudo frekuensi berturutan:

$$D_s = \sum_k \vert{}S_{k+1} - S_k\vert{}$$

- Power Bandwidth / Spectral Band PowerTotal daya atau fraksi daya dalam rentang frekuensi spesifik $[f_a, f_b]$.

$$P_{[f_a, f_b]} = \sum_{f_k = f_a}^{f_b} S_k$$

- Linear Prediction Coefficients (LPC)
Koefisien $\alpha_i$ dari model autoregressive yang memprediksi nilai $x_n$ berdasarkan $p$ sampel sebelumnya:

$$\hat{x}_n = -\sum_{i=1}^{p} \alpha_i x_{n-i}$$

- Mel-Frequency Cepstral Coefficients (MFCC)Representasi spektral yang memetakan logaritmik daya pada skala frekuensi Mel, lalu diterapkan Discrete Cosine Transform (DCT):

$$\text{Mel}(f) = 2595 \log_{10}\left(1 + \frac{f}{700}\right)$$

- Wavelet Features (Energy / Absolute Mean)
Daya dan nilai rata-rata mutlak dari koefisien dekomposisi Wavelet Transform (DWT) pada skala atau sub-band tertentu:

$$E_W = \sum_j \vert{}c_j\vert{}^2$$