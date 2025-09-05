- **Kategori**: Web – Injection  
- **Poin**    : ★★★ (3 bintang)  
- **Judul**   : Database Schema  
- **Tag**     : With Coding Challenge  

## Deskripsi 
Exfiltrate the entire DB schema definition via SQL Injection.

## Hint 
1. Find out where this information could come from. Then craft an attack string against an endpoint that offers an unnecessary way to filter data.  
2. Find out which database system is in use and where it would usually store its schema definitions.  
3. Craft a UNION SELECT attack string to join the relevant data from any such identified system table into the original result.  
4. You might have to tackle some query syntax issues step-by-step, basically hopping from  
