# Soal
- **Kategori**: Web – Injection  
- **Poin**    : ★★★★ (4 bintang)  
- **Judul**   : Christmas Special  
- **Tag**     : (tidak ada tag khusus)  

## Deskripsi 
Order the Christmas special offer of 2014.

## Hint 
1. Find out how the application handles unavailable products and try to find a loophole.  
2. Find out how the application hides deleted products from its customers.  
3. Try to craft an attack string that makes deleted products visible again.  
4. You need to get the deleted product into your shopping cart and trigger the Checkout.  
5. Neither of the above can be achieved through the application frontend and it might even require (half-)Blind SQL Injection.

# Resource
https://github.com/juice-shop/juice-shop

1. Temukan titik injeksi (product search)

   - Buka fitur Search di UI dan amati request network yang dipanggil:
     GET /rest/products/search?q=<payload>
   - Atau panggil langsung di browser: http://localhost:3000/rest/products/search?q=test
2. Uji dan injeksi SQL pada parameter q

   - Intercept request (Burp/DevTools) dan ganti nilai q dengan payload untuk menampilkan item yang dihapus.
     test')) UNION SELECT * FROM PRODUCTS WHERE deletedAt IS NOT NULL--
4. Periksa response untuk menemukan produk tersembunyi

   - Lihat JSON hasil response. Cari item yang sebelumnya tidak terlihat (Christmas Super-Surprise-Box (2014 Edition)).
   - Catat id produk tersebut — ProductId = 10.
5. Dapatkan BasketId milik akun Anda

   - Login ke akun user biasa.
   - Tambahkan satu produk biasa dari UI ke keranjang.
   - Tangkap request POST /api/BasketItems saat menambahkan
6. Tambahkan produk tersembunyi ke keranjang burpsuite

<img width="1919" height="615" alt="image" src="https://github.com/user-attachments/assets/6dda0fd4-34e5-48f2-aec0-56aa2197179f" />


<img width="1488" height="816" alt="image" src="https://github.com/user-attachments/assets/60956642-659b-4610-93ec-2baab060c5d3" />

<img width="1896" height="906" alt="image" src="https://github.com/user-attachments/assets/fe248089-2403-474d-80c7-f321a10e644e" />

# Catatan / Pembelajaran

- Produk yang disembunyikan sering ditandai dengan field seperti deletedAt, bukan benar-benar dihapus.
- Dengan SQL Injection (UNION SELECT), penyerang bisa menampilkan kembali produk tersembunyi, seperti Christmas Special.
- Setelah mengetahui productId, penyerang bisa langsung menambahkannya ke keranjang meskipun produk tidak tampil di UI.

Pembelajaran utama:

- Soft delete tidak aman bila query rentan.
- UNION-based SQLi dapat dipakai untuk exfiltrasi maupun manipulasi bisnis.
- Mitigasi: gunakan prepared statements dan validasi server-side.

