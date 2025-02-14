# Membuat Vector Database
## _Oleh Dedi Wahyu Sasongko S.T_

## Install PostgreSQL dan PostGIS
1. Download PostgreSQL dari [situs resminya](https://www.postgresql.org/download/).
2. Jalankan **Stack Builder** setelah instalasi PostgreSQL selesai.
![V_DB_01](https://github.com/user-attachments/assets/d67b0105-b93d-42ec-b3ac-b4c7afd0e3a5)
4. Pilih PostgreSQL versi yang diinstal.  
![V_DB_02](https://github.com/user-attachments/assets/c0096ab1-1bbd-47e8-a9a3-4c3c0c15cc7d)
5. Pilih **Spatial Extensions → PostGIS**.  
![V_DB_03](https://github.com/user-attachments/assets/498b44d8-9b6c-4d90-86cf-4e36011805d8)
6. Klik **Next** dan ikuti proses instalasi hingga selesai.

---

## Pembuatan dan Pengaturan Database (Server)

### Pembuatan Database
1. Buka **pgAdmin** dan login ke PostgreSQL.
2. Klik kanan pada **Databases → Create → Database**.  
 ![V_DB_04](https://github.com/user-attachments/assets/74e24a70-72fb-43d7-beb6-8abb75af5e10)
3. Isi detail database:
   - **Database Name**: nama file database (contoh: `tes_jabar`)
   - **Owner**: `postgres` (atau user lain yang Anda buat)  
![V_DB_05](https://github.com/user-attachments/assets/7f1586ee-9ec5-46d3-8e43-e08897a5615f)
4. Klik **Save** untuk menyimpan database.
5. Aktifkan **PostGIS** di database:
   - Klik kanan pada Database → **Query Tool** di pgAdmin.  
     ![V_DB_06](https://github.com/user-attachments/assets/23f782a1-7638-445a-aa12-333d9cdba6dd)
   - Akan ditampilkan jendela Query di pgAdmin.  
     ![V_DB_07](https://github.com/user-attachments/assets/f88c7f2b-9e3b-493d-a1fa-dc6c3aaed17f)
   - Masukkan SQL berikut:
     ```sql
     CREATE EXTENSION postgis;
     ```
     ![V_DB_08](https://github.com/user-attachments/assets/c04c9998-c40b-47b9-95af-e5ca47b77711)
   - Klik **Execute/Refresh** untuk menjalankan perintah.
   - Jika berhasil, akan muncul pesan **"successfully"**, menandakan ekstensi **PostGIS** telah aktif.
   - Untuk memastikan PostGIS telah aktif:
     - Buka **Object Explorer** pada pgAdmin.
     - Pilih **Database (tes_jabar) → Extensions**.
     - Pastikan **PostGIS** terlihat dalam daftar ekstensi.  
       ![V_DB_09](https://github.com/user-attachments/assets/b670db61-c695-4939-9f67-cda13759f5bd)
---

### Menghubungkan QGIS ke Enterprise Geodatabase
1. Buka **QGIS**.
2. Pilih **Layer → Data Source Manager → PostgreSQL**.  
   ![V_DB_10](https://github.com/user-attachments/assets/47751f71-759f-4e05-b8aa-dc6584a3a162)
   <br>
   ![V_DB_11](https://github.com/user-attachments/assets/1f05aad7-8697-4dc1-98cf-54ad7d3223c5)
4. Klik **New** dan isi:
   - **Name**: (misal: `PostGISDB`)
   - **Host**: `localhost` (atau IP server PostgreSQL)
   - **Port**: `5432`
   - **Database**: nama database yang telah dibuat sebelumnya (contoh: `tes_jabar`)
   - **Username**: `postgres`
   - **Password**: (password user PostgreSQL)  
   ![V_DB_12](https://github.com/user-attachments/assets/388ede35-c2df-409d-aaf1-55c98b4c6fbd)
5. Klik **Test Connection** untuk memastikan koneksi berhasil.
6. Klik **OK**, lalu **Connect** untuk melihat tabel dalam database.
7. Jika koneksi berhasil, **PostGISDB** akan muncul di jendela browser.  
   ![V_DB_13](https://github.com/user-attachments/assets/577e6600-6abd-4e6e-8fb0-f7dae01b88ad)

---

### Import Data Shapefile ke Dalam Database PostGIS di QGIS
1. Pilih **Database → DB Manager**.  
   ![V_DB_14](https://github.com/user-attachments/assets/df2fbbdf-e2ec-4734-b60f-bad213c924be)
2. Pilih **PostGIS** → Pilih koneksi database yang sudah dibuat.
3. Klik **Import Layer/File** (ikon berbentuk panah ke database).  
   ![V_DB_15](https://github.com/user-attachments/assets/b83dd11f-f5e2-4332-8136-f26a4006bc26)
4. Pilih file **Shapefile (.shp)** yang ingin diimpor.
5. Pilih **Nama Tabel** yang akan dibuat dalam PostGIS.
6. Centang **Create Spatial Index** untuk mempercepat query.  
   ![V_DB_16](https://github.com/user-attachments/assets/d61c346a-3294-4617-994a-b94f5f7c3faf)
7. Klik **OK** untuk memulai proses import.
8. Setelah selesai, data bisa dilihat di **Browser Panel → PostGIS Connection**.  
   ![V_DB_17](https://github.com/user-attachments/assets/bfedd10b-314c-4d46-b813-f5f851ca0728)
9. Data yang diimpor bisa langsung diedit di QGIS.

---

### Menambahkan Kolom untuk Tracking Perubahan Data
Untuk melacak siapa yang mengedit data dan kapan, jalankan SQL berikut pada **Query Tool** di pgAdmin:
```sh
ALTER TABLE sungai ADD COLUMN created_by TEXT DEFAULT CURRENT_USER;
ALTER TABLE sungai ADD COLUMN updated_by TEXT;
ALTER TABLE sungai ADD COLUMN updated_at TIMESTAMP DEFAULT now();
```
### Pengaturan Hak Akses ke User Lain
PostgreSQL menggunakan sistem perizinan yang bisa dikonfigurasi untuk multiuser editing.
1. Buat User Baru untuk Editor, isikan SQL:
```sh
CREATE USER afit WITH PASSWORD '123'; 
CREATE USER munir WITH PASSWORD '456';
CREATE USER dedon WITH PASSWORD '789';
```
Pada contoh SQL diatas dibuat akses kepada 3 user. User dapat dibuat sebanyak sesuai dengan kebutuhan dengan menambahkan baris baru pada SQL.
User-user yang telah dibuat dapat dilihat di dalam jendela Object Explorer, Pada Database --> Login/Group Roles.

![V_DB_18](https://github.com/user-attachments/assets/2658fae6-4b5e-48eb-b15b-289415d65728)

2. Berikan Hak Akses untuk user ke Database
```sh
GRANT CONNECT ON DATABASE tes_jabar TO afit;
```
4. Beri Hak Akses untuk user ke Tabel dan Data
```sh
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE sungai TO afit; GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO afit;
```

### Setting IP yang akan diberikan hak akses ke database
1. Buka file pg_hba.conf yang berlokasi di C:\Programfiles\PostgreSQL\14\data\pg_hba.conf
2. Edit dengan teks editor/Notepad
3. Tambahkan isian berikut di baris bawah:
host all all 192.168.1.0/24 md5
![V_DB_19](https://github.com/user-attachments/assets/38a8ee70-e622-441a-8d35-f5d4bca9d922)


## Setting Firewall dan Port Database
Masuk ke dalam menu Control Panel → Windows Defender Firewall → Advanced Settings → Inbound Rules → New Rule Pilih Port → Masukkan 5432 → Izinkan akses
![V_DB_20](https://github.com/user-attachments/assets/924a1376-6f0d-4828-940e-521f58fc7de3)
<br>
![V_DB_21](https://github.com/user-attachments/assets/c5b06252-59df-4b44-b894-3df6d377b0ee)
---
## Akses Geodatabase (User)
1. Buka QGIS.
2. Pilih Layer → Data Source Manager → PostgreSQL.

![V_DB_22](https://github.com/user-attachments/assets/96655ca5-61ba-410b-83fc-21c060101cb3)

![V_DB_23](https://github.com/user-attachments/assets/4762fc4d-3c03-4987-9829-a427a4782c46)

3. Klik New dan isi:
- Name: Isikan bebas (misal: PostGISDB)
- Host: IP server PostgreSQL
- Port: 5432
- Database: Isikan nama database yang telah dibuat sebelumnya (contoh: tes_jabar)
- Username: sesuai dengan akun akses user (contoh: afit)
- Password: sesuai dengan password akun user.

![V_DB_24](https://github.com/user-attachments/assets/7fc828be-24ef-46b0-a9cb-ee21e242a6fa)

4. Klik Test Connection untuk memastikan koneksi berhasil.
5. Klik OK, lalu Connect untuk melihat tabel dalam database.
6. Jika sudah terkoneksi, maka PostGISDB akan tampil di jendela browser dan data sudah siap diedit oleh user.

