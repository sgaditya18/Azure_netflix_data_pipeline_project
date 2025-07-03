# Azure_netflix_data_pipeline_project


This project demonstrates an end-to-end data engineering pipeline using:
- Databricks
- Azure Data Lake Storage (ADLS)
- Azure Synapse Analytics


The goal: **integrate and analyze Netflix and IMDB datasets to uncover insights about streaming content.**

---

## 📂 Project Overview

### Data Sources

✅ **Netflix Titles Dataset**
- Original CSV from [Kaggle Netflix Dataset](https://www.kaggle.com/shivamb/netflix-shows)
- Contains show details: title, director, cast, country, etc.

✅ **IMDB Titles Dataset**
- TSV files from [IMDB Datasets](https://datasets.imdbws.com/)
- Contains movie and series metadata.

---

## 🔗 Architecture

![image](https://github.com/user-attachments/assets/2f45af4a-c044-493e-ae9b-04d6433ee916)


**Data Flow:**


---

## 🚀 Steps Performed

### 1. Load Raw Data into Databricks

- Uploaded Netflix CSV into Databricks workspace.
- Connected to ADLS using a **SAS token** for reading IMDB data:
    ```python
    spark.conf.set(
        "fs.azure.sas.<container>.<storage_account>.dfs.core.windows.net",
        "<sas-token>"
    )
    ```

- Loaded IMDB TSV files using Spark’s read options.

---

### 2. Data Transformation in Databricks

- Cleaned and selected relevant columns.
- Joined Netflix data with IMDB data:
    - Matched titles
    - Merged genres, ratings, release years
- Created a unified DataFrame:
    ```python
    df_joined.write.format("delta").mode("overwrite").save(
        "abfss://transformed@<storage_account>.dfs.core.windows.net/curated/netflix_imdb_joined"
    )
    ```

---

### 3. Save Transformed Data in ADLS

- Stored final data in ADLS Gen2 as **Delta format** for scalability.
- Verified data read using:
    ```python
    df_read = spark.read.format("delta").load(
        "abfss://transformed@<storage_account>.dfs.core.windows.net/curated/netflix_imdb_joined"
    )
    ```

---

### 4. Create External Table in Synapse

- Created a database and external Delta table:
    ```sql
    CREATE DATABASE IF NOT EXISTS netflix_db;

    CREATE EXTERNAL TABLE netflix_db.netflix_imdb_joined
    USING DELTA
    LOCATION 'abfss://transformed@<storage_account>.dfs.core.windows.net/curated/netflix_imdb_joined';
    ```

- Tested queries in Synapse Serverless SQL pool.

---

### 5. Power BI Visualization

- Connected Power BI to Synapse Serverless SQL endpoint.
- Imported data into Power BI model.
- Created interactive dashboards visualizing:
    - Show counts by release year
    - Popular genres
    - Country trends
    - Content type (Movie vs TV Show)

---

## 🛠️ Technologies Used

- **Azure Databricks (Spark)**
- **Azure Data Lake Storage Gen2 (ADLS)**
- **Azure Synapse Analytics (Serverless SQL)**
- **Python / PySpark**

---

## 🎯 Key Learnings

✅ End-to-end data pipeline across Azure services.  
✅ Handling large data files (CSV, TSV) in Spark.  
✅ Using Delta format for scalable analytics.  
✅ Exposing data to BI tools through Synapse Serverless.  
✅ Building interactive visualizations in Power BI.

---

## 💡 Future Enhancements

- Automate ingestion via Azure Data Factory.
- Include time travel queries on Delta tables.
- Publish a Power BI service dashboard publicly.
- Add more IMDB datasets for ratings and reviews analysis.

---


## 🔗 Dataset References

- [Netflix Dataset on Kaggle](https://www.kaggle.com/shivamb/netflix-shows)
- [IMDB Datasets](https://datasets.imdbws.com/)

---

**Project by [Your Name]** 🚀

