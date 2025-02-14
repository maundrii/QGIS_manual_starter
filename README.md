# Membuat Vector Database

## Install PostgreSQL dan PostGIS
1. Download PostgreSQL dari [situs resminya](https://www.postgresql.org/download/).
2. Jalankan **Stack Builder** setelah instalasi PostgreSQL selesai.  
   ![alt text](image-2.png)
3. Pilih PostgreSQL versi yang diinstal.  
   ![alt text](image-1.png)
4. Pilih **Spatial Extensions → PostGIS**.  
   ![alt text](image-3.png)
5. Klik **Next** dan ikuti proses instalasi hingga selesai.

---

## Pembuatan dan Pengaturan Database (Server)

### Pembuatan Database
1. Buka **pgAdmin** dan login ke PostgreSQL.
2. Klik kanan pada **Databases → Create → Database**.  
   ![alt text](image-4.png)
3. Isi detail database:
   - **Database Name**: nama file database (contoh: `tes_jabar`)
   - **Owner**: `postgres` (atau user lain yang Anda buat)  
   ![alt text](image-5.png)
4. Klik **Save** untuk menyimpan database.
5. Aktifkan **PostGIS** di database:
   - Klik kanan pada Database → **Query Tool** di pgAdmin.  
     ![alt text](image-6.png)
   - Akan ditampilkan jendela Query di pgAdmin.  
     ![alt text](image-7.png)
   - Masukkan SQL berikut:
     ```sql
     CREATE EXTENSION postgis;
     ```
     ![alt text](image-8.png)
   - Klik **Execute/Refresh** untuk menjalankan perintah.
   - Jika berhasil, akan muncul pesan **"successfully"**, menandakan ekstensi **PostGIS** telah aktif.
   - Untuk memastikan PostGIS telah aktif:
     - Buka **Object Explorer** pada pgAdmin.
     - Pilih **Database (tes_jabar) → Extensions**.
     - Pastikan **PostGIS** terlihat dalam daftar ekstensi.  
       ![alt text](image-9.png)

---

### Menghubungkan QGIS ke Enterprise Geodatabase
1. Buka **QGIS**.
2. Pilih **Layer → Data Source Manager → PostgreSQL**.  
   ![alt text](image-10.png)  
   ![alt text](image-11.png)
3. Klik **New** dan isi:
   - **Name**: (misal: `PostGISDB`)
   - **Host**: `localhost` (atau IP server PostgreSQL)
   - **Port**: `5432`
   - **Database**: nama database yang telah dibuat sebelumnya (contoh: `tes_jabar`)
   - **Username**: `postgres`
   - **Password**: (password user PostgreSQL)  
     ![alt text](image-12.png)
4. Klik **Test Connection** untuk memastikan koneksi berhasil.
5. Klik **OK**, lalu **Connect** untuk melihat tabel dalam database.
6. Jika koneksi berhasil, **PostGISDB** akan muncul di jendela browser.  
   ![alt text](image-13.png)

---

### Import Data Shapefile ke Dalam Database PostGIS di QGIS
1. Pilih **Database → DB Manager**.  
   ![alt text](image-19.png)
2. Pilih **PostGIS** → Pilih koneksi database yang sudah dibuat.
3. Klik **Import Layer/File** (ikon berbentuk panah ke database).  
   ![alt text](image-20.png)
4. Pilih file **Shapefile (.shp)** yang ingin diimpor.
5. Pilih **Nama Tabel** yang akan dibuat dalam PostGIS.
6. Centang **Create Spatial Index** untuk mempercepat query.  
   ![alt text](image-21.png)
7. Klik **OK** untuk memulai proses import.
8. Setelah selesai, data bisa dilihat di **Browser Panel → PostGIS Connection**.  
   ![alt text](image-22.png)
9. Data yang diimpor bisa langsung diedit di QGIS.

---

### Menambahkan Kolom untuk Tracking Perubahan Data
Untuk melacak siapa yang mengedit data dan kapan, jalankan SQL berikut pada **Query Tool** di pgAdmin:

```sql
ALTER TABLE sungai ADD COLUMN created_by TEXT DEFAULT CURRENT_USER;
ALTER TABLE sungai ADD COLUMN updated_by TEXT;
ALTER TABLE sungai ADD COLUMN updated_at TIMESTAMP DEFAULT now();
