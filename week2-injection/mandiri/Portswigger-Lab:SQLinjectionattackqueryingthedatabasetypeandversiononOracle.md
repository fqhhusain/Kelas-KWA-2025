# Soal

- **Kategori**: SQL injection
- **Diff**    : PRACTITIONER  
- **Judul**   : Lab: SQL injection attack, querying the database type and version on Oracle

## Deskripsi 
This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.

To solve the lab, display the database version string.

# Resource
https://portswigger.net/web-security/sql-injection/cheat-sheet <br/>
https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle
# Step by step

1. Modify the request that sets the product category filter.
2. Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the category parameter:
```
'+UNION+SELECT+'abc','def'+FROM+dual--
```

<img width="1696" height="975" alt="image" src="https://github.com/user-attachments/assets/61a2e1aa-e39c-4991-9ac0-01df77fecb46" />


3. Use the following payload to display the database version:
```
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```
<img width="1919" height="1013" alt="image" src="https://github.com/user-attachments/assets/8508f12d-5db6-4bab-8bdd-6f0a72ec8736" />

# Catatan / Pembelajaran

## Ringkasan
Challenge ini memperlihatkan bagaimana **SQL Injection UNION attack** dapat digunakan untuk mengekstrak informasi sensitif dari database. Dengan mengetahui jumlah kolom dan tipe data yang cocok, attacker dapat menampilkan **versi database Oracle** langsung ke halaman aplikasi.

## Konsep Serangan
1. **Identifikasi jumlah kolom**  
   - UNION query hanya bisa berhasil jika jumlah kolom dan tipe data sesuai dengan query asli.  
   - Payload uji:  
     ```sql
     ' UNION SELECT 'abc','def' FROM dual--
     ```
   - Hasil menunjukkan query mengembalikan **2 kolom** bertipe teks.  

2. **Ekstraksi informasi database**  
   - Oracle menyimpan versi database di view `v$version`.  
   - Payload:  
     ```sql
     ' UNION SELECT BANNER,NULL FROM v$version--
     ```
   - Output `BANNER` berisi string versi database (misalnya: `Oracle Database 12c ...`).  

## Pembelajaran
1. **UNION attack memungkinkan data dari tabel lain ditampilkan di aplikasi** jika query tidak diamankan.  
2. **Oracle memiliki perbedaan sintaks** dengan DBMS lain:  
   - Wajib query ke `dual` untuk SELECT tanpa tabel.  
   - Informasi versi bisa diambil dari `v$version`.  
3. **Langkah sistematis dalam SQLi UNION attack**:  
   - Tentukan jumlah kolom.  
   - Temukan kolom yang menerima string/text.  
   - Gunakan kolom tersebut untuk menampilkan data sensitif.  
4. **Perlindungan yang benar**:  
   - Gunakan **parameterized queries** / **prepared statements**.  
   - Batasi hak akses database (principle of least privilege).  
   - Jangan tampilkan error SQL langsung ke user, karena itu mempercepat enumerasi attacker.  










