# -Web-Server-Log-Analysis-with-Apache-Hive

# Apache Hive Web Log Analysis Project

📊 Web Log Analysis using Apache Hive on Apache Zeppelin
Overview
This project demonstrates an end-to-end web log analysis pipeline using Apache Hive and Apache Zeppelin to process and analyze Apache web server logs stored in HDFS. The solution transforms unstructured log data into meaningful insights through interactive dashboards and SQL queries.
🎯 Situation
Our team needed to analyze Apache web server logs to understand user behavior, track request patterns, and identify errors in web traffic. This required processing large, unstructured log data stored in HDFS and turning it into meaningful insights — quickly and interactively.
📋 Task
Design and implement an interactive analytics pipeline that:

Reads raw Apache log files from HDFS
Parses unstructured log data into structured format (columns like host, request, status, size, timestamp)
Runs analytical queries to extract business and operational insights such as:

Most common HTTP status codes
Most frequently accessed paths
Distribution of requests over time
Requests resulting in 404 errors and their top sources



⚡ Action
1. Environment Setup

Installed and configured Apache Zeppelin inside WSL2
Enabled Hive JDBC Interpreter by adding required dependencies:

org.apache.hive:hive-jdbc:0.14.0
org.apache.hadoop:hadoop-common:2.6.0


Configured Hive interpreter with default.user and Hive server connection URL
Set up Windows → WSL port forwarding to access Zeppelin from the host browser

2. Data Preparation

Collected web log data and stored it in HDFS under /user/dataengineer/filesdata/Web_log

3. Hive Table Creation
Created an external Hive table apachelog using RegexSerDe to parse unstructured Apache log lines with the following schema:
sqlCREATE EXTERNAL TABLE apachelog (
  HOST STRING,
  IDENTITY STRING,
  WUSER STRING,
  WTIME STRING,
  REQUEST STRING,
  STATUS INT,
  SIZE INT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
  'input.regex' = '^(\\S+) (\\S+) (\\S+) \\[([^\\]]+)\\] "([^"]*)" (\\d+) (\\d+)$'
)
LOCATION '/user/dataengineer/filesdata/Web_log';
4. Data Loading
Loaded log data from HDFS into the Hive table:
sqlLOAD DATA INPATH '/user/dataengineer/filesdata/Web_log' INTO TABLE apachelog;
5. Exploratory Data Analysis in Zeppelin
Basic Statistics
sql-- Total records count
SELECT COUNT(*) as total_records FROM apachelog;

-- Unique hosts
SELECT COUNT(DISTINCT host) as unique_hosts FROM apachelog;

-- Response size statistics
SELECT 
  MIN(size) as min_size,
  MAX(size) as max_size,
  AVG(size) as avg_size
FROM apachelog;
Status Code Analysis
sql-- Status code distribution
SELECT status, COUNT(*) as count
FROM apachelog
GROUP BY status
ORDER BY count ASC;
Top Visitors Analysis
sql-- Top visitors by request count
SELECT host, COUNT(*) as request_count
FROM apachelog
GROUP BY host
ORDER BY request_count DESC
LIMIT 10;
Path Analysis
sql-- Extract and analyze requested paths
SELECT 
  regexp_extract(request, '^\\w+\\s+([^\\s]+)', 1) as path,
  COUNT(*) as requests
FROM apachelog
GROUP BY regexp_extract(request, '^\\w+\\s+([^\\s]+)', 1)
ORDER BY requests DESC
LIMIT 20;
Error Analysis (404)
sql-- Count 404 errors
SELECT COUNT(*) as error_404_count
FROM apachelog
WHERE status = 404;

-- Top URLs causing 404 errors
SELECT 
  regexp_extract(request, '^\\w+\\s+([^\\s]+)', 1) as path,
  COUNT(*) as error_count
FROM apachelog
WHERE status = 404
GROUP BY regexp_extract(request, '^\\w+\\s+([^\\s]+)', 1)
ORDER BY error_count DESC
LIMIT 10;
Traffic Over Time
sql-- Traffic distribution by date
SELECT 
  regexp_extract(wtime, '^([^:]+)', 1) as date,
  COUNT(*) as requests
FROM apachelog
GROUP BY regexp_extract(wtime, '^([^:]+)', 1)
ORDER BY date;
🏆 Results
Key Achievements

Built an end-to-end interactive dashboard in Zeppelin to visualize and query Apache log data in near real-time
Successfully processed and analyzed large volumes of unstructured web log data
Created reusable analytical queries for ongoing monitoring and insights

Business Insights Delivered

Top Traffic Sources: Identified most active hosts and user segments
Resource Usage: Determined most and least requested web resources
Error Patterns: Found frequent error-causing URLs and broken links to fix
Traffic Trends: Analyzed traffic distribution patterns by day and hour
Performance Metrics: Established baseline metrics for response sizes and status codes

Technical Impact

Demonstrated efficiency of Hive for log analytics on large datasets stored in HDFS
Provided actionable insights for web infrastructure optimization
Established scalable framework for ongoing log analysis

🛠️ Technologies Used
TechnologyPurposeApache HiveSQL-like querying of big dataApache ZeppelinInteractive data analytics and visualizationHDFSDistributed storage for log filesRegexSerDeParsing unstructured log dataWSL2Linux environment on WindowsHiveQLQuery language for data analysis
