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

![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a35d345a-ff83-4c76-b177-baf3b1b99bb9)


## ETL Process:

### Resume
The Extraction, Transformation and Loading (ETL) process is carried out as follows:

1. Football Data Collection: Data is collected by a Python script.
This script is responsible for extracting the relevant data necessary for the analysis.
2. Data Writing: After collection, the data is written to a Bucket in Oracle Cloud Infrastructure (OCI).
3. Loading into the Data Mart: After recording the data, the ETL process begins to load into the Data Mart. This process is divided into three steps:
o The data is loaded into a stage by a procedure that searches for the file in the bucket.
o The data is processed to be loaded in its respective dimensions.
o Finally, the FACT table is loaded.
This entire ETL process was carried out using Oracle's Autonomous Data Warehouse.

### Load History:
The historical data load was done for the following years 2019,2020,2021,2022,2023.

### Scheduling:
For the 2024 games, a routine was instantiated in the scheduler to automate the entire ETL process. This ensures that data is updated regularly and is always available for analysis.

### Data Consumption:
After the ETL process is complete, the data is consumed by reports. These reports are designed to provide a clear and understandable view of data, allowing for effective analysis.

## Overall Architecture


![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a7d1c2a2-569d-4727-a36a-8744cfcb0d68)


## Modeling Architecture

The modeling was developed following the proposed by Ralph Kimball, the StarSchema model.

![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/f7dafba5-1e2a-4a2d-84b9-576874d33031)


## Sample Dashboard

I've create a sample Dashboard for showing how its going.

![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/103e857e-1c2a-4d12-a115-dbb46e39974e)


The project is still not over, at the current moment steps 1,2,3 are finished but they are not fully orchestrared.
Soon i will bring more updates with all the source codes.


