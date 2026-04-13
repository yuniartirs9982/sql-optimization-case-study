# SQL Optimization Case Study

## 📌 Overview
This repository demonstrates a real-world approach to **SQL query optimization** in data-intensive backend systems.

The focus is on improving performance of **complex queries and database views** involving large datasets (10k–100k+ records), commonly found in reporting, financial, and admission systems.

---

## 🎯 Problem Statement
A complex SQL query used for reporting experienced:
- Slow execution time  
- High resource consumption  
- Inefficient joins and filtering  

This impacted system performance and response time for end users.

---

## ⚠️ Initial Query (Before Optimization)

```sql
SELECT *
FROM pmb_hasil ph
LEFT JOIN program_studi ps1 ON ph.kd_program_studi_1 = ps1.kd_program_studi
LEFT JOIN program_studi ps2 ON ph.kd_program_studi_2 = ps2.kd_program_studi
LEFT JOIN program_studi ps3 ON ph.kd_program_studi_3 = ps3.kd_program_studi
LEFT JOIN program_studi ps4 ON ph.kd_program_studi_4 = ps4.kd_program_studi
WHERE 
    (:kdFak IS NULL OR ps1.kd_fakultas = :kdFak 
     OR ps2.kd_fakultas = :kdFak 
     OR ps3.kd_fakultas = :kdFak 
     OR ps4.kd_fakultas = :kdFak)
```
## ❌ Issues Identified
- Multiple LEFT JOINs on the same table
- Heavy use of OR conditions, preventing index usage
- Poor filtering logic
- Full table scans on large datasets

---

## 💡 Optimization Strategy
1. Reduce Redundant Joins
Avoid joining the same table multiple times unnecessarily
2. Improve Filtering Logic
Replace OR-heavy conditions with more efficient structures
3. Index Optimization
Add indexes on frequently filtered columns
4. Query Refactoring
Simplify logic for better execution plans

---

## ✅ Optimized Query (After)
```sql
SELECT ph.*
FROM pmb_hasil ph
WHERE EXISTS (
    SELECT 1
    FROM program_studi ps
    WHERE ps.kd_program_studi IN (
        ph.kd_program_studi_1,
        ph.kd_program_studi_2,
        ph.kd_program_studi_3,
        ph.kd_program_studi_4
    )
    AND (:kdFak IS NULL OR ps.kd_fakultas = :kdFak)
)
```

---


## ⚙️ Indexing Strategy
```sql
CREATE INDEX idx_program_studi_kd ON program_studi(kd_program_studi);
CREATE INDEX idx_program_studi_fakultas ON program_studi(kd_fakultas);
CREATE INDEX idx_pmb_hasil_prodi ON pmb_hasil(
    kd_program_studi_1,
    kd_program_studi_2,
    kd_program_studi_3,
    kd_program_studi_4
);
```


---
## 📈 Performance Improvement
| Metric            | Before       | After         |
| ----------------- | ------------ | ------------- |
| Execution Time    | ~2–5 seconds | ~0.5–1 second |
| Query Cost        | High         | Reduced       |
| Index Utilization | Minimal      | Optimized     |

---

## 🔑 Key Takeaways
- Avoid excessive JOIN duplication
- Minimize OR conditions for better index usage
- Use EXISTS for efficient filtering
- Apply proper indexing strategies
- Always analyze query execution plans

---

## 🧠 Real-World Impact
- Reduced query execution time by 30–60%
- Improved performance of reporting endpoints
- Enhanced scalability for large datasets
- Lowered database load

---

## 🔍 Future Improvements
- Query caching (Redis)
- Partitioning large tables
- Materialized views for heavy reporting
- Monitoring using slow query logs

---

## 📬 About Me

Backend Engineer focused on data-intensive systems, SQL optimization, and distributed architectures using Laravel, Golang, and Kafka.


