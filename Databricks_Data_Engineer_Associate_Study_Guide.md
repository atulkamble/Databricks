# Databricks Certified Data Engineer Associate - Study Guide

## Table of Contents
1. [Overview](#overview)
2. [Important Points to Remember](#important-points-to-remember)
3. [Study Notes by Topic](#study-notes-by-topic)
4. [Practice MCQs with Answers](#practice-mcqs-with-answers)
5. [Exam Preparation Checklist](#exam-preparation-checklist)

---

## Overview

**Certification:** Databricks Certified Data Engineer Associate  
**Purpose:** Validates ability to use Databricks Data Intelligence Platform for introductory data engineering tasks  
**Validity:** 2 years (recertification required)

---

## Important Points to Remember

### ⭐ Exam Logistics
- **Total Questions:** 45 scored questions (plus unscored statistical items)
- **Time Limit:** 90 minutes (~2 minutes per question)
- **Passing Requirements:** Not publicly disclosed (typically 70%+)
- **Cost:** $200 USD
- **Format:** Multiple choice, online proctored or test center
- **Test Aids:** None allowed (no notes, documentation, or external resources)
- **Languages Available:** English, Japanese, Portuguese BR, Korean
- **Prerequisites:** None, but 6+ months hands-on experience recommended

### 📊 Exam Weight Distribution
1. **Databricks Intelligence Platform:** 10% (~4-5 questions)
2. **Development and Ingestion:** 30% (~13-14 questions)
3. **Data Processing & Transformations:** 31% (~14 questions)
4. **Productionizing Data Pipelines:** 18% (~8 questions)
5. **Data Governance & Quality:** 11% (~5 questions)

### 🎯 Key Success Factors
- Focus on **Development and Ingestion** (30%) and **Data Processing** (31%) - these make up 61% of the exam
- Code examples are primarily in **SQL**, with Python (PySpark) for non-SQL scenarios
- Hands-on experience is crucial - theory alone won't suffice
- Understand the Lakehouse architecture and Unity Catalog thoroughly
- Practice with Databricks workflows and job scheduling

### 🚨 Critical Concepts
- **Delta Lake** transactions and ACID properties
- **Unity Catalog** for governance
- **Apache Spark SQL** and **PySpark** fundamentals
- **ETL patterns** and best practices
- **Databricks Workflows** and job orchestration
- **Data quality** and validation techniques

---

## Study Notes by Topic

### 1. Databricks Intelligence Platform (10%)

#### Key Concepts:
- **Lakehouse Architecture:** Combines data warehouse and data lake capabilities
- **Workspace Components:**
  - Notebooks (collaborative coding environment)
  - Dashboards (data visualization)
  - Repos (Git integration)
  - Workflows (job orchestration)
  - Data Explorer (metadata browsing)
  - SQL Warehouses (query compute)
  
- **Cluster Types:**
  - **All-Purpose Clusters:** Interactive workloads, manual creation
  - **Job Clusters:** Automated jobs, created and terminated automatically
  
- **Databricks Runtime:** Pre-configured Spark environment with optimizations

#### Important Points:
✓ Understand workspace navigation and component purposes  
✓ Know when to use all-purpose vs. job clusters  
✓ Recognize cost optimization strategies (auto-termination, right-sizing)  
✓ Understand compute resource management  

---

### 2. Development and Ingestion (30%)

#### Key Concepts:

**Data Ingestion Methods:**
- **Auto Loader (cloudFiles):** Incremental ingestion from cloud storage
- **COPY INTO:** SQL-based file ingestion with idempotency
- **Streaming:** Real-time data ingestion with Structured Streaming
- **Batch:** Traditional batch file processing

**Auto Loader Benefits:**
- Automatic schema inference and evolution
- Checkpoint management for exactly-once processing
- Efficient handling of millions of files
- Built-in retry logic

**Data Sources:**
- Cloud storage (S3, ADLS, GCS)
- JDBC/ODBC connections
- Kafka/Event Hubs
- Delta tables
- CSV, JSON, Parquet, Avro files

**Lakeflow Connect:**
- Simplified data ingestion from various sources
- Pre-built connectors for popular data sources

#### Important Points:
✓ Auto Loader is preferred for incremental file ingestion  
✓ COPY INTO provides idempotent batch ingestion  
✓ Understand checkpoint locations for streaming jobs  
✓ Know different file formats and when to use each  
✓ Schema enforcement vs. schema evolution strategies  

---

### 3. Data Processing & Transformations (31%)

#### Key Concepts:

**Apache Spark SQL:**
```sql
-- Window functions
SELECT 
  customer_id,
  order_date,
  amount,
  SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date) as running_total
FROM orders;

-- CTEs (Common Table Expressions)
WITH recent_orders AS (
  SELECT * FROM orders WHERE order_date >= '2024-01-01'
)
SELECT customer_id, COUNT(*) FROM recent_orders GROUP BY customer_id;

-- Joins and aggregations
SELECT c.name, COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.name;
```

**PySpark Transformations:**
```python
from pyspark.sql.functions import col, sum, avg, when, lit

# Basic transformations
df = df.filter(col("amount") > 100) \
       .select("customer_id", "amount", "date") \
       .groupBy("customer_id") \
       .agg(sum("amount").alias("total_amount"))

# User Defined Functions (UDFs)
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

@udf(returnType=StringType())
def categorize_amount(amount):
    if amount > 1000:
        return "High"
    elif amount > 500:
        return "Medium"
    else:
        return "Low"

df = df.withColumn("category", categorize_amount(col("amount")))
```

**Complex Data Handling:**
- **Nested Structures:** JSON, structs, arrays, maps
- **Explode:** Converting arrays to rows
- **Struct Operations:** Accessing nested fields
- **Higher-order Functions:** transform, filter, aggregate on arrays

**Delta Lake Operations:**
```sql
-- Merge (Upsert)
MERGE INTO target_table t
USING source_table s
ON t.id = s.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;

-- Time Travel
SELECT * FROM table_name VERSION AS OF 10;
SELECT * FROM table_name TIMESTAMP AS OF '2024-01-01';

-- Optimize
OPTIMIZE table_name;
OPTIMIZE table_name ZORDER BY (column1, column2);

-- Vacuum (remove old files)
VACUUM table_name RETAIN 168 HOURS;
```

#### Important Points:
✓ Master window functions (RANK, ROW_NUMBER, LEAD, LAG)  
✓ Understand when to use UDFs vs. built-in functions (built-in is faster)  
✓ Know Delta Lake operations: MERGE, DELETE, UPDATE  
✓ Understand partitioning and optimization strategies  
✓ Complex data types: arrays, structs, maps  
✓ Time travel capabilities for auditing and debugging  

---

### 4. Productionizing Data Pipelines (18%)

#### Key Concepts:

**Databricks Workflows:**
- **Jobs:** Scheduled or triggered execution of notebooks/scripts
- **Tasks:** Individual units of work within a job
- **Dependencies:** Task orchestration and sequencing
- **Clusters:** Job clusters vs. shared clusters

**Job Configuration:**
- **Scheduling:** Cron expressions, manual triggers, file arrival triggers
- **Parameters:** Passing values to notebooks/scripts
- **Retries:** Automatic retry logic for failures
- **Alerts:** Email/webhook notifications on success/failure
- **Concurrent Runs:** Policy for handling overlapping executions

**Delta Live Tables (DLT):**
```python
import dlt
from pyspark.sql.functions import col

# Bronze layer - raw ingestion
@dlt.table(
    comment="Raw sensor data"
)
def bronze_sensors():
    return spark.readStream.format("cloudFiles") \
        .option("cloudFiles.format", "json") \
        .load("/path/to/source")

# Silver layer - cleaned data
@dlt.table(
    comment="Cleaned sensor data"
)
@dlt.expect_or_drop("valid_timestamp", "timestamp IS NOT NULL")
def silver_sensors():
    return dlt.read_stream("bronze_sensors") \
        .filter(col("reading") > 0)

# Gold layer - aggregated data
@dlt.table(
    comment="Hourly sensor averages"
)
def gold_sensor_hourly():
    return dlt.read("silver_sensors") \
        .groupBy("sensor_id", "hour") \
        .agg(avg("reading").alias("avg_reading"))
```

**Pipeline Monitoring:**
- Job run history and metrics
- Pipeline lineage tracking
- Error handling and logging
- Performance monitoring

#### Important Points:
✓ Understand job vs. task configuration  
✓ Know scheduling options (cron, triggers)  
✓ DLT expectations for data quality  
✓ Pipeline dependencies and orchestration  
✓ Error handling strategies  
✓ Job cluster lifecycle management  

---

### 5. Data Governance & Quality (11%)

#### Key Concepts:

**Unity Catalog:**
- **Three-level Namespace:** catalog.schema.table
- **Access Control:** Fine-grained permissions (SELECT, INSERT, UPDATE, DELETE)
- **Data Lineage:** Automatic tracking of data dependencies
- **Data Discovery:** Centralized metadata management
- **Secure Sharing:** Delta Sharing for external collaboration

**Unity Catalog Structure:**
```
Metastore (Account-level)
└── Catalog
    └── Schema (Database)
        ├── Tables
        ├── Views
        ├── Functions
        └── Volumes (for files)
```

**Access Control:**
```sql
-- Grant permissions
GRANT SELECT ON TABLE catalog.schema.table TO `user@example.com`;
GRANT USAGE ON CATALOG catalog TO `data_engineers`;
GRANT CREATE TABLE ON SCHEMA catalog.schema TO `etl_role`;

-- Revoke permissions
REVOKE SELECT ON TABLE catalog.schema.table FROM `user@example.com`;
```

**Data Quality:**
- **Expectations:** Data validation rules in DLT
- **Constraints:** NOT NULL, UNIQUE, CHECK constraints
- **Data Profiling:** Statistical analysis of data
- **Testing:** Unit tests for transformations

**Data Quality Patterns:**
```python
# DLT Expectations
@dlt.expect("valid_email", "email LIKE '%@%.%'")
@dlt.expect_or_fail("required_id", "id IS NOT NULL")
@dlt.expect_or_drop("valid_date", "date >= '2020-01-01'")

# Table constraints
CREATE TABLE customers (
  id BIGINT NOT NULL,
  email STRING,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP(),
  CONSTRAINT valid_email CHECK (email LIKE '%@%.%')
);
```

#### Important Points:
✓ Unity Catalog three-level namespace  
✓ GRANT/REVOKE syntax and permissions  
✓ DLT expectations: expect, expect_or_fail, expect_or_drop  
✓ Data lineage and discovery capabilities  
✓ Table constraints and validation  
✓ Delta Sharing for secure external access  

---

## Practice MCQs with Answers

### Section 1: Databricks Intelligence Platform (10%)

**Q1. Which cluster type is automatically created and terminated for each job run?**
- A. All-Purpose Cluster
- B. Job Cluster
- C. Interactive Cluster
- D. High-Concurrency Cluster

**Answer: B. Job Cluster**  
*Explanation: Job clusters are created automatically when a job starts and terminated when it completes, optimizing cost. All-purpose clusters are manually created and persist until manually terminated.*

---

**Q2. Which workspace component is used for Git integration in Databricks?**
- A. Notebooks
- B. Repos
- C. Workflows
- D. Data Explorer

**Answer: B. Repos**  
*Explanation: Repos provide Git integration for version control of notebooks and code. This enables collaboration and CI/CD workflows.*

---

**Q3. What is the primary benefit of the Databricks Lakehouse architecture?**
- A. It only supports structured data
- B. It combines the benefits of data lakes and data warehouses
- C. It eliminates the need for data governance
- D. It only works with Delta format

**Answer: B. It combines the benefits of data lakes and data warehouses**  
*Explanation: Lakehouse architecture provides the flexibility and cost-effectiveness of data lakes with the ACID transactions and performance of data warehouses.*

---

**Q4. What is the purpose of auto-termination for clusters in Databricks?**
- A. To prevent unauthorized access
- B. To automatically shut down inactive clusters to reduce costs
- C. To improve cluster performance
- D. To enable load balancing

**Answer: B. To automatically shut down inactive clusters to reduce costs**  
*Explanation: Auto-termination automatically stops clusters after a specified period of inactivity, helping to control cloud computing costs while preventing unnecessary resource usage.*

---

**Q5. Which Databricks component provides a SQL interface for querying data?**
- A. Databricks SQL
- B. MLflow
- C. Repos
- D. Jobs

**Answer: A. Databricks SQL**  
*Explanation: Databricks SQL (formerly SQL Analytics) provides a native SQL interface with SQL warehouses for running queries, creating dashboards, and performing analytics.*

---

### Section 2: Development and Ingestion (30%)

**Q6. Which ingestion method is recommended for incrementally processing millions of files from cloud storage?**
- A. COPY INTO
- B. spark.read.format()
- C. Auto Loader (cloudFiles)
- D. JDBC connection

**Answer: C. Auto Loader (cloudFiles)**  
*Explanation: Auto Loader is optimized for incremental file processing, automatically handles schema inference and evolution, and efficiently processes millions of files.*

---

**Q7. What is a key difference between Auto Loader and COPY INTO?**
- A. COPY INTO supports schema evolution, Auto Loader does not
- B. Auto Loader automatically tracks processed files, COPY INTO requires manual tracking
- C. COPY INTO is for streaming, Auto Loader is for batch only
- D. Auto Loader only works with CSV files

**Answer: B. Auto Loader automatically tracks processed files, COPY INTO requires manual tracking**  
*Explanation: Auto Loader maintains checkpoint information automatically, while COPY INTO relies on file-level idempotency and requires more manual management.*

---

**Q8. Which format provides ACID transactions in Databricks?**
- A. CSV
- B. JSON
- C. Parquet
- D. Delta Lake

**Answer: D. Delta Lake**  
*Explanation: Delta Lake extends Parquet with ACID transaction capabilities, enabling reliable reads and writes with versioning and time travel.*

---

**Q9. What does the checkpoint location do in streaming jobs?**
- A. Stores the final output data
- B. Tracks processed data to ensure exactly-once processing
- C. Backs up the cluster configuration
- D. Stores error logs

**Answer: B. Tracks processed data to ensure exactly-once processing**  
*Explanation: Checkpoints maintain the state of a streaming query, tracking what data has been processed to provide fault tolerance and exactly-once semantics.*

---

**Q10. Which Auto Loader option is used to infer the schema from incoming data?**
- A. cloudFiles.schemaLocation
- B. cloudFiles.inferColumnTypes
- C. cloudFiles.format
- D. cloudFiles.useInferredSchema

**Answer: B. cloudFiles.inferColumnTypes**  
*Explanation: Setting cloudFiles.inferColumnTypes to "true" enables Auto Loader to automatically infer column types from the data, rather than treating all columns as strings.*

---

**Q11. What is the purpose of the COPY INTO command in Databricks?**
- A. To copy data between two Delta tables
- B. To perform idempotent batch ingestion from files
- C. To duplicate a table structure
- D. To copy notebooks between workspaces

**Answer: B. To perform idempotent batch ingestion from files**  
*Explanation: COPY INTO loads data from files into tables with built-in deduplication, ensuring that files are processed only once even if the command is re-executed.*

---

**Q12. Which streaming trigger mode processes data as soon as it arrives?**
- A. trigger(once=True)
- B. trigger(processingTime='1 minute')
- C. trigger(continuous='1 second')
- D. trigger(availableNow=True)

**Answer: C. trigger(continuous='1 second')**  
*Explanation: Continuous trigger mode provides the lowest latency by processing data continuously with millisecond-level checkpointing intervals.*

---

**Q13. What is the correct syntax to read JSON files using Auto Loader?**
- A. spark.read.format("cloudFiles").load(path)
- B. spark.readStream.format("cloudFiles").option("cloudFiles.format", "json").load(path)
- C. spark.read.format("json").load(path)
- D. spark.autoLoader.read(path)

**Answer: B. spark.readStream.format("cloudFiles").option("cloudFiles.format", "json").load(path)**  
*Explanation: Auto Loader uses readStream with format "cloudFiles" and requires specifying the underlying file format via the cloudFiles.format option.*

---

**Q14. Which option ensures that COPY INTO will not load duplicate files?**
- A. COPY INTO provides idempotency by default
- B. You must use the DEDUPLICATE keyword
- C. You must track file names manually
- D. Deduplication is not possible with COPY INTO

**Answer: A. COPY INTO provides idempotency by default**  
*Explanation: COPY INTO automatically tracks which files have been loaded and will skip files that have already been processed, providing built-in idempotency.*

---

**Q15. What is the advantage of using Parquet format over CSV?**
- A. Parquet is human-readable
- B. Parquet supports columnar storage and better compression
- C. CSV is faster for analytical queries
- D. Parquet doesn't support schema

**Answer: B. Parquet supports columnar storage and better compression**  
*Explanation: Parquet's columnar format enables better compression, efficient column pruning, and predicate pushdown, making it ideal for analytical workloads.*

---

**Q16. How can you handle schema evolution when using Auto Loader?**
- A. Set cloudFiles.schemaEvolutionMode to "rescue"
- B. Use the option .option("cloudFiles.schemaEvolutionMode", "addNewColumns")
- C. Schema evolution is not supported
- D. Manually update the schema each time

**Answer: B. Use the option .option("cloudFiles.schemaEvolutionMode", "addNewColumns")**  
*Explanation: Auto Loader supports schema evolution with modes like "addNewColumns" to automatically handle new columns or "rescue" to capture unexpected data.*

---

**Q17. Which method is best for ingesting real-time streaming data from Kafka?**
- A. COPY INTO
- B. Auto Loader
- C. Structured Streaming with Kafka source
- D. spark.read.format("kafka")

**Answer: C. Structured Streaming with Kafka source**  
*Explanation: Structured Streaming provides native integration with Kafka for continuous real-time data ingestion with exactly-once processing guarantees.*

---

**Q18. What is the purpose of the 'rescuedDataColumn' option in Auto Loader?**
- A. To backup data automatically
- B. To capture data that doesn't match the expected schema
- C. To encrypt sensitive data
- D. To compress data

**Answer: B. To capture data that doesn't match the expected schema**  
*Explanation: The rescuedDataColumn option (e.g., "_rescued_data") stores rows or columns that don't conform to the expected schema, preventing data loss during ingestion.*

---

### Section 3: Data Processing & Transformations (31%)

**Q19. Which SQL command is used to perform upsert operations in Delta Lake?**
- A. INSERT INTO
- B. UPDATE
- C. MERGE INTO
- D. REPLACE

**Answer: C. MERGE INTO**  
*Explanation: MERGE INTO combines INSERT and UPDATE operations based on matching conditions, enabling efficient upsert patterns.*

---

**Q20. What is the purpose of the OPTIMIZE command in Delta Lake?**
- A. To compress files using gzip
- B. To combine small files into larger ones for better performance
- C. To delete old data
- D. To create indexes on columns

**Answer: B. To combine small files into larger ones for better performance**  
*Explanation: OPTIMIZE performs compaction by combining small files into larger ones, improving read performance. ZORDER can be added for data skipping.*

---

**Q21. Which function converts an array column into multiple rows?**
- A. flatten()
- B. explode()
- C. expand()
- D. unnest()

**Answer: B. explode()**  
*Explanation: explode() takes an array or map column and creates a new row for each element in the array.*

---

**Q22. What is the difference between RANK() and ROW_NUMBER() window functions?**
- A. RANK() assigns unique sequential numbers, ROW_NUMBER() can have gaps
- B. ROW_NUMBER() assigns unique sequential numbers, RANK() can have gaps and ties
- C. They are identical functions
- D. RANK() only works with integers

**Answer: B. ROW_NUMBER() assigns unique sequential numbers, RANK() can have gaps and ties**  
*Explanation: ROW_NUMBER() always provides unique sequential numbers (1,2,3,4...), while RANK() can assign the same rank to tied values and skip numbers (1,2,2,4...).*

---

**Q23. When should you use a User Defined Function (UDF) instead of built-in functions?**
- A. Always, UDFs are faster
- B. Only when built-in functions cannot achieve the required logic
- C. For simple arithmetic operations
- D. UDFs should never be used

**Answer: B. Only when built-in functions cannot achieve the required logic**  
*Explanation: Built-in functions are optimized and faster than UDFs. Use UDFs only when custom logic is required that cannot be expressed with built-in functions.*

---

**Q24. What does the VACUUM command do in Delta Lake?**
- A. Removes duplicate records
- B. Removes old data files that are no longer referenced
- C. Compacts small files
- D. Updates table statistics

**Answer: B. Removes old data files that are no longer referenced**  
*Explanation: VACUUM removes old data files beyond the retention threshold (default 7 days) to free up storage. It affects time travel capabilities.*

---

**Q25. How can you query a Delta table as it existed at a specific point in time?**
- A. Using the ROLLBACK command
- B. Using VERSION AS OF or TIMESTAMP AS OF
- C. Using the RESTORE command
- D. Time travel is not possible in Delta Lake

**Answer: B. Using VERSION AS OF or TIMESTAMP AS OF**  
*Explanation: Delta Lake's time travel feature allows querying historical versions using VERSION AS OF (version number) or TIMESTAMP AS OF (timestamp).*

---

**Q26. Which window function would you use to calculate a running total?**
- A. RANK()
- B. SUM() OVER (ORDER BY column)
- C. COUNT()
- D. LAG()

**Answer: B. SUM() OVER (ORDER BY column)**  
*Explanation: SUM() with an ORDER BY clause in a window function calculates cumulative sums, creating a running total across ordered rows.*

---

**Q27. What is the purpose of the ZORDER BY clause in the OPTIMIZE command?**
- A. To sort the table alphabetically
- B. To co-locate related data in files for better data skipping
- C. To compress data
- D. To create table partitions

**Answer: B. To co-locate related data in files for better data skipping**  
*Explanation: ZORDER BY uses a space-filling curve algorithm to co-locate related information in the same set of files, enabling efficient data skipping during queries.*

---

**Q28. Which PySpark function is used to add a new column to a DataFrame?**
- A. addColumn()
- B. newColumn()
- C. withColumn()
- D. insertColumn()

**Answer: C. withColumn()**  
*Explanation: withColumn() is the PySpark method used to add a new column or replace an existing column in a DataFrame.*

---

**Q29. What does the DENSE_RANK() window function do differently from RANK()?**
- A. DENSE_RANK() doesn't skip numbers after ties
- B. DENSE_RANK() is faster
- C. DENSE_RANK() only works with dense data
- D. They are identical

**Answer: A. DENSE_RANK() doesn't skip numbers after ties**  
*Explanation: DENSE_RANK() assigns consecutive ranks (1,2,2,3) without gaps, while RANK() skips numbers after ties (1,2,2,4).*

---

**Q30. How do you access a nested field in a struct column using SQL?**
- A. struct[field]
- B. struct->field
- C. struct.field
- D. struct::field

**Answer: C. struct.field**  
*Explanation: In Spark SQL, you access nested struct fields using dot notation: struct_column.nested_field.*

---

**Q31. Which function would you use to parse a JSON string into a structured format?**
- A. from_json()
- B. parse_json()
- C. json_to_struct()
- D. read_json()

**Answer: A. from_json()**  
*Explanation: from_json() parses a JSON string column into a structured format (struct, array) based on a provided schema.*

---

**Q32. What is the purpose of the LEAD() window function?**
- A. To access values from previous rows
- B. To access values from subsequent rows
- C. To calculate moving averages
- D. To rank rows

**Answer: B. To access values from subsequent rows**  
*Explanation: LEAD() accesses values from rows that come after the current row in the window partition, useful for calculating forward-looking metrics.*

---

**Q33. Which operation triggers lazy evaluation in Spark to execute?**
- A. filter()
- B. select()
- C. write()
- D. withColumn()

**Answer: C. write()**  
*Explanation: Spark uses lazy evaluation where transformations (filter, select, withColumn) define a plan, but actions (write, show, count) trigger actual execution.*

---

**Q34. What is the correct syntax to update specific rows in a Delta table?**
- A. UPDATE table_name SET column = value
- B. UPDATE table_name SET column = value WHERE condition
- C. MODIFY table_name SET column = value WHERE condition
- D. ALTER table_name UPDATE column = value WHERE condition

**Answer: B. UPDATE table_name SET column = value WHERE condition**  
*Explanation: Delta Lake supports SQL UPDATE statements with SET clause for new values and WHERE clause to specify which rows to update.*

---

**Q35. Which aggregate function is best for removing duplicates from an array column?**
- A. array_distinct()
- B. array_unique()
- C. distinct_array()
- D. unique()

**Answer: A. array_distinct()**  
*Explanation: array_distinct() removes duplicate values from an array column, returning only unique elements.*

---

**Q36. How do you handle null values when performing aggregations in Spark SQL?**
- A. Null values are automatically excluded from aggregations like SUM, AVG
- B. You must manually filter nulls first
- C. Null values cause errors in aggregations
- D. Null values are treated as zero

**Answer: A. Null values are automatically excluded from aggregations like SUM, AVG**  
*Explanation: Most Spark SQL aggregate functions (SUM, AVG, MIN, MAX) automatically ignore null values. COUNT(*) counts nulls, but COUNT(column) does not.*

---

**Q37. What is the purpose of broadcast joins in Spark?**
- A. To send data to external systems
- B. To replicate a small table to all executors for efficient joins
- C. To partition large tables
- D. To compress data during joins

**Answer: B. To replicate a small table to all executors for efficient joins**  
*Explanation: Broadcast joins (using broadcast() hint) replicate small tables to all executors, avoiding expensive shuffles when joining with large tables.*

---

**Q38. Which command permanently deletes rows from a Delta table based on a condition?**
- A. REMOVE FROM table WHERE condition
- B. DROP FROM table WHERE condition
- C. DELETE FROM table WHERE condition
- D. TRUNCATE table WHERE condition

**Answer: C. DELETE FROM table WHERE condition**  
*Explanation: Delta Lake supports DELETE FROM statements to permanently remove rows that match the specified condition.*

---

### Section 4: Productionizing Data Pipelines (18%)

**Q39. What is the recommended cluster type for production jobs?**
- A. All-Purpose Cluster
- B. Interactive Cluster
- C. Job Cluster
- D. High-Concurrency Cluster

**Answer: C. Job Cluster**  
*Explanation: Job clusters are created for each job run and terminated afterward, providing isolation and cost optimization for production workloads.*

---

**Q40. In a Databricks Workflow, how can tasks be configured to run?**
- A. Only sequentially, one after another
- B. Only in parallel, all at once
- C. Both sequentially and in parallel based on dependencies
- D. Tasks cannot be configured

**Answer: C. Both sequentially and in parallel based on dependencies**  
*Explanation: Databricks Workflows support complex dependencies where tasks can run sequentially (with dependencies) or in parallel (without dependencies).*

---

**Q41. What happens when a job's "Max concurrent runs" is set to 1 and a new run is triggered while one is running?**
- A. The new run is queued or skipped based on configuration
- B. The running job is terminated
- C. Both jobs run simultaneously
- D. An error is raised

**Answer: A. The new run is queued or skipped based on configuration**  
*Explanation: When max concurrent runs is 1, the behavior for overlapping runs can be configured to queue the new run or skip it.*

---

**Q42. In Delta Live Tables, what does the @dlt.expect_or_drop() expectation do?**
- A. Fails the pipeline if the expectation is not met
- B. Drops records that don't meet the expectation
- C. Logs violations but keeps all records
- D. Sends an alert to administrators

**Answer: B. Drops records that don't meet the expectation**  
*Explanation: expect_or_drop() filters out records that violate the expectation, while expect() logs violations and expect_or_fail() stops the pipeline.*

---

**Q43. Which of the following is a valid cron expression for running a job every day at 2:00 AM?**
- A. 0 2 * * *
- B. 2 0 * * *
- C. * * 2 * *
- D. 0 0 2 * *

**Answer: A. 0 2 * * ***  
*Explanation: Cron format is "minute hour day month day-of-week". 0 2 * * * means 0 minutes past 2:00 AM every day.*

---

**Q44. What is a Delta Live Tables pipeline?**
- A. A single notebook with transformations
- B. A declarative framework for building batch and streaming data pipelines
- C. A tool for copying files
- D. A type of cluster configuration

**Answer: B. A declarative framework for building batch and streaming data pipelines**  
*Explanation: DLT is a declarative ETL framework that automatically manages pipeline infrastructure, dependencies, and data quality.*

---

**Q45. How can you pass parameters to a notebook in a Databricks job?**
- A. Through environment variables only
- B. Using widgets and job parameters
- C. Parameters cannot be passed
- D. Only through command-line arguments

**Answer: B. Using widgets and job parameters**  
*Explanation: Databricks jobs can pass parameters to notebooks using widgets (dbutils.widgets) and job-level parameters configured in the workflow.*

---

**Q46. What is the purpose of retry policies in Databricks workflows?**
- A. To retry failed tasks automatically based on configuration
- B. To restart the entire job
- C. To rollback changes
- D. Retry is not supported

**Answer: A. To retry failed tasks automatically based on configuration**  
*Explanation: Retry policies allow automatic retry of failed tasks with configurable retry count and intervals, improving pipeline reliability.*

---

**Q47. In DLT, what is the difference between @dlt.table and @dlt.view?**
- A. Tables are temporary, views are persistent
- B. Tables persist data to storage, views are computed on-demand
- C. They are identical
- D. Views cannot be queried

**Answer: B. Tables persist data to storage, views are computed on-demand**  
*Explanation: @dlt.table creates materialized tables that persist data, while @dlt.view creates views that are recomputed when queried.*

---

**Q48. Which notification method is supported for job completion in Databricks?**
- A. Email only
- B. SMS only
- C. Email and webhook notifications
- D. No notifications are supported

**Answer: C. Email and webhook notifications**  
*Explanation: Databricks jobs support email notifications and webhooks for job start, success, and failure events, enabling integration with external systems.*

---

**Q49. What is the medallion architecture in data engineering?**
- A. A security framework
- B. A multi-layered data architecture (Bronze, Silver, Gold)
- C. A cluster configuration
- D. A type of database

**Answer: B. A multi-layered data architecture (Bronze, Silver, Gold)**  
*Explanation: Medallion architecture organizes data into layers: Bronze (raw), Silver (cleaned), and Gold (aggregated/business-level), progressively improving data quality.*

---

**Q50. How can you monitor the execution of a DLT pipeline?**
- A. Through pipeline event logs and lineage graphs
- B. Monitoring is not available
- C. Only through cluster logs
- D. Only through email notifications

**Answer: A. Through pipeline event logs and lineage graphs**  
*Explanation: DLT provides detailed event logs, data quality metrics, and interactive lineage graphs to monitor pipeline execution and troubleshoot issues.*

---

**Q51. What happens to a job cluster after the job completes?**
- A. It remains running for future jobs
- B. It is automatically terminated
- C. It enters hibernation mode
- D. Manual termination is required

**Answer: B. It is automatically terminated**  
*Explanation: Job clusters are ephemeral and are automatically terminated after the job completes, optimizing costs by not keeping idle resources.*

---

**Q52. Which DLT decorator is used to create a streaming table?**
- A. @dlt.stream_table
- B. @dlt.table with dlt.read_stream()
- C. @dlt.streaming()
- D. @dlt.continuous_table

**Answer: B. @dlt.table with dlt.read_stream()**  
*Explanation: DLT streaming tables are created using @dlt.table decorator with dlt.read_stream() to read from streaming sources.*

---

### Section 5: Data Governance & Quality (11%)

**Q53. What is the three-level namespace format in Unity Catalog?**
- A. database.schema.table
- B. catalog.schema.table
- C. workspace.database.table
- D. cluster.database.table

**Answer: B. catalog.schema.table**  
*Explanation: Unity Catalog uses a three-level namespace: catalog.schema.table (or catalog.database.table), providing better organization and isolation.*

---

**Q54. Which command grants SELECT permission on a table to a user?**
- A. GRANT SELECT ON TABLE table_name TO user@example.com
- B. ALLOW SELECT FOR user@example.com ON table_name
- C. GRANT SELECT TO user@example.com FROM table_name
- D. PERMISSION SELECT table_name TO user@example.com

**Answer: A. GRANT SELECT ON TABLE table_name TO user@example.com**  
*Explanation: The GRANT command follows the syntax: GRANT privilege ON object_type object_name TO principal.*

---

**Q55. What does the @dlt.expect_or_fail() expectation do in Delta Live Tables?**
- A. Logs violations but continues processing
- B. Drops invalid records
- C. Stops the pipeline execution if the expectation is violated
- D. Sends an email notification

**Answer: C. Stops the pipeline execution if the expectation is violated**  
*Explanation: expect_or_fail() halts the pipeline if any record violates the expectation, ensuring strict data quality requirements.*

---

**Q56. Which feature in Unity Catalog automatically tracks data dependencies and transformations?**
- A. Data Profiling
- B. Data Lineage
- C. Data Catalog
- D. Data Sharing

**Answer: B. Data Lineage**  
*Explanation: Unity Catalog automatically captures data lineage, showing how data flows through tables, views, and notebooks, useful for impact analysis and compliance.*

---

**Q57. What is Delta Sharing used for?**
- A. Sharing clusters between workspaces
- B. Securely sharing live data with external organizations without copying it
- C. Sharing notebooks with team members
- D. Sharing compute resources

**Answer: B. Securely sharing live data with external organizations without copying it**  
*Explanation: Delta Sharing is an open protocol for secure data sharing across organizations, allowing live access to data without duplication.*

---

**Q58. Which permission is required to create a new table in a schema?**
- A. SELECT on schema
- B. USAGE on schema
- C. CREATE TABLE on schema
- D. MODIFY on schema

**Answer: C. CREATE TABLE on schema**  
*Explanation: CREATE TABLE permission on the schema (along with USAGE on catalog and schema) is required to create new tables.*

---

**Q59. What is the purpose of table constraints in Delta Lake?**
- A. To improve query performance
- B. To enforce data quality rules at write time
- C. To compress data
- D. To partition tables

**Answer: B. To enforce data quality rules at write time**  
*Explanation: Table constraints (NOT NULL, CHECK) validate data during write operations, preventing invalid data from being written to the table.*

---

**Q60. What is the USAGE privilege in Unity Catalog?**
- A. Grants full read and write access
- B. Allows traversing the namespace hierarchy to access objects
- C. Only grants administrative access
- D. Grants delete permissions

**Answer: B. Allows traversing the namespace hierarchy to access objects**  
*Explanation: USAGE is a prerequisite permission that allows navigation through catalogs and schemas. Additional permissions like SELECT are needed for actual data access.*

---

**Q61. Which DLT expectation logs violations but keeps all records?**
- A. @dlt.expect()
- B. @dlt.expect_or_drop()
- C. @dlt.expect_or_fail()
- D. @dlt.log_violations()

**Answer: A. @dlt.expect()**  
*Explanation: @dlt.expect() logs data quality violations to event logs but allows all records to pass through the pipeline for monitoring purposes.*

---

**Q62. What information does Unity Catalog store in its metastore?**
- A. Only table schemas
- B. Metadata about data assets, permissions, and lineage
- C. Only user credentials
- D. Only cluster configurations

**Answer: B. Metadata about data assets, permissions, and lineage**  
*Explanation: Unity Catalog's metastore stores comprehensive metadata including table schemas, permissions, ownership, lineage, and data classification.*

---

**Q63. How can you revoke permissions in Unity Catalog?**
- A. DELETE PERMISSION FROM user
- B. REVOKE privilege ON object FROM principal
- C. REMOVE ACCESS FOR user
- D. DROP PERMISSION ON object

**Answer: B. REVOKE privilege ON object FROM principal**  
*Explanation: REVOKE command removes previously granted permissions using the syntax: REVOKE privilege ON object_type object_name FROM principal.*

---

**Q64. What is a managed table in Unity Catalog?**
- A. A table where Unity Catalog manages only the metadata
- B. A table where Unity Catalog manages both metadata and data files
- C. A temporary table
- D. An external table

**Answer: B. A table where Unity Catalog manages both metadata and data files**  
*Explanation: Managed tables have both metadata and data lifecycle managed by Unity Catalog. Dropping the table deletes both metadata and underlying data files.*

---

**Q65. What is the purpose of row-level security in Unity Catalog?**
- A. To encrypt rows
- B. To control which rows users can access based on functions
- C. To compress rows
- D. To partition rows

**Answer: B. To control which rows users can access based on functions**  
*Explanation: Row-level security (row filters) allows fine-grained access control by applying SQL predicates that filter which rows different users can see in a table.*

---

### Section 6: Advanced Topics & Mixed Concepts

**Q66. Which of the following is NOT a valid file format for ingestion in Databricks?**
- A. CSV
- B. JSON
- C. Parquet
- D. EXE

**Answer: D. EXE**  
*Explanation: Databricks supports data file formats like CSV, JSON, Parquet, Avro, ORC, Delta, but not executable files.*

---

**Q67. What is the default retention period for data files in Delta Lake before VACUUM removes them?**
- A. 24 hours
- B. 7 days (168 hours)
- C. 30 days
- D. 90 days

**Answer: B. 7 days (168 hours)**  
*Explanation: The default retention period is 7 days (168 hours) to preserve time travel capabilities. This can be configured with RETAIN X HOURS.*

---

**Q68. Which Spark operation triggers execution of transformations?**
- A. filter()
- B. select()
- C. withColumn()
- D. write()

**Answer: D. write()**  
*Explanation: Spark uses lazy evaluation. Transformations like filter(), select() only define the execution plan. Actions like write(), count(), show() trigger execution.*

---

**Q69. In PySpark, which method is used to filter rows based on a condition?**
- A. select()
- B. filter() or where()
- C. withColumn()
- D. groupBy()

**Answer: B. filter() or where()**  
*Explanation: Both filter() and where() are used to filter rows in a DataFrame based on conditions. They are aliases of each other.*

---

**Q70. What is the purpose of partitioning a Delta table?**
- A. To encrypt data
- B. To physically organize data by column values for better query performance
- C. To compress data
- D. To replicate data

**Answer: B. To physically organize data by column values for better query performance**  
*Explanation: Partitioning organizes data into subdirectories based on column values, enabling partition pruning for faster queries on those columns.*

---

**Q71. Which command shows the history of operations performed on a Delta table?**
- A. SHOW HISTORY
- B. DESCRIBE HISTORY table_name
- C. SELECT * FROM table_name.history
- D. GET HISTORY table_name

**Answer: B. DESCRIBE HISTORY table_name**  
*Explanation: DESCRIBE HISTORY shows the transaction log of a Delta table, including operations, timestamps, and versions.*

---

**Q72. What is the benefit of using ZORDER in the OPTIMIZE command?**
- A. It encrypts the data
- B. It co-locates related information in the same files for data skipping
- C. It compresses data
- D. It partitions the table

**Answer: B. It co-locates related information in the same files for data skipping**  
*Explanation: ZORDER BY arranges data within files to enable efficient data skipping, improving query performance on specified columns.*

---

**Q73. Which SQL function is used to parse a JSON string into a struct?**
- A. from_json()
- B. parse_json()
- C. json_parse()
- D. to_struct()

**Answer: A. from_json()**  
*Explanation: from_json() parses a JSON string column into a structured format (struct) using a provided schema.*

---

**Q74. What is the difference between streaming and batch processing?**
- A. Streaming processes data in real-time as it arrives; batch processes accumulated data periodically
- B. Streaming is slower than batch
- C. Batch can only process small datasets
- D. There is no difference

**Answer: A. Streaming processes data in real-time as it arrives; batch processes accumulated data periodically**  
*Explanation: Streaming provides low-latency processing of continuous data, while batch processes data at scheduled intervals.*

---

**Q75. Which operation allows you to restore a Delta table to a previous version?**
- A. ROLLBACK
- B. RESTORE TABLE table_name TO VERSION AS OF X
- C. REVERT TO VERSION X
- D. UNDO CHANGES

**Answer: B. RESTORE TABLE table_name TO VERSION AS OF X**  
*Explanation: RESTORE allows reverting a Delta table to a previous state specified by version number or timestamp.*

---

**Q76. Which Databricks feature provides automatic cluster scaling based on workload?**
- A. Manual scaling only
- B. Autoscaling
- C. Static configuration
- D. Vertical scaling

**Answer: B. Autoscaling**  
*Explanation: Autoscaling automatically adds or removes nodes based on workload demands, optimizing performance and cost.*

---

**Q77. What is the purpose of liquid clustering in Delta Lake?**
- A. To encrypt data
- B. To automatically optimize data layout without manual ZORDER commands
- C. To compress data
- D. To create backups

**Answer: B. To automatically optimize data layout without manual ZORDER commands**  
*Explanation: Liquid clustering is a newer Delta Lake feature that automatically maintains optimal data layout, simplifying performance tuning compared to manual partitioning and ZORDER.*

---

**Q78. Which method should you use to read a Delta table in PySpark?**
- A. spark.read.delta("path")
- B. spark.read.format("delta").load("path")
- C. spark.read.table("table_name")
- D. All of the above

**Answer: D. All of the above**  
*Explanation: Delta tables can be read using spark.read.delta(), spark.read.format("delta").load(), or spark.read.table() for registered tables.*

---

**Q79. What is the LAG() window function used for?**
- A. To access values from subsequent rows
- B. To access values from previous rows
- C. To calculate averages
- D. To rank rows

**Answer: B. To access values from previous rows**  
*Explanation: LAG() accesses values from rows that come before the current row in the window partition, useful for calculating changes or differences.*

---

**Q80. Which statement is true about Delta Lake transactions?**
- A. Only one write operation can occur at a time on a table
- B. Multiple concurrent writes are supported with optimistic concurrency control
- C. Transactions are not supported
- D. Only reads support concurrency

**Answer: B. Multiple concurrent writes are supported with optimistic concurrency control**  
*Explanation: Delta Lake uses optimistic concurrency control, allowing multiple concurrent writers. Conflicts are detected at commit time and can be retried.*

---

## Exam Preparation Checklist

### 4 Weeks Before Exam
- ☐ Review exam guide thoroughly
- ☐ Complete recommended training courses
- ☐ Set up Databricks Community Edition for hands-on practice
- ☐ Create study schedule covering all 5 domains

### 2 Weeks Before Exam
- ☐ Practice with sample MCQs
- ☐ Review all study notes
- ☐ Focus on weak areas (especially high-weight topics)
- ☐ Practice writing SQL and PySpark code
- ☐ Review Unity Catalog permissions and structure

### 1 Week Before Exam
- ☐ Take practice exams (simulate 90-minute time limit)
- ☐ Review all incorrect answers
- ☐ Memorize key syntax (GRANT, MERGE, DLT decorators)
- ☐ Verify technical requirements for online proctoring
- ☐ Run system check on exam platform

### Day Before Exam
- ☐ Review this study guide (quick refresh)
- ☐ Review exam weight distribution
- ☐ Prepare exam environment (quiet space, stable internet)
- ☐ Get adequate rest

### Day of Exam
- ☐ Arrive/log in 15 minutes early
- ☐ Have valid ID ready
- ☐ Clear workspace (no notes, books, or devices)
- ☐ Stay calm and read each question carefully
- ☐ Manage time: ~2 minutes per question

---

## Additional Tips for Success

### Time Management
- **45 questions in 90 minutes = 2 minutes per question**
- Don't spend more than 3 minutes on any single question
- Mark difficult questions for review
- Answer all questions (no penalty for wrong answers)

### Test-Taking Strategies
- ✓ Read questions carefully - watch for "NOT", "EXCEPT", "BEST"
- ✓ Eliminate obviously wrong answers first
- ✓ Look for keywords that match documentation terminology
- ✓ Consider Databricks best practices, not just what "works"
- ✓ When unsure, choose the most specific/detailed answer

### Common Traps to Avoid
- ⚠️ Confusing Auto Loader vs. COPY INTO
- ⚠️ Mixing up UDF syntax in SQL vs. PySpark
- ⚠️ Forgetting Unity Catalog three-level namespace
- ⚠️ Confusing window function behavior (RANK vs. ROW_NUMBER)
- ⚠️ Not knowing DLT expectation types
- ⚠️ Mixing up cluster types (all-purpose vs. job)

### Key Documentation to Review
- Delta Lake documentation
- Unity Catalog access control
- Databricks Workflows
- Delta Live Tables
- Auto Loader
- Spark SQL functions reference

---

## Quick Reference Tables

### File Formats Comparison
| Format | Schema | ACID | Compression | Use Case |
|--------|--------|------|-------------|----------|
| CSV | No | No | Limited | Simple data exchange |
| JSON | Partial | No | Good | Semi-structured data |
| Parquet | Yes | No | Excellent | Analytical workloads |
| Delta | Yes | Yes | Excellent | All Databricks workloads |

### Cluster Types
| Type | Use Case | Lifecycle | Cost |
|------|----------|-----------|------|
| All-Purpose | Interactive development | Manual | Higher (runs continuously) |
| Job | Production automated jobs | Auto (per job) | Lower (ephemeral) |

### DLT Expectations
| Type | Behavior | Use Case |
|------|----------|----------|
| @dlt.expect() | Log violations, keep records | Monitoring |
| @dlt.expect_or_drop() | Drop invalid records | Filtering |
| @dlt.expect_or_fail() | Fail pipeline | Critical quality |

### Window Function Comparison
| Function | Unique Values | Gaps | Ties |
|----------|---------------|------|------|
| ROW_NUMBER() | Yes | No | No |
| RANK() | No | Yes | Yes |
| DENSE_RANK() | No | No | Yes |

---

## Recommended Study Resources

### Official Databricks Training
- ✓ Data Engineering With Databricks (Instructor-led)
- ✓ Data Ingestion with Lakeflow Connect (Self-paced)
- ✓ Deploy Workloads with Lakeflow Jobs (Self-paced)
- ✓ Build Data Pipelines with Lakeflow Spark (Self-paced)
- ✓ Data Management and Governance with Unity Catalog (Self-paced)

### Documentation
- Databricks Documentation (docs.databricks.com)
- Delta Lake Documentation
- Apache Spark SQL Reference
- Unity Catalog Documentation

### Hands-On Practice
- Databricks Community Edition (Free)
- Practice building end-to-end pipelines
- Experiment with Unity Catalog permissions
- Create and schedule Databricks Workflows

---

## Final Thoughts

**Key to Success:** Hands-on experience is critical. Reading and memorizing alone won't be sufficient. Make sure to:
1. **Practice writing code** in both SQL and PySpark
2. **Build actual pipelines** with ingestion, transformation, and scheduling
3. **Experiment with Unity Catalog** permissions and governance features
4. **Create and run jobs** in Databricks Workflows
5. **Work with Delta Lake** operations (MERGE, OPTIMIZE, time travel)

**Remember:** This certification validates practical skills, not just theoretical knowledge. Focus on understanding WHY certain approaches are recommended, not just memorizing syntax.

Good luck with your exam! 🚀

---

*Last Updated: November 2025*  
*Certification Valid For: 2 years*  
*Exam Version: Associate Level*
