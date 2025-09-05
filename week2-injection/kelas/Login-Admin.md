# Soal

- **Kategori**: Web – Injection  
- **Poin**    : ★★ (2 bintang)  
- **Judul**   : Login Admin  
- **Tag**     : Tutorial, Good for Demos, With Coding Challenge  

## Deskripsi 
Log in with the administrator's user account.

## Hint 
1. The challenge description probably gave away what form you should attack.  
2. If you happen to know the email address of the admin already, you can launch a targeted attack.  
3. You might be lucky with a dedicated attack pattern even if you have no clue about the admin email address.  
4. If you harvested the admin’s password hash, you can of course try to attack that instead of using SQL Injection.  
5. Alternatively you can solve this challenge as a combo with other challenges.  


# Resource

https://github.com/juice-shop/juice-shop

# Step by step

1. **Menjalankan Juice Shop**
   - Gunakan Docker:  
     ```bash
     docker run --rm -p 127.0.0.1:3000:3000 bkimminich/juice-shop
     ```
   - Akses di browser: [http://localhost:3000](http://localhost:3000)

2. **Membuka Halaman Login**
   - Klik tombol **Account → Log in**
   - Tersedia dua input: *Email* dan *Password*

3. **Uji Input Injeksi**
   - Pada field **Email/Username** masukkan payload:
     ```
     ' OR '1'='1' -- 
     ```
   - Password bisa diisi sembarang (karena akan diabaikan oleh query akibat komentar `--`).
  
  <img width="465" height="268" alt="image" src="https://github.com/user-attachments/assets/54d02b88-efbd-45c5-9bf4-bef7037f6fa1" />


4. **Eksekusi**
   - Klik **Log in**
   - Jika berhasil, sistem melewati autentikasi dan langsung masuk sebagai admin@juice-sh.op.

5. **Verifikasi**
   - Cek menu **Account**
   - Username terdeteksi sebagai admin@juice-sh.op

<img width="312" height="325" alt="image" src="https://github.com/user-attachments/assets/d2f62dea-25ce-4b69-bbe4-478d1e732666" />


# Catatan / Pembelajaran


## Ringkasan
Challenge **Login Admin** menunjukkan bagaimana sebuah form login yang tidak aman dapat dimanipulasi menggunakan **SQL Injection**. Dengan payload sederhana, penyerang bisa masuk sebagai **administrator** tanpa mengetahui kredensial asli.

---

## Konsep Serangan
- **SQL Injection** terjadi ketika input user langsung dimasukkan ke query SQL tanpa validasi atau parameterisasi.
- Payload `' OR '1'='1' --` membuat kondisi **WHERE** selalu bernilai benar:
  ```sql
  SELECT * FROM Users WHERE email = '' OR '1'='1' -- ' AND password = 'xyz';
