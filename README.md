# -Web-Server-Log-Analysis-with-Apache-Hive

# Apache Hive Web Log Analysis Project

## 📌 Overview
This project demonstrates how to use **Apache Hive** to analyze **Apache HTTP web server logs** stored in **HDFS**.  
We parse raw log data using **RegexSerDe**, store it in a Hive table, and run analytical queries to uncover insights such as:

- Request frequency over time  
- Most popular URLs and paths  
- Error trends (4xx, 5xx status codes)  
- Unique visitors based on IP addresses  

---

## 🛠 Tech Stack
- **Apache Hive** – SQL-on-Hadoop engine for querying large datasets  
- **HDFS** – Hadoop Distributed File System for storing input log files  
- **RegexSerDe** – To parse unstructured log data using regular expressions  
- **HiveQL** – SQL-like query language for analysis  

---

## 📂 Project Steps
1. **Upload Logs to HDFS**  
   ```bash
   hdfs dfs -put access_log /user/hive/logs/
