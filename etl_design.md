# ETL Pipeline Design: E-Commerce Orders

## 1. Overview
Pipeline ini memproses data transaksi e-commerce harian untuk membersihkan data kotor dari aplikasi, melakukan validasi kualitas data, dan menghasilkan laporan ringkasan performa penjualan per kategori produk.

## 2. Extract
- Sumber: File CSV (`raw_orders.csv` dan `raw_products.csv`)
- Format: Comma-Separated Values (CSV)
- Volume: 130 baris data pesanan mentah

## 3. Transform
- Langkah 1: Menghapus 10 baris data duplikat untuk memastikan keunikan data.
- Langkah 2: Menghapus 5 baris data dengan harga negatif yang terindikasi sebagai data error.
- Langkah 3: Mengisi missing value pada email dengan `unknown@placeholder.com` dan mengisi harga yang kosong menggunakan nilai median.
- Langkah 4: Menstandarkan format tanggal menjadi datetime standar.
- Langkah 5: Konsistensi casing teks (lowercase untuk channel dan title case untuk nama kota).
- Langkah 6: Membuat kolom baru berupa `bulan` dan `kategori_harga` (kecil, sedang, besar) untuk kebutuhan enkapsulasi data analisis.

## 4. Load
- Tujuan: Penyimpanan lokal/Staging area (Simulasi Warehouse)
- Format output: `orders_clean.csv` (110 baris data bersih) dan `summary_report.csv` (laporan agregasi)

## 5. Orchestration
- Tool: Apache Airflow (Konsep DAG & Simulasi Python Task Runner)
- Schedule: `0 6 * * *` (Dijalankan otomatis setiap hari jam 06:00 WIB)
- DAG flow: `start >> extract >> transform >> validate >> load >> report >> notify >> end`

## 6. Error Handling
- Skenario 1 (File Hilang): Menggunakan validasi `os.path.exists` pada task Extract, jika file tidak ditemukan maka pipeline berhenti dan melempar `FileNotFoundError`.
- Skenario 2 (Gagal Eksekusi Task): Menggunakan mekanisme *Task Runner* dengan `MAX_RETRIES = 3` dan *exponential backoff* untuk mencoba ulang task yang gagal sebelum dinyatakan *Fatal Error*.

## 7. Monitoring
- Bagaimana cara tahu pipeline sukses? Melalui file `pipeline_log.txt` yang mencatat setiap status `RUNNING`, `SUCCESS`, hingga `COMPLETED`, serta adanya notifikasi akhir (simulasi email/Slack).
- Bagaimana cara tahu data berkualitas? Melalui komponen `task_validate` (Validation Gate) yang memastikan data yang masuk ke tahap Load memiliki *zero duplicates*, *zero nulls*, dan tidak ada harga negatif. Jika pengecekan gagal, pipeline otomatis berhenti dan data kotor tidak akan disimpan.
