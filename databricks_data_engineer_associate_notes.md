# Databricks Certified Data Engineer Associate – Study Pack

## Exam Snapshot
- **Purpose:** Validates ability to perform introductory data engineering tasks in the Databricks Data Intelligence Platform.
- **Question count:** 45 scored questions (may include some unscored items).
- **Format:** Multiple choice, 90 minutes, proctored (online or test center), $200 fee.
- **Languages offered:** English, Japanese (日本語), Brazilian Portuguese (Português BR), Korean (한국어).
- **Prerequisites:** None required; 6+ months of Databricks data engineering experience recommended.
- **Recertification:** Every 2 years by retaking the current exam version.
- **Code format in exam:** SQL preferred; otherwise Python (PySpark).

## Content Allocation
| Domain                              | Weight |
|-------------------------------------|-------:|
| Databricks Intelligence Platform    | 10%    |
| Development and Ingestion           | 30%    |
| Data Processing & Transformations   | 31%    |
| Productionizing Data Pipelines      | 18%    |
| Data Governance & Quality           | 11%    |

## Important Points to Remember
1. Understand workspace layout, compute options, Unity Catalog integration, and Lakehouse architecture basics.
2. Expect ETL coverage: ingestion patterns, schema evolution, complex data types, UDFs, and Lakehouse Spark declarative pipelines.
3. Be ready to use Databricks Workflows/Lakeflow Jobs for deployment, scheduling, alerts, and monitoring.
4. Governance topics include Unity Catalog, lineage, permissions, and data quality enforcement.
5. No aids allowed during the exam; run the PSI system check beforehand if testing online.
6. Brush up on SQL window functions, incremental processing patterns (Auto Loader, COPY INTO), and PySpark DataFrame APIs.
7. Related training paths: instructor-led “Data Engineering With Databricks” and self-paced Lakeflow/Unity Catalog courses.

## Quick Reference Checklist
- Review the official exam guide twice: once to scope content, once to validate readiness.
- Complete at least one end-to-end Lakehouse pipeline project (ingest → transform → workflow → govern).
- Practice translating SQL logic into PySpark when needed.
- Familiarize yourself with Databricks’ compute, storage, and security controls to answer architectural questions quickly.
- Schedule exam only after mock tests consistently score above passing threshold.

## Short Answer Practice
1. **What does the certification validate?** Ability to use the Databricks Data Intelligence Platform for foundational data engineering, including workspace usage, ETL with SQL/PySpark, and workflow orchestration.
2. **How often do you need to recertify?** Every two years by passing the latest version of the exam.
3. **What are typical development tasks assessed?** Data ingestion, transformation, handling complex data, UDF usage, and managing jobs in Databricks Workflows.
4. **Which languages appear in the exam?** Primarily SQL; Python (PySpark) when SQL is not applicable.
5. **How should you prepare logistically?** Register via the exam delivery platform, ensure technical requirements are met, and run a proctored exam system check.

## Scenario-Based Questions (with Answers)
1. **Scenario:** You need to ensure lineage and fine-grained permissions for a new Lakehouse zone. **Answer:** Implement Unity Catalog for object-level security, leverage catalogs/schemas for isolation, and capture lineage through built-in tracking.
2. **Scenario:** A streaming ingestion job must auto-handle schema changes from cloud storage. **Answer:** Use Auto Loader with schema evolution enabled and optionally schema hints; monitor via Workflows.
3. **Scenario:** Stakeholders request nightly batch jobs with retry policies. **Answer:** Configure Databricks Workflows (Lakeflow Jobs) with task orchestration, schedule triggers, and alerting; use job clusters or existing pools.
4. **Scenario:** You must validate data quality before loading curated tables. **Answer:** Apply Delta Live Tables expectations or custom quality checks with Delta constraints, logging failures for remediation.
5. **Scenario:** Python code must reuse complex logic in SQL jobs. **Answer:** Define PySpark UDFs (or Pandas UDFs) and register them for SQL access, ensuring performance considerations are addressed.

## Multiple-Choice Practice
1. **Which feature is most directly responsible for centralized governance in Databricks?**  
   A. Auto Loader  
   B. Unity Catalog  
   C. Delta Caching  
   D. Notebook Repos  
   **Answer:** B

2. **A Lakehouse pipeline must ingest from cloud object storage with minimal manual schema updates. Which service fits best?**  
   A. COPY INTO with static schema  
   B. Auto Loader with schema evolution  
   C. Legacy Spark Streaming with manual parsing  
   D. JDBC ingestion via SQL Warehouses  
   **Answer:** B

3. **What is the main benefit of Databricks Workflows (Lakeflow Jobs) for this exam?**  
   A. Directly editing Unity Catalog permissions  
   B. Scheduling and orchestrating tasks with retries and alerts  
   C. Managing ML model registry entries  
   D. Delivering BI dashboards to stakeholders  
   **Answer:** B

4. **Why does the exam emphasize SQL first for data manipulation?**  
   A. SQL is faster than PySpark in Databricks  
   B. Most candidates only know SQL  
   C. SQL expressions are portable and concise; PySpark is used when SQL doesn’t cover a requirement  
   D. Python is deprecated in Databricks  
   **Answer:** C

5. **During preparation, which combination best covers governance topics?**  
   A. Learning Auto Loader + Workflows  
   B. Studying Unity Catalog + Delta constraints/expectations  
   C. Practicing notebooks + repos  
   D. Memorizing Spark configuration keys only  
   **Answer:** B

## Study Flow Recommendations
1. **Review the exam guide** to enumerate objectives and map them to personal skill gaps.
2. **Take recommended training** (Data Engineering with Databricks or relevant Academy modules).
3. **Build hands-on projects** covering ingestion, transformation, and workflow deployment.
4. **Drill governance and quality topics** with Unity Catalog labs and Delta Live Tables expectations.
5. **Run mock exams and timed quizzes** to practice 90-minute pacing.
6. **Register and run system checks** well before exam day; ensure no technical blockers remain.
7. **Finalize notes** on platform components, UDF patterns, workflow orchestration, and governance nuances.

Keeping this concise study pack nearby should make last-minute review and self-testing easier before taking the Databricks Certified Data Engineer Associate exam.
