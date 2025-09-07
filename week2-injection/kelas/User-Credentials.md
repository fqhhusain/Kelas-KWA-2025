# Soal 

- **Kategori**: Web – Injection  
- **Poin**    : ★★★★ (4 bintang)  
- **Judul**   : User Credentials  
- **Tag**     : With Coding Challenge  

---

## Deskripsi  
Retrieve a list of all user credentials via SQL Injection.  

---

## Hint  
1. Cari tahu di mana data user disimpan dan bagaimana query dilakukan.  
2. Temukan endpoint yang mengambil data dari database dan bisa dipengaruhi input user.  
3. Buat serangan **UNION SELECT** untuk menggabungkan tabel `users` dengan query asli.  
4. Perbaiki error query secara bertahap hingga injeksi berhasil.  
5. Eksperimen payload beberapa kali sampai menemukan kombinasi kolom yang cocok.  

---

# Resource
- [SQL Injection Cheat Sheet – PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet)  
- [SQLite schema and metadata tables](https://www.sqlite.org/schematab.html)  

---

# Step by step

1. **Identifikasi endpoint rentan**  
   - Endpoint search di aplikasi:  
     ```
     http://127.0.0.1:3000/rest/products/search?q=<payload>
     ```
   - Parameter `q` rawan SQL injection.

2. **Uji jumlah kolom**  
   - Query asli mengembalikan 9 kolom (berdasarkan uji `UNION SELECT NULL,...`).  
   - Maka payload harus punya **9 nilai** agar query valid.

3. **Craft payload untuk dump credentials**  
   - Tabel target: `users`.  
   - Kolom penting: `id`, `email`, `password`.  
   - Payload yang berhasil:  
     ```sql
     M')) UNION SELECT id,email,password,4,5,6,7,8,9 FROM users--
     ```

4. **Eksekusi payload**  
   - Kirim payload via parameter `q`.  
   - Aplikasi merespon dengan data user (email + password hash) ditampilkan dalam hasil search.

5. **Hasil**  

<img width="1053" height="54" alt="image" src="https://github.com/user-attachments/assets/6c5c8658-50ec-41d0-a612-51f2cd0c1a38" />

<img width="1805" height="473" alt="image" src="https://github.com/user-attachments/assets/4cb2781e-6a46-45bf-a58a-73eaf7a016e1" />

- name = email user
- description = password hash (MD5/SHA1)

# Catatan / Pembelajaran

## Konsep yang Dipelajari
- **SQL Injection** memungkinkan penyerang untuk memodifikasi query SQL yang dieksekusi server.
- **UNION SELECT** dipakai untuk menggabungkan hasil query asli dengan data dari tabel lain (contoh: `users`).
- Agar payload valid, jumlah kolom dan tipe data dalam `UNION SELECT` harus cocok dengan query asli.
- Data sensitif (email, password hash) dapat dieksfiltrasi jika aplikasi menampilkan output langsung dari database.

## Insight dari Challenge
1. Endpoint pencarian (`q`) ternyata tidak melakukan filtering atau parameterized query → rentan SQLi.  
2. Mapping kolom hasil query berbeda:
   - `name` → email user  
   - `description` → password hash  
3. Hash user dapat di-*crack* dengan tools seperti **hashcat** atau **john the ripper**, sehingga membuka jalan ke login sebagai admin.




