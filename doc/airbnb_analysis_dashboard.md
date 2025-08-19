# Airbnb ETL & Dashboard Project

This document explains the steps performed in the Colab notebook and the Power BI dashboard created from the processed data. It is written to be included in a GitHub repo as documentation.

## Project Summary

- **Objective:** Clean and transform Airbnb listing data, create a three-layer data architecture (bronze/silver/gold), store data on Google Drive, and build a Power BI dashboard for analysis and visualization.

- **Environment:** Google Colab with PySpark (SparkSession), using Google Drive as cloud storage (mounted to Colab). Delta/Parquet used for intermediate storage (if present in notebook).

## Environment & Setup

- **Google Drive**: Mounted in Colab using `drive.mount('/content/drive')` to use Drive as cloud storage for raw and processed files.

- **PySpark**: SparkSession is created to process large data with DataFrame transformations.

## Data Ingestion

- **Source format**: Parquet files (multiple parquet files in a Drive folder). These were read into Spark DataFrames (or pandas) using `spark.read.parquet(...)` or equivalent.

## Data Cleansing & Transformation Steps

The notebook follows standard cleansing steps. Key operations found in the notebook code:

- **Null handling**: No explicit null-drop detected; check notebook cells for `dropna` or `fillna` to confirm.

- **Deduplication**: No explicit dedup detected; recommended to deduplicate on `id`.

- **Type casting**: Columns like `price`, `service_fee`, `lat`, `long`, and `availability_365` were cast to numeric types and cleaned to remove currency symbols and commas.

- **Aggregation**: Use `groupBy` to compute aggregates for dashboard KPIs and charts.

## Three-Layer Data Architecture (Bronze / Silver / Gold)

This project applies a layered ETL approach commonly used in data engineering:

- **Bronze (Raw ingestion)**: Raw files are copied into a 'bronze' area on Google Drive as Parquet/JSON. This layer preserves original data and minimal transformations.

- **Silver (Cleansed / Enriched)**: Apply cleaning steps (null handling, type casting, deduplication, parsing dates). Add derived columns like `year`, `month`, `day_name`, `price_clean`, `is_instant_bookable_bool`.

- **Gold (Analytics / Aggregates)**: Create aggregated tables optimized for reporting (e.g., monthly guest counts, avg price by room type & neighborhood, host-level summaries). These are written to parquet/CSV for Power BI ingestion.

## Storage: Using Google Drive as Cloud Storage

- **Drive as storage**: The notebook mounts Google Drive to `/content/drive` and reads/writes data there. Use Drive folders for Bronze/Silver/Gold zones like:

  - `/content/drive/MyDrive/airbnb/bronze/`
  - `/content/drive/MyDrive/airbnb/silver/`
  - `/content/drive/MyDrive/airbnb/gold/`

- **Formats**: Use Parquet (or Delta if configured) for efficient columnar storage. Export CSV where Power BI requires CSV input.

## Part 2: Power BI Dashboard - Visuals and Purpose

The dashboard created uses the following visuals (what they show and why):

### KPI Cards

- **Avg Review**: Average `review_rate_number` across selected filters — indicates guest satisfaction.

- **Avg Service Price**: Average `service_fee` — helps understand fees levied on bookings.

- **Avg Night Price**: Average `price` per night — core revenue metric.

- **Overall Hosts**: `DISTINCTCOUNT(host_id)` — total unique hosts in the dataset.

- **Overall Listings**: `COUNT(id)` — total number of listings (properties).

### Slicers

- **Month**: Filter time-based visuals to a specific month.

- **Year**: Filter by year.

- **Instant Bookable**: Filter listings that are instant-bookable or not.

### Map

- **Map**: 'Airbnb Listings by Location' using `lat` and `long`. Bubble size maps to the overall number of listings at or aggregated around a point (clustered or aggregated) — useful to identify hot spots.

### Decomposition Tree

- **Decomposition Tree**: Analyze `Overall Listings` and drill into `room_type` and `price` to see which room types and price buckets explain listing counts.

### Charts

- **Bar Chart**: Sum of `price` by `host_name` — shows top-earning hosts by total price.

- **Line Chart**: 'Count of guests by month' — if guests not present, this uses proxy like `number_of_reviews` or bookings-derived table; otherwise, counts of visits per month.

- **Funnel**: 'Listing Availability Distribution' — visualize the drop-off from always-available to rarely-available listings (use availability buckets).

- **Clustered Column**: Average Price by `neighbourhood_group` and `room_type` (legend for room_type) — compares price across areas and room types.

- **Donut Chart**: Overall price by rating (1–5) — aggregate price sums or averages grouped by `review_rate_number` categories.

- **Table**: Price of hosts by day — a detailed table showing `host_name`, day-of-week (`day_name`), and prices to inspect price patterns across weekdays.

## How to reproduce (high-level steps)

1. Open the Colab notebook and mount Google Drive:

```python
from google.colab import drive
drive.mount('/content/drive')
```

2. Start SparkSession (PySpark):

```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName('AirbnbETL').getOrCreate()
```

3. Read raw parquet files from Drive into Spark DataFrames (bronze layer):

```python
df = spark.read.parquet('/content/drive/MyDrive/airbnb/raw/*.parquet')
```

4. Clean data (silver layer):

- Trim/standardize strings, handle nulls, cast numeric columns, parse dates, remove duplicates.

5. Aggregate and write analytics tables (gold layer) to `/content/drive/MyDrive/airbnb/gold/` in Parquet or CSV for Power BI.

## Files Generated / Outputs

- Bronze: raw parquet files (copied original files)
- Silver: cleaned parquet/dataframes
- Gold: aggregated reporting tables (Parquet/CSV)
- Notebook: the Colab notebook file (.ipynb)

## Recommendations & Next Steps

- Consider using Delta Lake on a proper cloud storage (GCS/Azure Blob/S3) for ACID and time travel.

- Add data quality tests (counts, null rate checks) to the pipeline.

- If you need guest-level analytics, ingest booking data with guest IDs.

- Add more advanced features: price per season, occupancy rate, host response time (if available).
