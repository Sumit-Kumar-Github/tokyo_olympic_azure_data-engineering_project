# 🚀 Step-by-Step Project Execution Guide

This document provides a comprehensive, visual walkthrough of the entire data engineering and analytics pipeline built for the Tokyo 2020 Olympics dataset. It documents every phase of the ETL process across the Microsoft Azure ecosystem and Power BI.

---

## Phase 1: Resource Provisioning & Storage Setup

**1. Azure Resource Group** To keep the project organized and centrally managed, a dedicated Resource Group named `tokyo-olympic` was created. This group houses all the required services: Azure Data Factory, Databricks, Synapse Analytics, and the Storage Account.
![Resource Group](../images/1.jpg)

**2. Azure Data Lake Storage Gen2 (ADLS Gen2)** A storage account (`tokyoolympicdatasumit`) was deployed with a hierarchical namespace enabled to allow folder-level structuring of big data.
![Storage Account](../images/2.jpg)

**3. Storage Containers** Inside the ADLS Gen2 account, a primary container named `tokyo-olympic-data` was created to serve as the main data lake repository.
![Storage Container](../images/3.jpg)

**4. Data Directories (Raw & Transformed)** Within the container, the data lake was logically divided into two directories: `raw-data` (to store the initial, unprocessed files) and `transformed-data` (to store the cleaned, query-ready data).
![Data Directories](../images/4.jpg)

---

## Phase 2: Data Ingestion (Extract)

**5. Azure Data Factory Studio** Azure Data Factory (ADF) was launched to build the automated data pipelines responsible for extracting data from the source and loading it into the cloud.
![Data Factory Studio](../images/5.jpg)

**6. Automated Ingestion Pipeline** A pipeline named `data-ingestion` was engineered using multiple "Copy Data" activities. These activities ran in parallel to fetch the 5 core CSV datasets (Athletes, Coaches, EntriesGender, Medals, Teams) via HTTP and sink them directly into the `raw-data` directory of the Data Lake.
![Data Factory Pipeline](../images/6.jpg)

---

## Phase 3: Compute & Authentication Setup

**7. Azure Databricks Workspace** For massive parallel data processing, an Azure Databricks workspace (`tokyo-olympic-d_bricks`) was provisioned.
![Databricks Workspace](../images/7.jpg)

**8. PySpark Cluster Configuration** A compute cluster (`Sumit Kumar's Cluster`) was configured with 16GB memory and 4 cores. This cluster provides the engine required to run the PySpark transformation scripts.
![Databricks Cluster](../images/8.jpg)

**9. Service Principal Creation (App Registration)** To allow Databricks to securely read and write data to ADLS Gen2 without exposing root credentials, an App Registration named `app01` was created in Microsoft Entra ID.
![App Registration](../images/9.jpg)

**10. Generating Client Secrets** A secret key was generated for `app01` to establish a secure, programmatic OAuth 2.0 connection.
![App Secrets](../images/10.jpg)

**11. Credential Extraction** The Tenant ID, Client ID, and Secret Value were temporarily extracted to safely pass them into the Databricks configuration code.
![Credential Extraction](../images/11.jpg)

**12. Access Control (IAM)** Navigated to the Access Control (IAM) settings of the ADLS Gen2 storage container to grant the Service Principal the correct permissions.
![IAM Access Control](../images/12.jpg)

**13. Role Assignment** The `Storage Blob Data Contributor` role was selected, which grants full read/write access to the storage blobs.
![Role Assignment](../images/13.jpg)

**14. Assigning the App** The `app01` Service Principal was selected as the designated member for this role.
![Selecting App01](../images/14.png)

**15. Finalizing Permissions** The role assignment was reviewed and applied, officially bridging the security gap between Databricks and the Data Lake.
![Review and Assign](../images/15.png)

---

## Phase 4: Data Transformation (Transform)

**16. Mounting Storage to Databricks** Using a Databricks Notebook, a PySpark script was written to mount the ADLS Gen2 storage container directly to the Databricks File System (DBFS) using the OAuth credentials generated earlier.
![Mounting Storage](../images/16.jpg)

**17. Data Extraction & Schema Inference** The raw CSV files were read from the `/mnt/tokyoolympic/raw-data` directory into PySpark DataFrames. The `inferSchema` option was enabled to ensure numerical values (like medal counts) were correctly cast as integers instead of strings.
![Reading DataFrames](../images/17.jpg)

**18. Writing Transformed Data** After validation, the DataFrames were written back to the data lake, this time into the `/mnt/tokyoolympic/transformed-data` directory, structured and ready for analytical queries.
![Writing Transformed Data](../images/18.jpg)

---

## Phase 5: Data Serving & Analytics (Load)

**19. Azure Synapse Analytics Workspace** Azure Synapse Analytics was deployed to act as the analytics service and data warehouse bridge.
![Synapse Workspace](../images/19.jpg)

**20. Creating Lake Database Tables** Using Synapse's Serverless SQL pools, logical tables were created pointing directly to the transformed CSVs in the data lake. This makes the Big Data instantly accessible to BI tools using standard SQL syntax.
![Synapse Tables](../images/20.jpg)

---

## Phase 6: Data Modeling & Visualization

**21. Power BI Data Modeling** The data was imported into Power BI. A data model was established using Many-to-Many relationships with bidirectional cross-filtering to seamlessly connect the `teams`, `medals`, `athletes`, `coaches`, and `entriesGender` tables.
![Power BI Model](../images/21.jpg)

**22. Dashboard 1: Executive Summary** A high-level landing page utilizing DAX measures to display total KPIs, dynamic country/discipline slicers, and an efficiency scatter plot.
![Executive Summary](../images/Screenshot 2026-02-28 173228.jpg)

**23. Dashboard 2: The Olympic Summary** A geographical distribution of medals and a classic Olympic leaderboard built using Matrix visuals.
![Olympic Summary](../images/Screenshot 2026-02-28 131528.jpg)

**24. Dashboard 3: Athlete & Discipline Insights** Deep dive into delegation sizes using Treemaps and Coach-to-Athlete correlations.
![Athlete Insights](../images/Screenshot 2026-02-28 163204.jpg)

**25. Dashboard 4: Gender Diversity** 100% Stacked Bar charts providing an analytical look into the male-to-female ratio across every discipline.
![Gender Diversity](../images/Screenshot 2026-02-28 165745.jpg)

---
*This step-by-step pipeline confirms the successful extraction, transformation, and visualization of the Tokyo 2020 dataset using enterprise-grade cloud architecture.*
