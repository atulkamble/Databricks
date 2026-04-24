# 🚀 Databricks (Hands-on + Notes)

## 📌 1. What is Databricks?

* Unified analytics platform for **Data Engineering + Data Science + ML**
* Built on top of **Apache Spark**
* Works with **Azure (ADB), AWS, GCP**
* Supports:

  * Python (PySpark)
  * SQL
  * Scala
  * R

---

## 📌 2. Core Components

### 🔹 Workspace

* Notebooks (like Jupyter)
* Collaboration

### 🔹 Cluster

* Compute engine (Spark cluster)
* Types:

  * Standard
  * High Concurrency
  * Single Node

### 🔹 DBFS (Databricks File System)

* Distributed storage
* Mounted with cloud storage (S3, ADLS, GCS)

### 🔹 Delta Lake

* Storage layer with:

  * ACID transactions
  * Time travel
  * Schema enforcement

---

## 📌 3. Important Concepts (Interview + Real Use)

* Lazy Evaluation (Spark)
* Transformations vs Actions
* Partitioning & Performance
* Caching
* Delta Tables vs Parquet
* Jobs & Scheduling
* Auto-scaling clusters

---

# 🧪 HANDS-ON LAB (Step-by-Step)

## ✅ Step 1: Create Workspace

* Go to:

  * Azure → Create **Azure Databricks**
  * AWS → Databricks workspace

---

## ✅ Step 2: Create Cluster

```
Cluster Name: demo-cluster
Runtime: 13.x (Latest LTS)
Node Type: Standard_DS3_v2 (Azure)
Auto Termination: 60 mins
```

---

## ✅ Step 3: Create Notebook

* Language: Python

---

# 🧪 DEMO 1: Basic Spark (PySpark)

```python
# Create DataFrame
data = [("Atul", 28), ("Raj", 30), ("Amit", 25)]
columns = ["Name", "Age"]

df = spark.createDataFrame(data, columns)

df.show()
```

👉 Output:

```
+-----+---+
|Name |Age|
+-----+---+
|Atul |28 |
|Raj  |30 |
|Amit |25 |
+-----+---+
```

---

# 🧪 DEMO 2: Transformations

```python
# Filter
df.filter(df.Age > 26).show()

# Select
df.select("Name").show()

# Add Column
from pyspark.sql.functions import col

df2 = df.withColumn("AgePlus5", col("Age") + 5)
df2.show()
```

---

# 🧪 DEMO 3: Actions

```python
df.count()
df.collect()
df.first()
```

---

# 🧪 DEMO 4: Read File (CSV)

```python
df = spark.read.csv("/FileStore/tables/data.csv", header=True, inferSchema=True)
df.show()
```

---

# 🧪 DEMO 5: Write Data

```python
df.write.mode("overwrite").csv("/FileStore/output/")
```

---

# 🧪 DEMO 6: Delta Lake

```python
# Write as Delta
df.write.format("delta").save("/FileStore/delta_table")

# Read Delta
df_delta = spark.read.format("delta").load("/FileStore/delta_table")
df_delta.show()
```

---

# 🧪 DEMO 7: SQL in Databricks

```python
# Create temp view
df.createOrReplaceTempView("people")

# Run SQL
spark.sql("SELECT * FROM people WHERE Age > 26").show()
```

---

# 🧪 DEMO 8: Time Travel (Delta Lake)

```python
spark.read.format("delta") \
.option("versionAsOf", 0) \
.load("/FileStore/delta_table")
```

---

# 🧪 DEMO 9: Mount Storage (Azure ADLS)

```python
configs = {
  "fs.azure.account.key.<storage-account>.dfs.core.windows.net": "<access-key>"
}

dbutils.fs.mount(
  source = "abfss://container@<storage-account>.dfs.core.windows.net/",
  mount_point = "/mnt/data",
  extra_configs = configs)
```

---

# 🧪 DEMO 10: Caching (Performance)

```python
df.cache()
df.count()
```

---

# ⚡ Practice Tasks

### 🔹 Task 1

* Create DataFrame with:

  * Name, Salary
* Filter Salary > 50K

---

### 🔹 Task 2

* Read CSV → Convert to Delta → Query using SQL

---

### 🔹 Task 3

* Perform:

  * GroupBy
  * Aggregation

```python
df.groupBy("Name").count().show()
```

---

### 🔹 Task 4

* Create Job in Databricks
* Schedule notebook execution

---

# 📌 Important Points to Remember (VERY IMPORTANT 🔥)

### 💡 Spark Concepts

* Transformations → Lazy
* Actions → Execute job
* Wide vs Narrow transformations

---

### 💡 Performance

* Use `.cache()` for repeated queries
* Avoid `.collect()` on large data
* Use partitioning

---

### 💡 Delta Lake

* Supports:

  * ACID
  * Versioning
  * Schema evolution

---

### 💡 Best Practices

* Use **Delta instead of CSV/Parquet**
* Use **Auto Scaling clusters**
* Terminate idle clusters (cost saving)
* Use **Notebook version control (Git)**

---

# 📌 Interview Quick Q&A

### Q1: Difference between RDD & DataFrame?

* RDD → Low-level
* DataFrame → Optimized (Catalyst optimizer)

---

### Q2: What is Delta Lake?

* Storage layer with ACID on Data Lake

---

### Q3: What is Lazy Evaluation?

* Execution only when action is triggered

---

### Q4: Why caching?

* Improves performance for repeated queries

---

# 📌 Mini Project Idea (Resume Ready)

### 🔥 Project: Sales Data Pipeline

* Ingest CSV → Clean → Transform → Store in Delta
* Query using SQL
* Schedule job

---

# 🎯 Final Tip (Trainer Insight)

For **₹20–40 LPA roles / Architect level**:

* Focus on:

  * Spark optimization
  * Delta Lake design
  * Pipeline architecture
  * Integration with Azure Data Factory / AWS Glue

---
