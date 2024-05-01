---
layout: post
author: matheus
name_displayed: Matheus Kempa
image: oracle.png
---

# Data Mart Project on Oracle Cloud OCI
  
  
## Introduction

This BI project is aimed at the Brazilian Football Championship. The goal is to collect, process and visualize relevant data to provide valuable insights into the championship.
The project ranges from creating a Data Mart to creating dashboards to reflect the data.

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a35d345a-ff83-4c76-b177-baf3b1b99bb9){:height="400" width="600px"}
{: refdef}


## Architecture

  
### DataFlow

The architecture created consists in 4 parts:

1. **WebScrapping**: For this part it was created a Python Script to scrap all site informations and save this information in the OCI Bucket. 
2. **Bucket**: The bucket was designed to store the *raw* data.
3. **DataMart**: The DataMart has a routine to pick the bucket information e consume the dimensions and load into Database.
4. **Analytics**: The Dashboard were all the information can be displayed.


{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a7d1c2a2-569d-4727-a36a-8744cfcb0d68){:height="400" width="600px"}
{: refdef}

The details for each part will be shown on the nexts steps.

  
### Modeling Architecture

The modeling was developed following the proposed by Ralph Kimball, the Star-Schema model.

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/f7dafba5-1e2a-4a2d-84b9-576874d33031){:height="400" width="600px"}
{: refdef}

## Creation and ETL Process Detailed:

### Resume

Now let's go deep into this architecture and explain the wholde Extraction, Transformation and Loading (ETL) process:

1. Football Data Collection: Data is collected by a Python script.
This script is responsible for extracting the relevant data necessary for the analysis.
2. Data Writing: After collection, the data is written to a Bucket in Oracle Cloud Infrastructure (OCI).
3. Loading into the Data Mart: After recording the data, the ETL process begins to load into the Data Mart. This process is divided into three steps:
* The data is loaded into a stage by a procedure that searches for the file in the bucket.
* The data is processed to be loaded in its respective dimensions.
* Finally, the FACT table is loaded.
This entire ETL process was carried out using Oracle's Autonomous Data Warehouse.

### Load History:
The historical data load was done for the following years 2019,2020,2021,2022,2023.

### Scheduling:
For the 2024 games, a routine was instantiated in the scheduler to automate the entire ETL process. This ensures that data is updated regularly and is always available for analysis.

### Data Consumption:
After the ETL process is complete, the data is consumed by reports. These reports are designed to provide a clear and understandable view of data, allowing for effective analysis.



## Sample Dashboard

I've create a sample Dashboard for showing how its going.

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/103e857e-1c2a-4d12-a115-dbb46e39974e){:height="400" width="600px"}
{: refdef}


