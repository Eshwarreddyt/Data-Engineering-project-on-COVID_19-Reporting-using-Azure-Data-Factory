# Azure DataFactory Project on Covid-19 Reporting 

This project is aimed to ingest covid-19 related data from multiple sources like ECDC website and Azure Blob Storage into Azure Data Lake Gen2 and then perform transformation on these data using Azure DataFactory, HD Insight and Azure Databricks and store that data in Azure SQL Database and Azure Data Lake Storage Gen2 for reporting purposes and feeding ML models. 

Actions performed in this project include,
- Built a real-world data pipeline in Azure Data Factory (ADF).
- Ingested data from sources such as HTTP and Azure Blob Storage into Azure Data Lake Gen2     
  using Azure Data Factory (ADF).
- Transformed data using Data Flows in Azure Data Factory (ADF) and load into Azure Data Lake 
  Storage Gen2.
- Transformed data using Databricks Notebook Activity in Azure Data Factory (ADF) and load into 
  Azure Data Lake Storage Gen2.
- Transformed data using Azure HDInsight Activity in Azure Data Factory (ADF) and load into 
  Azure Data Lake Storage Gen2.
- Loaded transformed data from Azure Data Lake Storage Gen2 to Azure SQL Database using Azure 
  Data Factory (ADF).
- Built reports using Power BI on the data processed by the Azure Data Factory data pipelines.

**All the screenshots of the steps performed in this project are available in the Screenshots folder.**

Data Lake to be built with the following data to aid data scientists to predict the spread of the virus/mortality,
- Confirmed cases
- Mortality
- Hospitalization/ ICU Cases
- Testing Numbers
- Country’s population by age group

Data Warehouse to be built with the following data to aid Reporting on Trends,
- Confirmed cases
- Mortality
- Hospitalization/ ICU Cases
- Testing Numbers

**Data Sources:**

**ECDC Website**
- Confirmed cases
- Mortality
- Hospitalization/ ICU Cases
- Testing Numbers
  
**Eurostat Website**
- Population by age
