# 🏅 Tokyo 2020 Olympics: End-to-End Azure Data Engineering & Analytics

## 📌 Project Overview
This project is an end-to-end data engineering and analytics solution designed to process, transform, and visualize the Tokyo 2020 Olympics dataset. Built as a comprehensive research project, it demonstrates a complete ETL (Extract, Transform, Load) pipeline using the Microsoft Azure cloud ecosystem, culminating in a highly interactive and insightful Power BI dashboard.

## 🛠️ Tech Stack & Tools
* **Cloud Platform:** Microsoft Azure
* **Data Ingestion:** Azure Data Factory (ADF)
* **Data Storage:** Azure Data Lake Storage Gen2 (ADLS Gen2)
* **Data Processing & Transformation:** Azure Databricks, PySpark, Python
* **Data Serving / Warehousing:** Azure Synapse Analytics, SQL
* **Data Visualization:** Power BI

---

## 🏗️ Architecture & ETL Workflow

### 1. Azure Resource Provisioning
All required services were provisioned under a single logical Resource Group (`tokyo-olympic`), ensuring centralized management of Azure Data Factory, Databricks, Synapse Analytics, and Storage accounts.

![Azure Resource Group](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/1.png)

### 2. Data Storage Setup (ADLS Gen2)
An Azure Data Lake Storage (ADLS Gen2) account was created with a hierarchical namespace enabled. Two primary directories were structured inside the container to separate data stages:
* `raw-data/`: Destination for ingested, unprocessed data.
* `transformed-data/`: Destination for cleaned and processed data ready for analytical querying.

![ADLS Gen2 Containers](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/4.png)

### 3. Data Ingestion (Extract)
Automated data pipelines were engineered using **Azure Data Factory**. HTTP linked services were utilized to dynamically extract the 5 core CSV files (Athletes, Coaches, EntriesGender, Medals, Teams) directly from the source and sink them into the `raw-data` folder in ADLS Gen2.

![Azure Data Factory Pipeline](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/6.png)

### 4. Security & Access Control (IAM)
To ensure secure access between Databricks and ADLS Gen2, an **Azure Service Principal (App Registration)** was created. It was granted the `Storage Blob Data Contributor` role, allowing Databricks to securely mount and process the data via OAuth credentials and secret keys.

![App Registration Details](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/11.png)
![IAM Role Assignment](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/15.png)

### 5. Data Transformation (Transform)
Using **Azure Databricks**, an Apache Spark cluster was spun up to process the raw data. 
* **Mounting Storage:** PySpark was used to mount the ADLS Gen2 raw and transformed containers using the Service Principal credentials.
* **Cleaning & Formatting:** Data types were strictly enforced (e.g., casting medal counts and gender entries from strings to integers using `inferSchema`), and dataframes were validated.

![Databricks Mounting Storage](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/16.png)

Once transformed, the clean Spark DataFrames were written back to the `transformed-data` directory in ADLS Gen2 as structured CSVs.

![Databricks Writing Data](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/18.png)

### 6. Data Serving (Load)
**Azure Synapse Analytics** was connected to the transformed data lake. Serverless SQL pools were utilized to create logical tables directly on top of the transformed files, making the data instantly queryable for downstream BI tools.

![Azure Synapse Analytics Tables](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/20.png)

---

## 📊 Data Modeling & Power BI Dashboards

With the data successfully warehoused, it was connected to **Power BI**. The tables were modeled using Many-to-Many relationships with bidirectional cross-filtering to handle the complex, dimensional nature of the Olympic dataset.

![Power BI Data Model](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/21.png)

The final visualization suite is divided into four highly interactive chapters, utilizing custom DAX measures to deliver deep insights:

### 🏠 Executive Summary
A high-level landing page featuring dynamic KPI cards and an "Efficiency" scatter plot revealing which nations optimally converted athlete counts into medals. Includes global slicers for Country and Discipline.
![Executive Summary](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/tokyo_olympic_home-page_powerbi.png)

### 🌍 The Olympic Summary
A detailed geographical map and a classic Olympic medal leaderboard matrix, providing a fast breakdown of the top 10 countries by total historical performance.
![Olympic Summary](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/tokyo_olympic_page-1_powerbi.png)

### 🏃‍♂️ Athlete & Discipline Insights
Explores delegation sizes utilizing a Treemap and Stacked Column charts, exposing which sports required massive rosters (e.g., Athletics, Swimming) and comparing Coach-to-Athlete ratios globally.
![Athlete Insights](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/tokyo_olympic_page-2_powerbi.png)

### ⚖️ Gender Diversity & Participation
Utilizes 100% Stacked Bar charts to highlight the exact male-to-female ratio across every single discipline, analyzing the gender balance of the Tokyo 2020 games.
![Gender Diversity](https://github.com/Sumit-Kumar-Github/Raw-Data/blob/a8a038d598b615d135641d73ec2511cdb1eb03aa/Tokyo-Olympic-Data/images/tokyo_olympic_page-3_powerbi.png)

