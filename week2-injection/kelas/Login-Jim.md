# Soal

- **Kategori**: Web – Injection  
- **Poin**    : ★★★ (3 bintang)  
- **Judul**   : Login Jim  
- **Tag**     : Tutorial, With Coding Challenge  

## Deskripsi 
Log in with Jim's user account.

## Hint 
1. The challenge description probably gave away what form you should attack.  
2. You need to know (or smart-guess) Jim’s email address so you can launch a targeted attack.  
3. If you harvested Jim’s password hash, you can try to attack that instead of using SQL Injection.  


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
     jim@juice-sh.op
     ```

4. **Uji Input Injeksi**
   - Pada field **Email/Username** masukkan payload:
     ```
     jim@juice-sh.op' --
     ```
   - Password bisa diisi sembarang (karena akan diabaikan oleh query akibat komentar `--`).

<img width="454" height="404" alt="image" src="https://github.com/user-attachments/assets/37717507-8c35-4d36-9707-5b8739f31abd" />

5. **Eksekusi**
   - Klik **Log in**
   - Jika berhasil, sistem melewati autentikasi dan langsung masuk sebagai **jim@juice-sh.op**.

6. **Verifikasi**
   - Cek menu **Account**
   - Username terdeteksi sebagai **jim@juice-sh.op**

<img width="332" height="329" alt="image" src="https://github.com/user-attachments/assets/939bfe7f-bd9f-44dd-8d79-11690ec1beea" />

---

# Catatan / Pembelajaran

## Ringkasan
Challenge **Login Bender** menunjukkan bagaimana sebuah form login yang tidak aman dapat dimanipulasi menggunakan **SQL Injection targeted**. Dengan mengetahui email target, penyerang bisa login sebagai **jim** tanpa mengetahui password aslinya.

---

## Konsep Serangan
- **SQL Injection** terjadi ketika input user langsung dimasukkan ke query SQL tanpa validasi atau parameterisasi.
- Payload `bender@juice-sh.op' --` mengomentari bagian pengecekan password:
  ```sql
  SELECT * FROM Users 
  WHERE email = 'jim@juice-sh.op' -- ' 
  AND password = 'xyz';
  ```
- Bagian -- mengomentari sisa query sehingga password diabaikan.
- Akibatnya, aplikasi menganggap login valid dan memberikan akses
