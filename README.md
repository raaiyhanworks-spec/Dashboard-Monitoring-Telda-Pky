# Dashboard Monitoring Downline Telda Palangka Raya

## Overview

Dashboard Monitoring Downline Telda Palangka Raya merupakan sistem monitoring operasional yang digunakan untuk memantau progres order layanan HSI (High Speed Internet) dari berbagai channel, khususnya Downline.

Dashboard ini membantu Admin Telda dalam:

- Memantau status order secara real-time
- Mengidentifikasi kendala yang menghambat penyelesaian order
- Mengukur performa STO dan tim terkait
- Memastikan kepatuhan terhadap SLA
- Menyediakan laporan operasional yang cepat dan akurat

---

## Data Source

Sumber data berasal dari link spreadsheets: https://docs.google.com/spreadsheets/d/1L4qGSlFljZq_hUFxOevJha7Les-ckIetdaSsDVikgME/edit?gid=239386119#gid=239386119


### Struktur Data

| Field | Tipe Data | Deskripsi |
|---------|-----------|-----------|
| No | Integer | Nomor urut data |
| Order_ID | Varchar(20) | ID unik order |
| Customer | Varchar(255) | Nama pelanggan atau instansi |
| Tanggal_Input | Date | Tanggal order masuk |
| STO | Varchar(10) | STO yang menangani order |
| Channel | Varchar(50) | Jalur pengajuan layanan |
| Status | Varchar(50) | Status terkini order |
| Update_Kendala_Cancel | Text | Catatan progres, kendala, atau alasan pembatalan |

---

# Data Dictionary

## KPI Dashboard

### Open Orders

**Deskripsi**
Jumlah seluruh order yang masih aktif dan belum selesai diproses.

**Formula**

```sql
Open Orders = Total Order - Completed - Cancel
```

| Atribut | Nilai |
|----------|--------|
| Tipe Visualisasi | KPI Card |
| Sumber Data | Status |
| Update Frequency | Real-time |

---

### In Progress

**Deskripsi**
Jumlah order yang sedang berada dalam proses pengerjaan.

**Formula**

```sql
Status NOT IN ('Completed', 'Cancel')
```

| Atribut | Nilai |
|----------|--------|
| Tipe Visualisasi | KPI Card |
| Sumber Data | Status |
| Update Frequency | Real-time |

---

### Completed Today

**Deskripsi**
Jumlah order yang berhasil diselesaikan pada hari berjalan.

**Formula**

```sql
COUNT(Status = 'Completed')
```

| Atribut | Nilai |
|----------|--------|
| Tipe Visualisasi | KPI Card |
| Sumber Data | Status |
| Update Frequency | Harian |

---

### SLA Compliance

**Deskripsi**
Persentase order yang selesai sesuai target SLA.

**Formula**

```sql
(Order Sesuai SLA / Total Order Selesai) * 100%
```

| Atribut | Nilai |
|----------|--------|
| Tipe Visualisasi | KPI Card |
| Sumber Data | Status & Tanggal |
| Update Frequency | Harian |

---

## Recent Orders

Menampilkan daftar order terbaru yang masuk ke sistem.

| Nama Field Dashboard | Mapping Data Source |
|----------------------|---------------------|
| Order ID | Order_ID |
| Customer | Customer |
| STO | STO |
| Channel | Channel |
| Status | Status |
| Last Update | Update_Kendala_Cancel |
| Tanggal Input | Tanggal_Input |

---

## Filter Dashboard

| Filter | Sumber Data |
|----------|-----------|
| Periode | Tanggal_Input |
| STO | STO |
| Channel | Channel |
| Status | Status |
| Customer | Customer |
| Order ID | Order_ID |

---

## Master Status

### Completed
Order berhasil diselesaikan.

### Kendala
Order mengalami hambatan teknis maupun administratif.

### Cancel
Order dibatalkan oleh pelanggan atau sistem.

### PI
Menunggu proses instalasi.

### AR Manja - Send Survey
Menunggu proses survey lapangan.

### AR Premanja - FCC
Menunggu proses FCC.

### Review RSO
Sedang dalam proses review oleh RSO.

---

## Analytics Dashboard

### Status Distribution

Visualisasi distribusi order berdasarkan status.

**Visualisasi**
- Pie Chart
- Donut Chart

**Dimensi**
- Status

**Measure**
- Jumlah Order

---

### STO Performance

Visualisasi performa masing-masing STO.

**Dimensi**
- STO

**Measure**
- Total Order
- Completed
- Kendala
- Cancel
- Success Rate

**Visualisasi**
- Bar Chart
- Ranking Table

---

### Kendala Analysis

Analisis akar masalah yang menyebabkan order tertunda.

**Kategori Kendala**

- ODP Full
- FCC
- Eviden Kurang Lengkap
- Menunggu Survey
- Review RSO
- Kendala Teknis Lainnya

**Visualisasi**
- Horizontal Bar Chart

---

### Channel Performance

Analisis performa berdasarkan channel order.

**Dimensi**
- Channel

**Measure**
- Total Order
- Completed
- Success Rate

**Visualisasi**
- Bar Chart

---

## Recommended Metrics

### Aging Order

Mengukur umur order sejak tanggal input.

**Formula**

```sql
Aging = Current Date - Tanggal_Input
```

**Kategori**

| Range | Kategori |
|---------|-----------|
| 0-3 Hari | Normal |
| 4-7 Hari | Warning |
| >7 Hari | Critical |

---

### SLA Countdown

Mengukur sisa waktu penyelesaian order sebelum melanggar SLA.

**Formula**

```sql
SLA Target - Aging Order
```

---

### Root Cause Analysis

Mengidentifikasi penyebab dominan order tertunda atau gagal.

---

### STO Heatmap

Menampilkan performa STO berdasarkan:

- Volume Order
- Success Rate
- SLA Compliance

---

## Database Schema

### tbl_orders

```sql
CREATE TABLE tbl_orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    order_id VARCHAR(20),
    customer_name VARCHAR(255),
    tanggal_input DATE,
    sto VARCHAR(10),
    channel VARCHAR(50),
    status VARCHAR(50),
    update_note TEXT,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

### tbl_status

```sql
CREATE TABLE tbl_status (
    status_id INT PRIMARY KEY AUTO_INCREMENT,
    status_name VARCHAR(100),
    status_group VARCHAR(50)
);
```

Contoh Data:

| status_name | status_group |
|-------------|--------------|
| Completed | Closed |
| Cancel | Closed |
| Kendala | Open |
| PI | Open |
| AR Manja | Open |
| AR Premanja | Open |

---

## User Roles

### Admin

- Menambah order baru
- Mengubah status order
- Mengelola data monitoring
- Melihat seluruh laporan

### Telda Officer

- Monitoring performa STO
- Monitoring SLA
- Analisis kendala

### HOTD

- Monitoring KPI
- Evaluasi performa operasional
- Pengambilan keputusan berbasis data

---

## Expected Benefits

- Monitoring order lebih cepat dan terpusat
- Mengurangi keterlambatan penyelesaian order
- Mempermudah identifikasi bottleneck operasional
- Meningkatkan SLA Compliance
- Mendukung pengambilan keputusan berbasis data

---

**Project:** Dashboard Monitoring Downline Telda Palangka Raya  
**Owner:** Telda Palangka Raya  
**Last Updated:** June 2026
