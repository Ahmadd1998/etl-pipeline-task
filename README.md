# E-Commerce ETL Data Pipeline & Orchestration

An end-to-end Data Engineering project demonstrating an automated **Extract, Transform, Load (ETL)** pipeline for e-commerce transactional data. This project showcases data cleaning, strict quality validation gates, execution logging, and automated workflow orchestration principles using Python, Pandas, and Apache Airflow.

---

## 🚀 Repository Structure

* **dags/**
  * `etl_ecommerce_dag.py` : Production-ready Apache Airflow DAG configuration.
* `etl_pipeline.py` : Main Python ETL execution script (Modular Workflow).
* `ETL_Pipeline.ipynb` : Interactive Jupyter Notebook for development & testing.
* `raw_orders.csv` : Raw, dirty transactional source data.
* `raw_products.csv` : Reference product master data.
* `orders_clean.csv` : Transformed, validated, and clean destination data.
* `summary_report.csv` : Aggregated business summary report.
* `pipeline_log.txt` : Execution log file tracking task states and performance.
* `etl_design.md` : Detailed pipeline architecture & design documentation.

---

## 🛠️ Technology Stack

* **Language:** Python 3.8+
* **Data Manipulation:** Pandas, NumPy
* **Orchestration:** Apache Airflow (DAG Design) & Custom Python Sequential Task Runner
* **Environment:** Google Colab / Local Python Env

---

## ⚙️ Pipeline Anatomy & Workflow

The pipeline follows a strict dependency graph (DAG) to ensure data integrity. If any task fails—specifically the validation gate—downstream operations are immediately halted to prevent corrupted data from entering the warehouse.

`[Extract] ──> [Transform] ──> [Validate Gate] ──> [Load Area] ──> [Report] ──> [Notify]`

1. **Extract:** Ingests raw transactional data from `raw_orders.csv` (130 records) and inspects inconsistencies.
2. **Transform:** 
   * Removes 10 duplicate records.
   * Filters out 5 anomalous rows with negative pricing errors.
   * Handles missing values (imputes emails and sets missing prices to the median).
   * Standardizes date-time formats and text casings across fields.
   * Derives new analytical metrics (`bulan` and `kategori_harga`).
3. **Validate:** Acts as a data quality gate checking for zero nulls, zero duplicates, and correct data types.
4. **Load:** Persists the validated 110 clean rows into `orders_clean.csv`.
5. **Report & Notify:** Generates a summary metrics report per product category and triggers a completion notification alert.

---

## 📈 Aggregated Business Insights (`summary_report.csv`)

* **Elektronik:** 81 Total Orders | Total Revenue: Rp 435,180,000 | Average Revenue: Rp 5,372,593
* **Furniture:** 29 Total Orders | Total Revenue: Rp 127,350,000 | Average Revenue: Rp 4,391,379

---

## 💻 How to Run Locally

### Prerequisites
Make sure you have Python installed, then install the required dependencies:
`pip install pandas numpy`

### Execution
Run the standalone pipeline script to process the data and generate logs:
`python etl_pipeline.py`

You can monitor the processing timeline and status checkmarks directly via the generated `pipeline_log.txt` or through the standard terminal output.
