# AIS PostgreSQL Data Management Notebook

## Overview
This notebook automates the full workflow of managing AIS (Automatic Identification System) data with **PostgreSQL** and **Python**.

It performs three key steps:
1. **Table Creation** — builds yearly and monthly partitioned AIS tables.
2. **Data Upload** — reads cleaned Parquet files and uploads them in batches.
3. **Data Export** — extracts and saves daily chunks of AIS data as Parquet files.

---

## Environment Setup

### Requirements
- **PostgreSQL** 12+
- **Python** 3.8+
- **Jupyter Notebook** or **Google Colab**
- Installed packages:
  ```bash
  pip install pandas sqlalchemy psycopg2-binary pyarrow dask
  ```

### Database Connection
Use environment variables or a `.env` file (for security) instead of hard-coding credentials.

Example:
```bash
export DB_USER="appuser"
export DB_PASS="your_password"
export DB_HOST="pg-postgresql.jrycastillo.svc"
export DB_PORT="5432"
export DB_NAME="appdb"
```

In the notebook:
```python
from sqlalchemy import create_engine
import os

engine = create_engine(
    f"postgresql+psycopg2://{os.getenv('DB_USER')}:{os.getenv('DB_PASS')}@{os.getenv('DB_HOST')}:{os.getenv('DB_PORT')}/{os.getenv('DB_NAME')}"
)
```

---

## Notebook Sections

### 1️ Table Creation
Creates AIS tables partitioned by **year** and **month** to improve query performance and storage efficiency.

### 2️ Data Upload
Reads Parquet files, detects which years the data belongs to, and uploads in batches to avoid memory overload.

The upload process also maintains a `upload_log.csv` file that keeps track of already processed files.

### 3️ Data Export
Exports PostgreSQL data into Parquet files split **per day** using streaming cursor queries — ideal for large datasets.

Each export automatically includes:
- Table name and date range
- Compression (default: Snappy)
- Output folder with timestamps

---

## 🧪 Sample Usage
1. Place your cleaned Parquet files inside the designated data folder.  
2. Run the notebook cells in order:
   - Table creation
   - Data upload
   - Data export
3. Check the generated `upload_log.csv` and exported Parquet output folders.

Example directory structure:
```
/home/jovyan/shared/val/task/
  ├── ais_upload_notebook.ipynb
  ├── upload_log.csv
  ├── parquet_data/
  └── output_dl_postgres/
```

---

## Notes
- Do **not** include or share database credentials in public repositories.
- Adjust `BATCH_ROWS` and `CHUNKSIZE` parameters depending on system memory.
- The process is **idempotent** — re-running it won’t duplicate already-uploaded data.

---

## Summary
| Task | Description | Output |
|------|--------------|---------|
| Table Setup | Creates partitioned AIS tables | PostgreSQL tables |
| Data Upload | Loads Parquet data to database | Log + database inserts |
| Data Export | Extracts and saves data daily | Parquet output files |

---

**Author:** Val Kenneth Arado  
📆 **Last Updated:** October 2025  
🔖 **Type:** Jupyter Notebook – PostgreSQL AIS Data Workflow
