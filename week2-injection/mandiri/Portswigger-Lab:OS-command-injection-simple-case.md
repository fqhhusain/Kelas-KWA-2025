# Soal

- **Kategori**: OS Command Injection
- **Diff**    : APPRENTICE  
- **Judul**   : Lab: OS command injection, simple case

## Deskripsi 
This lab contains an OS command injection vulnerability in the product stock checker.

The application executes a shell command containing user-supplied product and store IDs, and returns the raw output from the command in its response.

To solve the lab, execute the `whoami` command to determine the name of the current user.

# Resource

https://portswigger.net/web-security/os-command-injection/lab-simple

# Step by step

1. Gunakan Burp Suite untuk intercept dan modifikasi request yang mengecek stock level.
2. Modifikasi `storeID` parameter, beri value `1|whoami`.

<img width="431" height="353" alt="image" src="https://github.com/user-attachments/assets/e2e700be-30b7-41fb-bbf7-783e5970025e" />


3. perhatikan response menampilkan name of the current user.

<img width="628" height="100" alt="image" src="https://github.com/user-attachments/assets/64184608-e210-4ebd-8e24-298d5cfbb476" />

# Catatan / Pembelajaran

## Ringkasan
Challenge ini memperlihatkan kerentanan **OS Command Injection** pada fitur product stock checker. Dengan memodifikasi parameter yang dipakai dalam perintah shell, attacker bisa mengeksekusi command arbitrer di server.  

## Konsep Serangan
- Aplikasi menjalankan perintah sistem operasi menggunakan input dari user, misalnya:
  ```bash
  stockchecker productID=2 storeID=1
  ```
- Input storeID tidak difilter, sehingga attacker bisa menyisipkan operator shell | untuk menambahkan command baru.

- Payload 1|whoami membuat perintah menjadi:
```
stockchecker productID=2 storeID=1 | whoami
```
- Hasil dari command whoami ditampilkan di response, mengungkap nama user yang menjalankan proses aplikasi.

