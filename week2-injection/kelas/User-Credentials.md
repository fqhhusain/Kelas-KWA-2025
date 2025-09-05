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
1. Gather information on where user data is stored and how it is addressed. Then craft a corresponding **UNION SELECT** attack.  
2. Try to find an endpoint where you can influence data being retrieved from the server.  
3. Craft a **UNION SELECT** attack string to join data from another table into the original result.  
4. You might have to tackle some query syntax issues step-by-step, basically hopping from one error to the next.  
5. As with *"Order the Christmas special offer of 2014"*, expect to experiment until you find the correct payload.  
