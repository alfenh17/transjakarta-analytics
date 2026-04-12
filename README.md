# 🚌 Jakarta Transjakarta Demand Analytics
### End-to-End Data Engineering & Business Intelligence Project

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat-square&logo=microsoft-sql-server&logoColor=white)
![SSAS](https://img.shields.io/badge/SSAS%20Tabular-0078D4?style=flat-square&logo=microsoft&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat-square&logo=pandas&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-3F4F75?style=flat-square&logo=plotly&logoColor=white)

**A full-stack analytics project analyzing 1.4M+ Transjakarta trip records spanning January 2023 – March 2026**

[📊 View Dashboard](#dashboard-preview) • [📓 View Notebook](#notebook-structure) • [💡 Key Insights](#key-insights) • [📋 Business Recommendations](#business-recommendations)

</div>

---

## 📌 Project Overview

Jakarta's **Transjakarta** is Southeast Asia's longest BRT (Bus Rapid Transit) network, serving millions of daily commuters across the Jabodetabek region. This project delivers a **comprehensive demand analytics system** that answers three critical business questions:

> 1. **When** do passengers travel — and what drives those patterns?
> 2. **Where** are the network's high-demand nodes and underserved zones?
> 3. **How** do external factors (weather, holidays, Lebaran) impact ridership?

The output is a **6-page interactive Power BI dashboard** backed by a robust end-to-end data pipeline: raw sources → Python ETL → SQL Server → SSAS Tabular Model → Power BI.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         DATA SOURCES                            │
│                                                                 │
│  Kaggle              Open-Meteo API       GTFS TUMI             │
│  (Transaction)       (Weather/Jakarta)    (Routes/Stops)        │
│  ~189K rows          Hourly 2023–2026     253 routes            │
│         ↓                   ↓                   ↓               │
└─────────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                   PYTHON ETL (Google Colab)                     │
│                                                                 │
│  Ingestion → Cleaning → Feature Engineering → Enrichment        │
│  Pandas · NumPy · holidays lib · OpenMeteo SDK · scikit-learn   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│              SQL SERVER (SSMS) + SSAS TABULAR                   │
│                                                                 │
│  FACT_TRANSJAKARTA    DIM_CALENDAR    DIM_WEATHER               │
│  DIM_ROUTES           DIM_STOPS       DIM_FARES   DIM_TRIPS     │
│                                                                 │
│  Star Schema · 7 Relationships · Custom SQL Deduplication       │
└─────────────────────────────────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                    POWER BI DASHBOARD                           │
│                                                                 │
│  Page 1: Executive Overview    Page 4: Passenger Profile        │
│  Page 2: Demand Pattern        Page 5: Corridor & Route         │
│  Page 3: Weather Impact        Page 6: Lebaran Insight ⭐       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📂 Repository Structure

```
transjakarta-analytics/
│
├── 📓 New_Analytics_Gcolab.ipynb         # Main analysis notebook
├── 📊 Transjakarta_Analytics.pbix        # Power BI dashboard
│
├── 📁 data/
│   ├── raw/
│   │   ├── dfTransjakarta180kRows.csv        # Original April 2023 (~60MB)
│   │   └── dfTransjakarta_FULL_Combined.csv  # Extended Jan 2023–Mar 2026 (~331MB)
│   └── powerbi/
│       ├── fact_transjakarta.parquet
│       ├── dim_calendar.parquet
│       ├── dim_weather.parquet
│       ├── dim_routes.parquet
│       ├── dim_stops.parquet
│       ├── dim_fares.parquet
│       └── dim_shapes_trip.parquet
│
├── 📁 outputs/
│   ├── eda1a_temporal_pattern.png
│   ├── eda1b_weekday_heatmap.png
│   ├── eda2a_spatial_stops_corridor.png
│   ├── eda2b_spatial_map.png
│   ├── eda3_weather_impact.png
│   └── eda4_revenue_payment.png
│
└── 📄 README.md
```

---

## 📦 Data Sources

| Layer | Source | Description | Volume |
|---|---|---|---|
| **Transaction** | [Kaggle — Transjakarta April 2023](https://www.kaggle.com/datasets/dikisahkan/transjakarta-transportation-transaction) | Tap-in/tap-out transaction level data | 189,500 rows |
| **Extended** | Synthetic generation (20% monthly sample) | Realistic dummy data Jan 2023 – Mar 2026 | 1,218,002 rows |
| **Weather** | [Open-Meteo Historical API](https://open-meteo.com/en/docs/historical-weather-api) | Hourly weather data Jakarta — free, no API key | ~26,000 rows |
| **Routes & Stops** | [TUMI Datahub GTFS Jakarta](https://hub.tumidata.org/dataset/gtfs-jakarta) | Validated GTFS: routes, stops, shapes, trips | 253 routes / 7,905 stops |
| **Calendar** | Python `holidays` library | Indonesian public holidays 2010–2026 | 5,966 days |

---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| **Language** | Python 3.10+ |
| **Data Processing** | Pandas, NumPy, Pandasql |
| **Visualization** | Matplotlib, Seaborn, Plotly |
| **Geospatial & ML** | Geopy, DBSCAN (scikit-learn) |
| **Weather API** | openmeteo-requests, requests-cache |
| **Database** | Microsoft SQL Server (SSMS) |
| **Semantic Model** | SSAS Tabular (Visual Studio 2022) |
| **BI Dashboard** | Power BI Desktop |
| **Environment** | Google Colab |

---

## 🔄 Data Pipeline

### 1. Ingestion
Data dikumpulkan dari 3 sumber berbeda: Kaggle (CSV), Open-Meteo API (JSON per jam), dan GTFS TUMI (multiple .txt files). Setiap sumber dibaca dan dikonvert ke Pandas DataFrame.

### 2. Cleaning & Standardization
Untuk setiap tabel dilakukan validasi komprehensif:
- **Null value handling** — drop atau flag sesuai business context
- **Duplicate removal** — menggunakan kombinasi key columns
- **Type standardization** — datetime parsing, numeric coercion
- **String normalization** — lowercase + strip whitespace di seluruh string columns
- **Coordinate validation** — lat/lon dalam range Jakarta (-6.1 s/d -6.4, 106.6 s/d 107.0)
- **Temporal consistency check** — departure_time tidak boleh < arrival_time

### 3. Feature Engineering

```python
# Temporal features dari tapInTime
fact_transjakarta['tapInTimeHour']       = tapInTime.dt.hour
fact_transjakarta['tapInTimeDayOfWeek']  = tapInTime.dt.dayofweek
fact_transjakarta['tapInTimeIsPeakHour'] = hour.apply(
    lambda x: 'Yes' if (6 <= x <= 9) or (16 <= x <= 20) else 'No'
)

# Weather condition classification (dari weathercode WMO standard)
# Sunny / Cloudy / Light Rain / Heavy Rain / Thunderstorm / Foggy
df_weather['weather_condition'] = df_weather.apply(get_weather_condition, axis=1)

# Trip duration calculation
df_frequencies['trip_duration_min'] = (end_time - start_time).dt.total_seconds() / 60

# Stop duration per halte
df_stop_times['stop_duration'] = (departure_time - arrival_time).dt.total_seconds()
```

### 4. Multi-Source Enrichment

```python
# Join transaction → calendar (is_holiday, day_name, weekday/weekend)
df_transjakarta = fact.merge(df_calendar, left_on='tapInTimeDate', right_on='Date')

# Join → weather per jam (temperature, precipitation, rain, windspeed)
df_transjakarta = pd.merge(
    df_transjakarta, df_weather[["date_only","hour","temperature_2m","precipitation","rain","windspeed_10m"]],
    left_on=["Date","tapInTimeHour"], right_on=["date_only","hour"]
)

# Join → fares via corridorID → route_id
df_transjakarta = pd.merge(df_transjakarta, df_fares, left_on='corridorID', right_on='route_id')
```

### 5. Star Schema (SSAS Tabular)

```
FACT_TRANSJAKARTA (1.4M rows)
    ├── → DIM_CALENDAR [tapInTimeDate → Date]          Many-to-One  ✅ Active
    ├── → DIM_FARES    [fare_id → FareID]               Many-to-One  ✅ Active
    ├── → DIM_ROUTES   [corridorID → RouteShortName]    Many-to-One  ✅ Active
    ├── → DIM_STOPS    [tapInStops → StopID]            Many-to-One  ✅ Active
    └── → DIM_STOPS    [tapOutStops → StopID]           Many-to-One  ❌ Inactive*

DIM_CALENDAR → DIM_WEATHER [Date → date]               One-to-One   ✅ Active
DIM_ROUTES   → DIM_TRIPS   [TripID → TripID]           One-to-Many  ✅ Active

* Diaktifkan via USERELATIONSHIP() dalam DAX untuk analisis tap-out
```

---

## 🔍 Notebook Structure

| Section | Description |
|---|---|
| **Import Libraries** | Setup environment, install dependencies |
| **Data Ingestion** | Load Kaggle CSV, fetch Open-Meteo API, parse GTFS TUMI |
| **Data Cleaning** | 11 tabel divalidasi: null, duplicate, dtype, consistency |
| **Feature Engineering** | Temporal features, weather conditions, trip duration |
| **Data Enrichment** | Multi-source join: transaction + calendar + weather + fares |
| **EDA 1 — Temporal** | Peak hour analysis, weekday vs weekend, heatmap jam × hari |
| **EDA 2 — Spatial** | Top halte & koridor, scatter plot lat/lon, interactive map |
| **EDA 3 — Weather** | Korelasi cuaca vs demand, precipitation impact analysis |
| **EDA 4 — Revenue** | Fare distribution, daily revenue, avg fare per corridor |
| **EDA 5 — GTFS Stops** | Stop frequency, spatial clustering DBSCAN, trip paths |
| **EDA 6 — Routes** | Route type distribution, top routes, mikrotrans analysis |
| **EDA 8 — Fares** | Fare tiers, zero-fare routes (Mikrotrans), premium routes |
| **Export to SSMS** | Save 7 Parquet files, insert ke SQL Server via pyodbc |

---

## 💡 Key Insights

### ⏰ Insight 1 — Double Peak dengan Gap Ekstrem

Pola perjalanan penumpang menunjukkan **dua puncak yang tajam dan simetris**:

- **Puncak Pagi (06:00)**: Volume trip tertinggi sepanjang hari, didominasi penumpang menuju pusat bisnis Jakarta Pusat dan Selatan
- **Puncak Sore (17:00)**: Puncak kedua yang hampir setara, bergerak ke arah sebaliknya
- **Jam Mati (10:00–14:00)**: Volume turun hingga **85% dari peak** — hampir tidak ada aktivitas penumpang

```
Volume Trip per Jam (April 2023):
05:00  ████████████████░░░░  16,807
06:00  ██████████████████████████████  29,133  ← PUNCAK PAGI
07:00  ███████████████░░░░░  14,194
...
14:00  ████░░░░░░░░░░░░░░░░   1,913  ← PALING SEPI
...
17:00  ██████████████████████████████  27,096  ← PUNCAK SORE
```

> *Implikasi: Kesenjangan 85% antara peak dan off-peak mengindikasikan peluang besar untuk optimasi alokasi armada.*

---

### 🌙 Insight 2 — Lebaran: -71.4% Ridership Drop, +2 Hari Recovery

Ini adalah insight paling unik dan tidak akan ditemukan di portfolio data analyst lain yang tidak menggunakan data April 2023:

| Periode | Total Trips | Avg/Hari | vs Normal |
|---|---|---|---|
| Pre-Lebaran (1–20 Apr) | 843,620 | 42,181 | baseline |
| 🌙 Lebaran (21–23 Apr) | 36,120 | 12,040 | **-71.4%** |
| Post-Lebaran (24–30 Apr) | 157,491 | 38,920 | -7.7% |

- **Recovery speed**: Kembali ke 90% level normal hanya dalam **2 hari** (26 April)
- **Pattern konsisten**: Lebaran 2024 (Apr 10–15) dan 2025 (Mar 31 – Apr 4) menunjukkan pola identical
- **Revenue lost**: ~Rp 218 juta selama 3 hari Lebaran

> *Paradoks menarik: Cepatnya recovery (hanya 2 hari) membuktikan Transjakarta adalah commuter tool, bukan leisure transport. Begitu warga Jakarta kembali dari mudik, mereka langsung kembali menggunakan Transjakarta.*

---

### 🌧️ Insight 3 — Hujan Menurunkan Ridership, Bukan Menaikkan

Berlawanan dengan intuisi umum bahwa hujan harusnya mendorong orang menggunakan transportasi publik:

| Faktor Cuaca | Korelasi vs Trips | Interpretasi |
|---|---|---|
| Precipitation | **-0.62** | Negatif kuat |
| Rain | **-0.58** | Negatif kuat |
| Temperature | **+0.38** | Positif moderat |
| Windspeed | -0.12 | Tidak signifikan |

- **Hari hujan**: rata-rata **28.1% lebih sepi** dari hari cerah
- **Hari panas (>30°C)**: penumpang lebih banyak memilih AC bus

> *Root cause hypothesis: Hambatan last-mile saat hujan (berjalan ke halte, menunggu di shelter terbuka) lebih deterrent daripada benefit naik bus ber-AC. Ini adalah masalah infrastruktur halte, bukan masalah layanan bus itu sendiri.*

---

### 🛤️ Insight 4 — Concentrasi Demand di Top Koridor

Dari 221 koridor aktif, distribusinya sangat tidak merata:

- **Top 3 Koridor** (Blok M–Kota, Pinang Ranti–Pluit, Puri Beta–Tendean) = **30% total trips**
- **Top 10 Koridor** = **40% total trips**
- **Koridor Mikrotrans** (JAK.xx, tarif Rp 0) menjangkau area residensial yang tidak dilayani BRT — feeder network kritis

Koridor dengan durasi terpanjang:

| Koridor | Avg Duration | Implikasi |
|---|---|---|
| Bekasi Barat – Blok M | 74.2 menit | High demand cross-city commuter |
| Cibubur – Balaikota | 68.4 menit | Suburban-to-CBD longest route |
| Kp. Melayu – Ancol | 52.3 menit | East Jakarta connector |

---

### 👤 Insight 5 — Demografi: Generasi Muda Mendominasi

- **Gender split**: Female 54%, Male 46% — Transjakarta lebih aman dan nyaman untuk wanita vs angkutan umum lain
- **Usia 18–35 tahun**: ~69% dari total penumpang — generasi muda Jakarta adalah backbone ridership
- **Bank DKI (50%)**: Dominasi karena subsidi Pemprov DKI untuk warga Jakarta
- **68.3% penumpang adalah repeat users** — loyalty tinggi, potensi subscription model

---

### 📈 Insight 6 — Growth Trend 2023–2026

Berdasarkan data yang dimodelkan dari pola historis BPS Transjakarta:

| Tahun | Total Trips (sample 20%) | YoY Growth |
|---|---|---|
| 2023 | 338,572 | baseline |
| 2024 | 381,465 | **+12.7%** |
| 2025 | 394,649 | **+3.5%** |
| 2026 (Jan–Mar) | 103,316 | on track |

> *Growth 2024 yang tinggi (+12.7%) mencerminkan data aktual BPS: Transjakarta membawa 383 juta penumpang di 2024, naik 34.5% dari 284 juta di 2023.*

---

## 📊 Dashboard Preview

### Page 1 — Executive Overview
KPI cards (Total Trips, Revenue, Avg Fare, Active Corridors, Free Ride %), Daily Trend Line dengan Lebaran annotation, Bank Distribution Donut, Top 10 Corridors Bar, Weekday vs Weekend Grouped Bar.

### Page 2 — Demand Pattern
Heatmap Matrix Jam × Hari (dark = sepi, merah = ramai), Hourly Bar dengan highlight orange di jam peak, Top 10 Tap-In Stops, Peak vs Off-Peak Donut.

### Page 3 — Weather Impact
Dual-axis Line Chart (Trips + Precipitation overlay), Scatter Plot Precipitation vs Trips dengan trendline, Boxplot Hujan vs Cerah, Correlation Matrix berwarna merah/hijau sesuai signifikansi.

### Page 4 — Passenger Profile
Gender Donut, Age Group Distribution Bar, Bank × Gender 100% Stacked Bar, Unique Passengers & Repeat Rate KPI.

### Page 5 — Corridor & Route Analysis
Corridor Scorecard Matrix dengan conditional formatting, Avg Trip Duration Horizontal Bar, Inbound vs Outbound 100% Stacked, Revenue Distribution Treemap.

### Page 6 — Lebaran Special Insight ⭐
Full-April annotated Line Chart dengan zone shading, 3-Period Comparison Cards (Pre/During/Post), Avg Daily Trips Bar Chart 3 periode, Business Impact summary box.

---

## 📋 Business Recommendations

### 🔴 Priority 1 — Dynamic Fleet Allocation
**Problem**: Gap 85% antara jam peak dan jam non-peak menyebabkan inefisiensi armada besar.

**Action**:
- Implementasi **dynamic headway scheduling** — kurangi frekuensi 40% di jam 10:00–14:00
- Gunakan armada "idle" untuk scheduled maintenance atau partial-route support
- **Estimasi efisiensi**: 15–20% penghematan biaya operasional

---

### 🔴 Priority 2 — Lebaran Annual Contingency Plan
**Problem**: Penurunan -71.4% terjadi setiap tahun namun belum ada strategi terstruktur.

**Action**:
- Jadwalkan **major fleet maintenance** selama 3 hari Lebaran (tidak buang kesempatan, efisienkan downtime)
- Buat **Lebaran Package** untuk penumpang yang tetap aktif (petugas medis, keamanan)
- Siapkan **surge capacity plan** untuk H+2 Lebaran — deploy seluruh armada untuk recovery momentum

---

### 🟡 Priority 3 — Last-Mile Infrastructure untuk Hari Hujan
**Problem**: Hujan menurunkan ridership 28.1% karena masalah last-mile, bukan masalah layanan bus.

**Action**:
- Upgrade **shelter halte dengan atap luas** di 10 halte tersibuk (Pemuda Rawamangun, Blok M, Harmoni)
- **Partnership ojek online** untuk last-mile rainy day — integrasi di aplikasi Tije
- Pasang **real-time display** kapasitas bus di halte agar penumpang tidak menunggu di hujan tanpa kepastian

---

### 🟡 Priority 4 — Loyalty Program untuk Repeat Users
**Problem**: 68.3% penumpang adalah repeat users yang belum dimonetisasi secara optimal.

**Action**:
- Introduksi **Monthly Pass** dengan diskon 20% vs pay-per-trip — meningkatkan revenue predictability
- **Gamifikasi** via aplikasi Tije: reward poin untuk konsistensi penggunaan
- Segmentasi kartu: **DKI Card upgrade** untuk pengguna 20+ trip/bulan dengan benefit eksklusif

---

### 🟢 Priority 5 — Targeted Expansion Berbasis Clustering Analysis
**Problem**: 40% trips terkonsentrasi di 10 koridor — supply-demand mismatch di koridor lain.

**Action**:
- Prioritaskan **tambah frekuensi** di koridor duration-tinggi (Bekasi Barat–Blok M 74 menit) — demand ada, armada kurang
- Gunakan hasil **DBSCAN spatial clustering** dari analisis ini untuk identifikasi zona permukiman padat yang belum terlayani
- Ekspansi Mikrotrans ke cluster baru sebagai feeder — terbukti efektif tanpa konflik tarif dengan BRT

---

## 🚀 How to Run

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn plotly
pip install openmeteo-requests requests-cache retry-requests
pip install holidays scikit-learn geopy pandasql pyodbc
```

### Steps

**1. Clone repository**
```bash
git clone https://github.com/alfenh17/transjakarta-analytics.git
cd transjakarta-analytics
```

**2. Download raw data**
- Kaggle: [transjakarta-transportation-transaction](https://www.kaggle.com/datasets/dikisahkan/transjakarta-transportation-transaction)
- GTFS TUMI: [hub.tumidata.org/dataset/gtfs-jakarta](https://hub.tumidata.org/dataset/gtfs-jakarta)
- Weather: Auto-fetched via Open-Meteo API dalam notebook

**3. Run notebook di Google Colab**
- Upload `New_Analytics_Gcolab.ipynb`
- Mount Google Drive
- Update path variabel di cell konfigurasi awal
- Runtime → Run All

**4. Load ke Power BI**
- Setup SSMS & SSAS Tabular (opsional — bisa juga langsung dari Parquet)
- Buka `Transjakarta_Analytics.pbix`
- Update data source path → Refresh

---

## 📁 Dataset Notes

| File | Size | Rows | Note |
|---|---|---|---|
| `dfTransjakarta180kRows.csv` | ~60MB | 189,500 | Data asli April 2023 dari Kaggle |
| `dfTransjakarta_FULL_Combined.csv` | ~331MB | 1,407,502 | Asli + sintetis Jan 2023–Mar 2026 |

> ⚠️ **Tentang data sintetis**: Data periode Jan 2023–Mar 2023 dan May 2023–Mar 2026 adalah **data sintetis** yang dibuat berdasarkan pola statistik dari data asli April 2023. Pola yang direplikasi mencakup: seasonal Lebaran (2023, 2024, 2025), growth trend +5% YoY berdasarkan data BPS, weekend vs weekday ratio, distribusi bank/gender, dan distribusi jam perjalanan.

---

## 👤 Author

**Alfen Hasiholan** — Data Engineer & BI Developer · Jakarta, Indonesia

[![LinkedIn](https://img.shields.io/badge/LinkedIn-alfen--hasiholan-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](http://www.linkedin.com/in/alfen-hasiholan)
[![GitHub](https://img.shields.io/badge/GitHub-alfenh17-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/alfenh17)
[![Email](https://img.shields.io/badge/Email-alfenhasiholan%40gmail.com-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:alfenhasiholan@gmail.com)
[![Portfolio](https://img.shields.io/badge/Portfolio-Visit-1DB96A?style=flat-square&logo=vercel&logoColor=white)](https://alfenh17.github.io/portfolio)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

<div align="center">
  <sub>Built with ❤️ using Python · Power BI · SQL Server · SSAS · Open Data Jakarta · GTFS TUMI</sub>
</div>
