# Soal 

- **Kategori**: Web – Injection  
- **Poin**    : ★★★★ (4 bintang)  
- **Judul**   : NoSQL Manipulation  
- **Tag**     : With Coding Challenge  

---

## Deskripsi  
Update multiple product reviews at the same time.  

---

## Hint  
1. Perhatikan bagaimana **UPDATE-statements** bekerja pada MongoDB.  
2. Challenge ini membutuhkan **Injection attack** klasik.  
3. Pahami juga bagaimana **Query Operators** bekerja di MongoDB.  

---

# Resource  

- [NoSQL Injection PortSwigger](https://portswigger.net/web-security/nosql-injection)  
- https://github.com/juice-shop/juice-shop
---

# Step by step  

1. **Menemukan Endpoint**
   - Endpoint target adalah:
     ```
     /rest/products/reviews
     ```
   - Secara default, aplikasi menggunakan **PUT** untuk memperbarui review **hanya pada satu produk**.

<img width="1376" height="671" alt="Screenshot 2025-09-07 140646" src="https://github.com/user-attachments/assets/35e985bc-d715-42b7-a4c2-4addd0375c0c" />

2. **Eksploitasi**
   - Kita ingin memperbarui **semua review sekaligus**.
   - Untuk itu, ganti method menjadi **PATCH**.
   - Ubah parameter `author` menjadi query operator MongoDB.  
   - Gunakan `$ne: -1` agar kondisi match pada **semua dokumen**.

   Payload yang digunakan:
   ```json
   {
     "message": "hai",
     "author": { "$ne": -1 }
   }
   ```

<img width="1464" height="793" alt="Screenshot 2025-09-07 135902" src="https://github.com/user-attachments/assets/a0a69ef9-57ef-4e12-8a9f-09b0e5e19945" />

# Catatan / Pembelajaran

- **NoSQL Injection** memungkinkan manipulasi query MongoDB menggunakan operator JSON (`$ne`, `$gt`, `$lt`, dll.).  
- Berbeda dengan **SQL Injection** berbasis string, serangan ini menyalahgunakan **struktur query** (JSON-based).  
- Endpoint yang tidak memvalidasi input user dapat dieksploitasi untuk:  
  - **Mass update** (semua data berubah sekaligus).  
  - **Data exfiltration** (mencuri data sensitif).  
