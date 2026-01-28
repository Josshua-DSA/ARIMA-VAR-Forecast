# ARIMA-VAR Forecasting untuk IHSG

Analisis time series komprehensif untuk memprediksi pergerakan Indeks Harga Saham Gabungan (IHSG) menggunakan model ARIMA dan VAR
nb: Proyek dimulai 4 November 2025

## Deskripsi

Proyek ini merupakan analisis statistik untuk memprediksi pergerakan IHSG menggunakan dua pendekatan:
- **ARIMA** (AutoRegressive Integrated Moving Average) - Model univariate
- **VAR** (Vector Autoregression) - Model multivariate

Analisis dilakukan menggunakan data historis IHSG periode 2020-2025 yang mencakup variabel harga pembukaan, penutupan, tertinggi, terendah, volume perdagangan, dan return harian.

## Tujuan

1. Mengembangkan model forecasting yang akurat untuk IHSG
2. Membandingkan performa model univariate (ARIMA) vs multivariate (VAR)
3. Mengidentifikasi pola musiman dan tren jangka panjang dalam pergerakan IHSG
4. Memberikan insight hubungan dinamis antar variabel pasar saham Indonesia

## Dataset

| Variabel | Deskripsi | Format Awal | Format Akhir |
|----------|-----------|-------------|--------------|
| Tanggal | Tanggal perdagangan | DD-MM-YYYY | DatetimeIndex |
| Terakhir | Harga penutupan | String (`"5.159,45"`) | Float |
| Pembukaan | Harga pembukaan | String | Float |
| Tertinggi | Harga tertinggi | String | Float |
| Terendah | Harga terendah | String | Float |
| Vol. | Volume perdagangan | String (`"10,14B"`) | Float (miliar) |
| Perubahan% | Persentase perubahan | String (`"0,87%"`) | Float |

**Periode Data:** 2020-11-03 hingga 2025-11-03 (1.207 observasi harian)

## Metodologi

### 1. Model ARIMA

Model univariate menggunakan variabel `Close` (harga penutupan):

- **Uji Stasioneritas:** ADF Test (differencing d=1 diperlukan)
- **Identifikasi Parameter:** Plot ACF/PACF untuk parameter p dan q
- **Grid Search:** Optimasi kombinasi (p,d,q) berdasarkan AIC
- **Model Terbaik:** ARIMA(0,1,3) dengan seasonal component (1,1,1,5)

### 2. Model VAR

Model multivariate menggunakan 6 variabel endogen:

- `Close` - Harga penutupan
- `Open` - Harga pembukaan  
- `High` - Harga tertinggi
- `Low` - Harga terendah
- `Vol_bil` - Volume perdagangan (miliar)
- `Ret_pct` - Return harian (%)

**Proses:**
- Uji stasioneritas setiap variabel (differencing d=1)
- Seleksi lag optimal menggunakan AIC/BIC/FPE
- Lag optimal: 4
- Model: VAR(4) dengan 6 variabel

## Alur Analisis

### 1. Pra-pemrosesan Data
- Konversi format string ke numerik
- Penanganan missing values (3 observasi pada Vol_bil)
- Pembuatan variabel Return harian

### 2. Uji Stasioneritas
- ADF Test → data asli tidak stasioner (p-value > 0.05)
- Differencing pertama (d=1) → semua variabel stasioner

### 3. Pemodelan ARIMA
- Grid search parameter (p,d,q) dengan p,q ∈ [0,3], d ∈ [0,1]
- Model ARIMA(0,1,3) terpilih (AIC minimum)
- Diagnostik residual → white noise (Ljung-Box p-value > 0.05)

### 4. Pemodelan VAR
- Seleksi lag optimal: lag 4 (AIC)
- Uji stabilitas: semua roots dalam lingkaran satuan
- Uji Granger Causality → hubungan dinamis antar variabel
- Diagnostik residual → mendekati white noise

### 5. Forecasting
- Peramalan 150 hari ke depan (kedua model)
- Visualisasi perbandingan forecast vs data aktual
- Analisis error (RMSE, MAE)

## Hasil Pemodelan

### ARIMA Model

- **Parameter:** ARIMA(0,1,3) + seasonal (1,1,1,5)
- **AIC:** -4523.87
- **Performa:**
  - Menangkap tren jangka panjang IHSG
  - Forecast menunjukkan kenaikan moderat 150 hari
  - Error kecil pada short-term (<30 hari)

### VAR Model

- **Lag Optimal:** 4
- **Stabilitas:** Semua roots dalam lingkaran satuan
- **Insight:**
  - Harga pembukaan (`Open`) berpengaruh signifikan ke `Close`
  - Volume (`Vol_bil`) berhubungan negatif dengan volatilitas jangka pendek
  - Return (`Ret_pct`) dipengaruhi kuat oleh pergerakan harga L1-L4
- **Performa:**
  - Forecast lebih konsisten untuk semua variabel
  - Korelasi positif harga-volume pada tren naik

## 🛠️ Instalasi

```bash
# Clone repository
git clone https://github.com/username/ARIMA-VAR-Forecast.git
cd ARIMA-VAR-Forecast

# Install dependencies
pip install -r requirements.txt
```

## Dependencies

```
python >= 3.10
statsmodels >= 0.14.0
pandas >= 2.0.3
numpy >= 1.24.0
matplotlib >= 3.7.0
seaborn >= 0.12.0
scipy >= 1.10.0
```

## Usage

```python
# Import libraries
import pandas as pd
from statsmodels.tsa.arima.model import ARIMA
from statsmodels.tsa.api import VAR

# Load data
df = pd.read_csv('data/IHSG_data.csv')

# Jalankan ARIMA model
# [kode contoh implementasi]

# Jalankan VAR model
# [kode contoh implementasi]
```

## Customisasi

- **Analisis Sektoral:** Ganti data IHSG dengan indeks sektoral (contoh: IDX:ENERGY)
- **Frekuensi Berbeda:** Ubah resampling dari harian ke mingguan/bulanan
- **Model Hybrid:** Kombinasi dengan machine learning (contoh: LSTM untuk residual ARIMA)

## Disclaimer

> **Peringatan:** Forecasting pasar saham memiliki ketidakpastian tinggi. Hasil analisis ini **BUKAN rekomendasi investasi** dan hanya untuk keperluan **edukasi/riset**.

### Keterbatasan Model

- Model tidak mempertimbangkan faktor eksternal (kebijakan moneter, geopolitik, pandemi)
- Asumsi stasioneritas mungkin tidak berlaku pada kondisi pasar ekstrem
- Data historis tidak selalu mencerminkan pola masa depan
- **Update Berkala:** Model perlu direkalibrasi setiap 3-6 bulan

## Acknowledgments

- Data source: [Investing.com / Yahoo Finance]

---

**Jika proyek ini bermanfaat, berikan star di repository ini!**

**Feedback & Kontribusi:** Silakan buka issue atau pull request untuk saran dan perbaikan.

*Dikembangkan dengan untuk edukasi analisis time series di Indonesia*
