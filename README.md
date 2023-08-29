
# ETL Azure Data Factory Project on Covid-19 Reporting:

### Concept of the Project 💡
- This project is about ingesting a couple of Covid-19 Datasets from the ECDC website, transforming them using various ADF components, and then performing transformations by using ADF, HDInsight, and Databricks, then loading them into SQL Datawarehouse for the Analytics team to derive useful and actionable insights from these datasets. The primary objective is to comprehensively understand the influence of COVID-19 on the entirety of the European Region throughout the course of the year 2020.

### Task 🎯
- The task of this project is to ingest the data from multiple data sources, and clean and transform the data to make it more robust and fit for the goal. The cleaned data should then be loaded into a central repository, like a Data warehouse or a datalake so that the analytics team can consume it with their BI tools such as Power BI. The data warehouse will include details about confirmed cases, unfortunate mortality rates, hospitalization and ICU cases from our weekly data lake counts, and the testing numbers. Also, we can run ML Models that use these data to predict the spread of COVID-19 in the European region.

### Source Data: 📤
- ECDC (https://www.ecdc.europa.eu/en/covid-19)
- Population Data From Azure Blob Storage (eurostat_data)

### Destination: 📥📍
- Azure Data Lake Gen2 Storage

## Tools ⚙

### Data Integration/Ingestion

- ADF Data Flows within the Data Factory

### Transformation

- Data Flows within the Data Factory
- DataBricks

### Data Warehouse Solution

- Azure SQL Database

### Visualization

- Power BI Desktop
- Power BI Service

### All the steps performed in this project are available as images in the [Screenshots](https://github.com/Eshwarreddyt/Data-Engineering-project-on-COVID_19-Reporting-using-Azure-Data-Factory/tree/main/Screenshots) folder in this repository.

### Approach

### Environment Setup
- Azure Subscription
- Data Factory
- Azure Blob Storage Account
- Data Lake Storage Gen2
- Azure SQL Database
- Azure Databricks Cluster
- HD Insight Cluster

# Solution Architecture Overview: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/1.png)

### Data Extraction/Data Ingestion
Four different datasets were ingested from both the ECDC website and Azure blob storage into Datalake Gen2.
They are:
* Cases and Deaths Data
* Hospital Admissions Data
* Population Data
* Test Conducted Data

We used various components of ADF Pipeline activities to ingest the data from both HTTP Data Source and Azure Storage Account to Azure DataLake. 
Some of those activities are:
- Validation Activity
- Get Metadata Activity
- Copy Activity

 ###  1- Population Data 
 
Ingest "population by age" data for all EU Countries into the Data Lake to support the machine learning models with the data to predict an increase in COVID-19 mortality rate.

###  Solution Flow:
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/2.png )


### Steps:
1. Create a Linked Service To Azure Blob Storage
2. Create a Source Data Set
3. Create a Linked Service To Azure Data Lake storage (GEN2)
4. Create a Sink Data set
5. Create a Pipeline:
- Execute Copy activity when the file becomes available
- Check metadata counts before loading the data using the IF Condition
- Finally, Load Data into our destination
- 6. ScheduleTrigger

###Pipeline Design:
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/3.png)

 ### 2- ECDC Data 
 #### the ECDC Data Content - Four files of CSV : 

1. Case & Deaths Data.csv
2. Hospital Admission Data.csv
3. testing.csv
4. country_response.csv
 
###  Solution Flow:
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/4.png)

Steps:
1. Create a Linked Service using an HTTP connector
2. Create a Source Data Set
3. Create a Linked Service To Azure Data Lake storage (GEN2)
4. Create a Sink Data set
5. Create a Pipeline With Parameters & Variables
6. Lookup to get all the parameters from json file, then pass it to ForEach ECDC DATA as shown below
7. Schedule Trigger

### Json File: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/5.png)


 ### Pipeline Design: 
 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/6.png)
 
# 2.Data Transformation 
The Cases and Deaths data together with the Hospital admissions data was transformed using ADF Data flows.
The Data flow transformation used on both dataset include;
- Select transformation
- Lookup transformation
- Filter transformation
- Join transformation
- Sort transformation
- Conditional split transformation
- Derived columns transformation
- Sink transformation
 
 ## Data Flows (1)  Cases & Deaths Data:

###  Solution Flow: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/7.png)

### Steps:
1. Cases And Deaths Source (Azure Data Lake Storage Gen2 )
2. Filter Europe-Only Data
3. Select only the required columns
4. PivotCounts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
5. Lookup Country to get country_code_2_digit,country_code_3_digit columns
6. Select Only the required columns for the Sink
7. Create a Sink dataset (Azure Data Lake Storage Gen2)
8. Used Schedule Trigger

 ### Pipeline Overview: 

 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/8.png)

 ## Data Flows (2)  Hospital Admissions Data:
 
 ###  Solution Flow: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/9.png)

### Steps:
1. Hospital Admissions Source (Azure Data Lake Storage Gen2 )
2. Select only the required columns
3. Lookup Country to get country_code_2_digit,country_code_3_digit columns
4. Select only the required columns
5. Condition Split Weekly, Daily Split condition
  - indicator=='Weekly new hospital admissions per 100k' || indicator=='Weekly new ICU admissions per 100k'
  - indicator== "Daily hospital occupancy" || indicator=="Daily ICU occupancy"
6. For Weekly Path
- Join with Date to get ecdc_Year_week, week_start_date, week_End_date
- Piovt Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
- Sort data using reported_year_week ASC and Country DESC
- Select only the required columns for the sink
- Create a sink dataset (Azure Data Lake Storage Gen2)
- Schedule Trigger
7. For Daily Path
- Pivot Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
- Sort Data using reported_year_week ASC and Country DESC
- Select only the required columns for the sink
- Create a sink dataset (Azure Data Lake Storage Gen2)
- Used Schedule Trigger
   
 ### Pipeline Design: 

![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/10.png)

 ## Databricks Activity (3) -- Population File:
 
 ###  Overview: 
 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/11.png)

Attached File: 
(https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/pyspark_notebooks/transform_population_data.py)

 #  Copy Data to Azure SQL
 
 1- Copy Cases and Deaths 
 
 2- Copy hospital admissions data
 
 3- Copy testing data 
 

 Sample :  
 ![4 pl_succ](https://github.com/hbuddana/Azure_Data_Factory_COVID-19_Reporting/assets/65592890/dea9e004-a556-4bc9-929a-d7239d6a9359)

 # Reporting via Power BI

 1- Create a Connection from Azure SQL to Power BI and load the data 
 
 2- Analyze the data to get the total confirmed cases and deaths count 
 
 3- Identify the trends in data based on reporting date
 
 4- Publish the report to Power BI Server
 
 5- Publish to web 
 
 ## Covid-19 Trend in the EU/EEA & UK 2020 by Cases, Deaths, Hospital Occupancy, and ICU Occupancy
 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/13.png)

 ## Covid-19 Cases and Death breakdown by population in the UK, France, and Germany
 ![image](https://github.com/Eshwarreddyt/Covid19-Reporting-ETL-Azure-DataFactory-Project/blob/main/Screenshots/images/14.png)

 ## Confirmed Cases Vs Total Deaths By Country
 ![image](https://github.com/Eshwarreddyt/Covid19-Reporting-ETL-Azure-DataFactory-Project/blob/main/Screenshots/images/15.png)

 ## Total Number of Covid tests carried out vs. Confirmed Cases
 ![image](https://github.com/Eshwarreddyt/Covid19-Reporting-ETL-Azure-DataFactory-Project/blob/main/Screenshots/images/16.png)
 
 ### Dashboard Link : 
[Power BI Dashboard Covid 19 Cases](https://app.powerbi.com/view?r=eyJrIjoiYjBhYWU0NTItMmVhOS00MGM5LTk1ZGEtMTQxZTdmZDUxMWUwIiwidCI6ImUwYjlhZTFlLWViMjYtNDZhOC1hZGYyLWQ3ZGJjZjIzNDBhOSJ9)

### Used Technologies :
1. Azure DataFactory
2. Azure HDinsight (Hive)
3. Azure Databricks (Pyspark, SparkSql)
4. Azure Storage Account
5. Azure Data Lake Gen2
6. Azure SQL Database

### All the steps performed in this project are available as images in the  [Screenshots](https://github.com/Eshwarreddyt/Data-Engineering-project-on-COVID_19-Reporting-using-Azure-Data-Factory/tree/main/Screenshots)) folder in this repository.


<div id="badges">
  <a href="https://www.linkedin.com/in/eshwarreddyt">
    <img src="https://img.shields.io/badge/LinkedIn-blue?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn Badge"/>
  </a>
</div>

This project allowed me to demonstrate my expertise in Azure technologies, particularly in using Azure Databricks and Data Factory to orchestrate complex data workflows. It highlights the power of Azure Databricks and Data Factory in automating the process of cleaning and storing data, which in turn saves a lot of time and effort. I am excited about the endless possibilities that Microsoft Azure offers and can't wait to explore more.


 

 
