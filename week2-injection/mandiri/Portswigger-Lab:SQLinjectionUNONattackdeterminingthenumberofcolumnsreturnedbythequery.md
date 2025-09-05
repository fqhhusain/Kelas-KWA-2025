# Soal

- **Kategori**: SQL injection - UNION attacks
- **Diff**    : PRACTITIONER  
- **Judul**   : Lab: SQL injection UNION attack, determining the number of columns returned by the query

## Deskripsi 
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack.

To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values.

# Resource
https://portswigger.net/web-security/sql-injection/cheat-sheet <br/>
https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns
# Step by step

1. modify the product category filter.
2. Modify the category parameter, giving it the value `'+UNION+SELECT+NULL--`. Observe that an error occurs.
3. Modify the category parameter to add an additional column containing a null value:
```
'+UNION+SELECT+NULL,NULL--
```
4. Continue adding null values until the error disappears and the response includes additional content containing the null values.

```
'+UNION+SELECT+NULL,NULL,NULL--
```

<img width="1761" height="919" alt="image" src="https://github.com/user-attachments/assets/73495005-d913-46cf-a78a-ac834ae00713" />

# Catatan / Pembelajaran

## Ringkasan
Lab ini mengajarkan teknik **SQL Injection (UNION attack)** untuk menentukan berapa banyak kolom yang dikembalikan oleh query asli. Mengetahui jumlah kolom adalah langkah awal penting sebelum melakukan ekstraksi data menggunakan `UNION SELECT` karena `UNION` mengharuskan jumlah kolom dan (umumnya) tipe data kompatibel antara kedua query.

> Tujuan praktis lab: temukan jumlah kolom dengan menyuntikkan baris tambahan berisi `NULL` sampai response tidak lagi error dan menampilkan hasil gabungan.

---

## Konsep Serangan
- **UNION SELECT** menggabungkan hasil dari dua query. Agar sukses, kedua query harus mengembalikan jumlah kolom yang sama.  
- Menggunakan `NULL` sebagai placeholder kolom membantu menghindari perbedaan tipe data (NULL cocok untuk hampir semua tipe).  
- Teknik yang dipakai di lab: mulai dari satu `NULL`, lalu tambah satu per satu (`NULL,NULL`, `NULL,NULL,NULL`, ...) sampai response tidak lagi error — saat itu jumlah `NULL` = jumlah kolom.

