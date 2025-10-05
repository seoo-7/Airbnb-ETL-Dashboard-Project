# Airbnb ETL & Dashboard Project

> End-to-end project: Clean, transform, and visualize Airbnb listings data using Google Colab (PySpark) and Power BI.

## 📌 Overview

This project demonstrates a complete data pipeline:

1. **Data Engineering in Colab (PySpark)** — Ingest, clean, and transform Airbnb listings.

2. **Three-Layer Architecture** — Bronze (raw), Silver (cleaned), Gold (aggregated).

3. **Cloud Storage** — Use Google Drive as data lake for storage and retrieval.

4. **Visualization in Power BI** — Build an interactive dashboard with KPIs, filters, and advanced visuals.

## ⚙️ Environment & Tools

- Google Colab (runtime)
- PySpark (ETL & transformations)
- Google Drive (cloud storage)
- Power BI Desktop (visualization)

## 🔁 ETL Workflow (Bronze / Silver / Gold)

- **Bronze (Raw Layer)**: Load raw parquet/CSV files into Drive without transformations.

- **Silver (Clean Layer)**:
  - Data quality checks (`availability_365` , `review_rate_number`)
  - Dact Cleansing using  "[$,]" to remove the currency symbols in (`price` , `service_fee`)
  - Handle nulls (`dropna`, `fillna`)
  - Remove duplicates (`dropDuplicates`)
  - Standardize strings (`trim`)
  - Cast numeric columns (`price`, `service_fee`, `availability_365`)
  - Parse dates & extract `year`, `month`, `day_name`

- **Gold (Analytics Layer)**: Aggregated tables ready for BI:

  - Average price per night, service fee
  - Reviews per month & average rating
  - Availability distribution buckets
  - Host and listing counts

## ☁️ Storage in Google Drive

Data is organized in Drive folders:

```
/content/drive/MyDrive/airbnb/
  ├── bronze/   # raw files
  ├── silver/   # cleaned datasets
  └── gold/     # aggregated BI tables
```

## 📊 Power BI Dashboard
<img width="1415" height="812" alt="Airbnb dashboard " src="https://github.com/user-attachments/assets/fc7e409f-7c53-4142-ac0c-ab42a3b98b0e" />

The dashboard contains the following visuals:

- **Cards (KPIs):** Avg Review, Avg Service Price, Avg Night Price, Overall Hosts, Overall Listings

- **Slicers:** Month, Year, Instant Bookable

- **Map:** Airbnb Listings by Location (bubble size = listings)

- **Decomposition Tree:** Analyze Overall Listings → Room Type → Price

- **Bar Chart:** Sum of Price by Host Name

- **Line Chart:** Count of Guests by Month (reviews as proxy)

- **Funnel:** Listing Availability Distribution

- **Clustered Column:** Avg Price by Neighborhood & Room Type

- **Donut:** Overall Price by Rating (1–5)

- **Table:** Price of Hosts by Day of Week

## 📈 Key Insights from Dashboard

- Hosts are concentrated in certain neighborhoods (e.g., Manhattan, Brooklyn).

- Entire homes/apartments dominate higher price ranges, private rooms lower.

- Availability varies widely — many listings are seasonal, fewer are full-year.

- Verified hosts correlate with higher review scores.


