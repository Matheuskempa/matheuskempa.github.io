---
layout: post
author: matheus
name_displayed: Matheus Kempa
image: oracle.png
---

# Data Mart Project on Oracle Cloud OCI

  
This is the first post of this blog and this post will be dedicated to one BI project and we are going to use Oracle Cloud Infrastructure (OCI). Since this is a blog post and not an academic paper, the main focus it will be "delivery good content with good and real projects" always with emphasis ont practice part. The theory behind will also be covered and the references will be shown at the end of the post. Without further ado let's dive in the concepts that are necessary to understand this Project. 

## Concepts

### Business Intelligence

Created in the 1960s Business Intelligence ***(BI)*** lays on the premise of deliverying information to support better decision making.
In order to achive that BI systems often involve data warehousing, data mining, reporting, and data visualization, all this combined to help turn raw data into insights with certain speed.

At the beginning of my career, I used to perceive BI as simply dashboards and panels, but I've come to realize that this is just the tip of the iceberg. There's a lot more beneath those beautiful dashboards that we see. While many people nowadays have a deep understanding of BI, some still believe it's just a matter of 'plugging' BI into the database and creating visualizations. I recently encountered this issue when a colleague created an excellent dashboard using several data sources. However, we couldn't deploy it to the production environment because the data source was too large for Microsoft Power BI to import. Even when we tried using Direct Query mode, the data source was still too large, preventing us from loading it onto the production server.

### Data Warehouse and DataMart

The main difference of a data warehouse from a database is its focus and structure. While a database is optimized for transactional processing and managing current data, a data warehouse is designed for analytical processing and storing large volumes of historical data from various sources within an organization. Additionally, a data warehouse typically undergoes an ETL (Extract, Transform, Load) process to clean, transform, and integrate data before storing it in a structured format for analysis.

A data mart, on the other hand, is a subset of a data warehouse that is focused on a specific department, function, or line of business within an organization. Data marts are often created to provide targeted reporting and analysis capabilities to specific user groups, allowing them to access and analyze relevant data more efficiently.

#### Star Schema Modelling

Star schema modeling is a popular technique used in data warehouse design. It involves organizing data into a central "fact" table surrounded by multiple "dimension" tables. The fact table contains numerical measures or metrics that represent business events (e.g., sales transactions), while the dimension tables contain descriptive attributes that provide context to the measures (e.g., customer, product, time). This star schema structure simplifies querying and analysis by enabling users to easily navigate and aggregate data along different dimensions.

## Project Resume

So This BI project aims to Brazilian Football Championship and the goal is to collect, process and visualize relevant data to provide valuable insights into the championship.
The project will cover since the creation of the Data Mart, to the ETL and the creation of the Dashboards to reflect the data.

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a35d345a-ff83-4c76-b177-baf3b1b99bb9)
{: refdef}


## Architecture

The architecture consists of four parts:

1. **Web Scraping**
2. **Bucket Store**
3. **Data Warehouse**
4. **Analytics**

As shown in the image below:

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a7d1c2a2-569d-4727-a36a-8744cfcb0d68)
{: refdef}

Now let's delve deeper into this architecture and explain the entire Extraction, Transformation, and Loading (ETL) process. As an Oracle expert, I probably didn't do it in the best way possible (using Oracle best practices), because at the current time, this project is my first contact with Oracle Cloud. So here is how it was done: 

### WebScrapping and Bucket Store

As I didn't have a provider for Football data, I choose to do it in the hard way, using WebScrapping tools. The code is responsible scrpaping site data and save on OCI Bucket Store.



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



### Modeling DataMart Architecture

The modeling was developed following the proposed by Ralph Kimball, the Star-Schema model.

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/f7dafba5-1e2a-4a2d-84b9-576874d33031)
{: refdef}




## Sample Dashboard

This Dashboard were created using Oracle Cloud Anlytics , (...)

{:refdef: style="text-align: center;"}
![image](https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/103e857e-1c2a-4d12-a115-dbb46e39974e)
{: refdef}


## Conclusions

I really liked to use Oracle Cloud, if you already know Cloud from other companys like Amazon,Google or Microsoft i think it's going to be easy to deploy and create your applications there, otherwhise i think that it's beter to find a minimal course to guide you in your first steps, like Alura's course. I encorouje you to think in some real project and try to deploy there. It as an amazing experiecne for me! I learned a lot of PL/SQL and OCI and OCA.  

## Referential

KIMBALL, R. The Kimball Group reader : relentlessly practical tools for data warehousing and business intelligence : remastered collection. Indianapolis, Indiana: Wiley, 2016.


<oracle-dv project-path="/@Catalog/users/matheusskempa@hotmail.com/DashBoard FuteLab" active-page="insight" active-tab-id="snapshot!canvas!1">
</oracle-dv>
‌