
# Azure Data Factory Project on Covid-19 Reporting:

- This is a Microsoft Azure Data Engineering project on COVID-19 data collected from 2 different sources. I loaded the dataset with the help of Azure Data Factory and then performed transformations by using ADF, HDInsight, and Databricks. When the data was processed, it was then loaded into the Azure SQL database for analysis and generating reports. All of these objectives were achieved by using ADF pipelines and event-based triggers to automate the workflows.
- I created a data platform from which our data scientists can create machine-learning models and populated the data warehouse with the subset of the data so that it can be used for reporting on trends. The data warehouse will include details about confirmed cases, unfortunate mortality rates, hospitalization and ICU cases from our weekly data lake counts, and the testing numbers. I have also built some PowerBi reports from the transformed data.


### Source Data:
- ECDC (https://www.ecdc.europa.eu/en/covid-19) 
- Population Data From Azure Blob Storage (eurostat_data)

### Destination: <br>
 Azure Data Lake Storage (GEN2) <br>
 
 ### Tools: <br> 
 - Data Integration/Ingestion: 
    - used ADF Data Flows within the Data Factory  
 
 - Transformation: 
   -  Data Flows within the Data Factory
   -  DataBricks 
 
 - Data warehouse solution: 
    -  used Azure SQL database
 
 - Visualization: 
    - Power BI Desktop and Power BI service  


<br> Note : <br>
- Data Factory will be used as the orchestration for the HDInsight and Databricks services.  <br>
- All the transformed data will then be stored in our Azure Data Lake storage. (GEN2)

### All the steps performed in this project are available as images in the screenshots folder in this repository. <br>


# Solution Architecture Overview: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/1.png)

 # Data Ingestion:
 ###  1- Population Data 
 
Ingest "population by age" data for all EU Countries into the Data Lake to support the machine learning models with the data to predict an increase in Covid-19 mortality rate.

###  Overview:
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/2.png )


### Steps: 
#### 1. Create a Linked Service To Azure Blob Storage 
#### 2. Create a Source Data Set
#### 3. Create a Linked Service To Azure Data Lake storage (GEN2)
#### 4. Create a Sink Data set
#### 5. Create a Pipeline:
  <ol>
   <li> Execute Copy activity when the file becomes available </li>
   <li> Check metadata counts before loading the data using the IF Condition  </li>
   <li> Finally Load Data into our destination </li>
  </ol>
  
#### 6. ScheduleTrigger <br>
 ### Pipeline Overview: 


![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/3.png)

 ### 2- ECDC Data 
 #### the ECDC Data Content - Four files of CSV : 

##### 1. Case & Deaths Data.csv
##### 2. Hospital Admission Data.csv
##### 3. testing.csv
##### 4. country_response.csv
 
###  Overview:
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/4.png)

### Steps: 
#### 1. Create a Linked Service using an HTTP connector
#### 2. Create a Source Data Set
#### 3. Create a Linked Service To Azure Data Lake storage (GEN2)
#### 4. Create a Sink Data set
#### 5. Create a Pipeline With Parameters & Variables 
#### 6. used Lookup to get all the parameters from json file, then pass it to ForEach ECDC DATA as shown below 
#### 7. Used Schedule Trigger <br>

### Json File: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/5.png)


 ### Pipeline Overview: 
 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/6.png)

### End Data Ingestion 
 # 2.Data Transformation 
 
 ## Data Flows (1)  Cases & Deaths Data:

###  Overview: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/7.png)

### Steps: 
#### 1. Cases And Deaths Source (Azure Data Lake Storage Gen2 )
#### 2. Filter Europe-Only Data 
#### 3. Select only the required columns
#### 4. PivotCounts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
#### 5. Lookup Country to get country_code_2_digit,country_code_3_digit columns
#### 6. Select Only the required columns for the Sink
#### 7. Create a Sink dataset (Azure Data Lake Storage Gen2)
#### 8. Used Schedule Trigger <br>

 ### Pipeline Overview: 

 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/8.png)

 ## Data Flows (2)  Hospital Admissions Data:
 
 ###  Overview: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/9.png)

### Steps: 
#### 1. Hospital Admissions Source (Azure Data Lake Storage Gen2 )
#### 2. Select only the required columns 
#### 3. Lookup Country to get country_code_2_digit,country_code_3_digit columns
#### 4. Select only the required columns
#### 5. Condition Split Weekly, Daily Split condition 
 <ol>
   <li> indicator=='Weekly new hospital admissions per 100k' || indicator=='Weekly new ICU admissions per 100k'</li>
   <li>  indicator== "Daily hospital occupancy" || indicator=="Daily ICU occupancy" </li>
 </ol>
  
#### 6. For Weekly Path 
 <ol>
    <li> Join with Date to get ecdc_Year_week, week_start_date, week_End_date</li>
   <li> Piovt Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count </li>
   <li> Sort data using reported_year_week ASC and Country DESC  </li>
   <li> Select only required columns for sink </li>
   <li> Create a sink dataset (Azure Data Lake Storage Gen2) </li>
   <li> Used Schedule Trigger </li>
 
 </ol>

   
#### 7. For Daily Path 
 <ol>
   <li> Pivot Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count </li>
   <li> Sort Data using reported_year_week ASC and Country DESC  </li>
   <li> Select only required columns for sink </li>
   <li> Create a sink dataset (Azure Data Lake Storage Gen2) </li>
   <li> Used Schedule Trigger </li>

 </ol>
 

 ### Pipeline Overview: 

![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/10.png)

 ## Databricks Activity (3) -- Population File:
 
 ###  Overview: 


 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/11.png)

Attached File: 
(https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/pyspark_notebooks/transform_population_data.py)

 ### End Data Transformation 


 #  Copy Data to Azure SQL

 1- Copy Cases and Deaths  <br> 
 2- Copy hospital admissions data  <br>
 3- Copy testing data   <br>

 Sample :  
 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/12.png)

 ### End copy data to Azure SQL

 # Reporting via Power BI

 1- Create a Connection from Azure SQL to Power Bi and load the data  <br>
 2- Analyze the data to get the total confirmed cases and deaths count  <br>
 3- Identify the trends in data based on reporting date <br>
 4- Publish the report to Power BI Server   <br>
 5- Publish to web <br>
  ### Sample :
 ![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/13.png)

 ### Dashboard Link : 
[Power BI Dashboard Covid 19 Cases](https://app.powerbi.com/view?r=eyJrIjoiYjBhYWU0NTItMmVhOS00MGM5LTk1ZGEtMTQxZTdmZDUxMWUwIiwidCI6ImUwYjlhZTFlLWViMjYtNDZhOC1hZGYyLWQ3ZGJjZjIzNDBhOSJ9)

### Used Technologies :
1. Azure DataFactory
2. Azure HDinsight (Hive)
3. Azure Databricks (Pyspark, SparkSql)
4. Azure Storage Account
5. Azure Data Lake Gen2
6. Azure SQL Database

### All the steps performed in this project are available as images in the screenshots folder in this repository. <br>


<p> 
Thanks, <br>
Eshwar Reddy Thimmapuram <br>
eshwart28@gmail.com
 
[Linkedin](https://www.linkedin.com/in/eshwarreddyt/)
</p>

This project allowed me to demonstrate my expertise in Azure technologies, particularly in using Azure Databricks and Data Factory to orchestrate complex data workflows. It highlights the power of Azure Databricks and Data Factory in automating the process of cleaning and storing data, which in turn saves a lot of time and effort. I am excited about the endless possibilities that Microsoft Azure offers and can't wait to explore more.


 

 
