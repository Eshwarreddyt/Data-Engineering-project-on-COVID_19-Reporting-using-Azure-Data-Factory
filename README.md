
# Azure Data Factory Project on Covid-19 Reporting:

- This is a Microsoft Azure Data Engineering project on COVID-19 data from 2 different sources. I loaded the dataset with the help of Azure Data Factory and then performed transformations by using ADF, HDInsight, and Databricks. When the data was processed, it was then loaded into the Azure SQL database for analysis and generating reports. All of these objectives were achieved by using ADF pipelines and event-based triggers to automate the workflows.
 I created a data platform from which our data scientists can create machine-learning models and populated the data warehouse with the subset of the data so that it can be used for reporting on trends. The data warehouse will include details about confirmed cases, unfortunate mortality rates, hospitalization and ICU cases from our weekly data lake counts, and the testing numbers. I have also built some PowerBi reports from the transformed data.


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


# Solution Architecture Overview: 
![image](https://github.com/Eshwarreddyt/Azure-DataFactory-Project-Covid19-Reporting/blob/main/Screenshots/images/1.png)

 # Data Ingestion:
 ###  1- Population Data 
 
Ingest "population by age" data for all EU Countries into the Data Lake to support the machine learning models with the data to predict an increase in Covid-19 mortality rate.

###  Overview:
![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/50abaa2e-13aa-471c-9cfc-e36f78e4834d)


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


![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/6c17c903-8d2f-406e-9b6c-bfac9300fba5)

 ### 2- ECDC Data 
 #### the ECDC Data Content - Four files of CSV : 

##### 1. Case & Deaths Data.csv
##### 2. Hospital Admission Data.csv
##### 3. testing.csv
##### 4. country_response.csv
 
###  Overview:
![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/628015ca-81ce-4495-8cdf-53e983c3625b)

### Steps: 
#### 1. Create a Linked Service using an HTTP connector
#### 2. Create a Source Data Set
#### 3. Create a Linked Service To Azure Data Lake storage (GEN2)
#### 4. Create a Sink Data set
#### 5. Create a Pipeline With Parameters & Variables 
#### 6. used Lookup to get all the parameters from json file, then pass it to ForEach ECDC DATA as shown below 
#### 7. Used Schedule Trigger <br>

### Json File: 
![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/3334e5dd-b638-4f21-af29-7170f9f35eb0)


 ### Pipeline Overview: 
 ![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/0823f057-9f65-4c61-aed0-581a679a9d79)

### End Data Ingestion 
 # 2.Data Transformation 
 
 ## Data Flows (1)  Cases & Deaths Data:

###  Overview: 
![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/9d1ef49b-d2da-4424-a735-aeda963cc98c)

### Steps: 
#### 1. Cases And Deaths Source (Azure Data Lake Storage Gen2 )
#### 2. Filter Europe-Only Data 
#### 3. Select only required columns
#### 4. PivotCounts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
#### 5. Lookup Country to get country_code_2_digit,country_code_3_digit columns
#### 6. Select Only the required columns for the Sink
#### 7. Create a Sink dataset (Azure Data Lake Storage Gen2)
#### 8. Used Schedule Trigger <br>

 ### Pipeline Overview: 

 ![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/aa9e1bcd-1dfa-4582-9f22-ac18c04f033a)

 ## Data Flows (2)  Hospital Admissions Data:
 
 ###  Overview: 
![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/7b6b58dc-c381-4d15-89b3-afccec2e8e7a)

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

![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/6e8008e1-5255-43d8-86b0-286826a50435)

 ## Databricks Activity (3) -- Population File:
 
 ###  Overview: 


 ![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/97068206-7da2-418e-9a36-ed5a49085db4)

Attached File: 
pyspark_notebooks/transform_population_data.py

 ### End Data Transformation 


 #  Copy Data to Azure SQL

 1- Copy Cases and Deaths  <br> 
 2- Copy hospital admissions data  <br>
 3- Copy testing data   <br>

 Sample :  
 ![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/94083675-d0ec-44f4-8e13-b6ea596cd06d)

 ### End copy data to Azure SQL

 # Reporting via Power BI

 1- Create a Connection from Azure SQL to Power Bi and load the data  <br>
 2- Analyze the data to get the total confirmed cases and deaths count  <br>
 3- Identify the trends in data based on reporting date <br>
 4- Publish the report to Power BI Server   <br>
 5- Publish to web <br>
  ### Sample :
 ![image](https://github.com/AbdallahQoutbAli/Azure-Data-Factory-For-Data-Engineers---Project-on-Covid19/assets/47276503/e1dfa823-2abb-4742-aeb0-223cb6eebb68)

 ### Dashboard Link : 
[Power BI Dashboard Covid 19 Cases](https://app.powerbi.com/view?r=eyJrIjoiYjBhYWU0NTItMmVhOS00MGM5LTk1ZGEtMTQxZTdmZDUxMWUwIiwidCI6ImUwYjlhZTFlLWViMjYtNDZhOC1hZGYyLWQ3ZGJjZjIzNDBhOSJ9)



<p> 
Thanks <br>
Eshwar Reddy Thimmapuram <br>

eshwart28@gmail.com
 
[Linkedin](https://www.linkedin.com/in/eshwarreddyt/)
</p>

This project allowed me to demonstrate my expertise in Azure technologies, particularly in using Azure Databricks and Data Factory to orchestrate complex data workflows. It highlights the power of Azure Databricks and Data Factory in automating the process of cleaning and storing data, which in turn saves a lot of time and effort. I am excited about the endless possibilities that Microsoft Azure offers and can't wait to explore more.


 

 
