# Soal

- **Kategori**: Web – Injection  
- **Poin**    : ★★★ (3 bintang)  
- **Judul**   : Login Bender  
- **Tag**     : Tutorial, With Coding Challenge  

## Deskripsi 
Log in with Bender's user account.

## Hint 
1. The challenge description probably gave away what form you should attack.  
2. You need to know (or smart-guess) Bender’s email address so you can launch a targeted attack.  
3. Bender’s password hash might not help you very much.  
4. In case you try some other approach than SQL Injection, you will notice that Bender’s password hash is not very useful.  

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

3. **Menentukan Target Email**
   - Berdasarkan knowledge base Juice Shop, akun Bender menggunakan email:  
     ```
     bender@juice-sh.op
     ```

4. **Uji Input Injeksi**
   - Pada field **Email/Username** masukkan payload:
     ```
     bender@juice-sh.op' --
     ```
   - Password bisa diisi sembarang (karena akan diabaikan oleh query akibat komentar `--`).

<img width="449" height="455" alt="image" src="https://github.com/user-attachments/assets/8c3b09c5-7f57-4479-90f3-20686635d241" />

5. **Eksekusi**
   - Klik **Log in**
   - Jika berhasil, sistem melewati autentikasi dan langsung masuk sebagai **bender@juice-sh.op**.

6. **Verifikasi**
   - Cek menu **Account**
   - Username terdeteksi sebagai **bender@juice-sh.op**

<img width="338" height="329" alt="image" src="https://github.com/user-attachments/assets/12ac6530-17e0-4e5a-92cf-048b080e9604" />

---

# Catatan / Pembelajaran

## Ringkasan
Challenge **Login Bender** menunjukkan bagaimana sebuah form login yang tidak aman dapat dimanipulasi menggunakan **SQL Injection targeted**. Dengan mengetahui email target, penyerang bisa login sebagai **Bender** tanpa mengetahui password aslinya.

---

## Konsep Serangan
- **SQL Injection** terjadi ketika input user langsung dimasukkan ke query SQL tanpa validasi atau parameterisasi.
- Payload `bender@juice-sh.op' --` mengomentari bagian pengecekan password:
  ```sql
  SELECT * FROM Users 
  WHERE email = 'bender@juice-sh.op' -- ' 
  AND password = 'xyz';
  ```
- Bagian -- mengomentari sisa query sehingga password diabaikan.
- Akibatnya, aplikasi menganggap login valid dan memberikan akses


