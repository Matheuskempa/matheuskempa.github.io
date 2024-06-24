---
layout: post
author: matheus
name_displayed: Matheus Kempa
image: oracle_theme.png
subtitle: A real-life Data Mart project utilizing Oracle Cloud Infrastructure and Analytics.
language_tag: EN 
---

#### Table of Contents

1. [Introduction](#introduction)
2. [Project](#project)
    - [Architecture](#architecture)
    - [WebScrapping and Bucket Store](#web-scraping-and-bucket-store)
    - [Data Mart](#data-mart)
        - [Data Mart Modeling](#modeling-data-mart-architecture)
        - [ETL](#etl)
    - [Analytics](#analytics)
3. [Conclusions](#conclusions)
4. [References](#references)


<br>

---

## Introduction

This is the first post of the blog, and it will be dedicated to a Business Intelligence project on Oracle Cloud. As it's a blog post rather than an academic paper, the primary focus will be on delivering valuable content and real-life projects.

At the beginning of my career, I used to perceive BI as simply dashboards and panels, but I've come to realize that this is just the tip of the iceberg. There's a lot more beneath those beautiful dashboards that we see. While many people nowadays have a deep understanding of BI, some still believe it's just a matter of 'plugging' BI into the database and creating visualizations.

Created in the 1960s, Business Intelligence ***(BI)*** lays on the premise of delivering information to support better decision making. In order to achieve that, BI systems often involve data warehousing, data mining, reporting, and data visualization, all combined to help turn raw data into insights with speed.

Generally speaking about Data Warehousing, it's important to understand some differences against transactional Databases. In summary, the main difference of a data warehouse from a database is its focus and structure. While a database is optimized for transactional processing and managing current data, a data warehouse is designed for analytical processing and storing large volumes of historical data from various sources within an organization. Additionally, a data warehouse typically undergoes an ETL (Extract, Transform, Load) process to clean, transform, and integrate data before storing it in a structured format for analysis.

A data mart, on the other hand, is a subset of a data warehouse that is focused on a specific department, function, or line of business within an organization. Data marts are often created to provide targeted reporting and analysis capabilities to specific user groups, allowing them to access and analyze relevant data more efficiently. Inside of a data mart, data will be stored, but for that, the data have to be modeled, and so the data mart has to be designed. This is where star schema comes in (although there is snowflake modeling too, we are not going to use it here).

Star schema modeling is a popular technique used in data warehouse design. It involves organizing data into a central "fact" table surrounded by multiple "dimension" tables. The fact table contains numerical measures or metrics that represent business events (e.g., sales transactions), while the dimension tables contain descriptive attributes that provide context to the measures (e.g., customer, product, time). This star schema structure simplifies querying and analysis by enabling users to easily navigate and aggregate data along different dimensions.



<br>

## Project

I created this project for a selection process I was participating in at Oracle. As I had not worked with Oracle until now, I didn't use some specific tools like Oracle Data Integration, as I am not familiar with them. So, I went for the traditional way, coding in PL/SQL, taking advantage of the fact that I have a good background in SQL with T-SQL and PL/pgSQL. Here is the full documentation of the [project](https://github.com/Matheuskempa/matheuskempa.github.io/blob/feat/oracle_apex/assets/documents/DOCUMENTACAO_TECNICA.pdf).

This BI project aims to utilize Brazilian Football Championship data with the goal of collecting, processing, and visualizing relevant data to provide valuable insights into the championship.

The project will cover everything from the creation of the Data Mart to the ETL process and the creation of dashboards to reflect the data.

<img class="img-fluid" src="https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a35d345a-ff83-4c76-b177-baf3b1b99bb9" alt="Resume" style="width:800px;"/>




### Architecture

The architecture consists of three main parts:

1. **Web Scraping** and **Bucket Store**
2. **Data Mart**
3. **Analytics**

As shown in the image below:


<img class="img-fluid" src="https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/a7d1c2a2-569d-4727-a36a-8744cfcb0d68" alt="Resume" style="width:800px;"/>



Now let's delve deeper into this architecture and explain the entire Extraction, Transformation, and Loading (ETL) process. Since this is my first contact with Oracle Cloud, I probably didn't adhere to Oracle's best practices. So here is how it was done:

<br>

### Web Scraping and Bucket Store

As I didn't have access to a provider for football data, I opted to use web scraping tools. The code is responsible for scraping site data and saving it to OCI Bucket Store.

- Football Data Collection: Data is collected by a Python script from [FBREF](https://fbref.com/). The script extracts the relevant data necessary for the analysis.
- Data Writing: After collection, the data is written to a bucket in Oracle Cloud Infrastructure (OCI).

<br>

### Data Mart

#### Modeling Data Mart Architecture

In the image below, we have the Dimensional modeling that we've discussed, specifically the Star Schema. With this modeling, we are able to have high-performance access to the data for querying.



<img class="img-fluid" src="https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/f7dafba5-1e2a-4a2d-84b9-576874d33031" alt="Resume" style="width:800px;"/>

 
<br>

#### ETL

Now the next step is to load into the Data Mart. The ETL process begins by loading into the Data Mart and is divided into three steps:

**Stage**

The data is loaded into a stage by a procedure that searches for the file in the bucket and writes it to a pre-created table. (For more information, visit [Oracle Documentation](https://docs.oracle.com/en/cloud/paas/autonomous-database/dedicated/adbdm/index.html))

> **Procedure's DDL:**

```
-- sql
CREATE OR REPLACE EDITIONABLE PROCEDURE "ADMIN"."SP_IMPORTAR_DADOS_STAGE" (
    PARAMETER_OBJECT VARCHAR2,
    PARAMETER_TABELA VARCHAR2
) AS


    BEGIN
        execute immediate 'TRUNCATE TABLE ' || PARAMETER_TABELA;

        DBMS_CLOUD.COPY_DATA(
            table_name => PARAMETER_TABELA,
            file_uri_list => '<BUCKET URL>' || PARAMETER_OBJECT,
            format => json_object(
                'delimiter' value ',',
                'skipheaders' value 1,
                'blankasnull' value true,
                'ignoremissingcolumns' value true,
                'quote' value '"', 
                'endquote' value '"',
                'enablelogs' value FALSE
            )
        );
    END;
/


```
**Dimensions and Fact**

The data is processed to be loaded into their respective dimensions. After all the dimensions are fully loaded, we proceed to load the fact table. For that, I've created some procedures. These procedures load the data already treated inside the dimensions and the fact, and generate a log register. Here's an example of the DDL for the procedures:

> **Procedure's DDL:**


```
-- sql
CREATE OR REPLACE EDITIONABLE PROCEDURE "ADMIN"."SP_DIM_PARTIDAS" IS
BEGIN
    INSERT INTO TB_LOGS(NM_TABELA, DS_EVENTO, ST_STATUS)
    VALUES ('DIM_PARTIDAS', 'MERGE', 'INICIADO');

    MERGE INTO DIM_PARTIDAS dest
    USING (
        SELECT 
            ID_PARTIDA AS ID_FONTE,
            HOME || ' X ' || AWAY AS NM_PARTIDA,
            NULL AS DS_DETALHES,
            PARTIDAS_FINAL AS URL,
            REFEREE AS NM_ARBITRO,
            VENUE AS NM_ESTADIO
        FROM ADMIN.BRASILEIRAO_JOGOS 
    ) src
    ON (src.ID_FONTE = dest.ID_FONTE) 
    WHEN MATCHED THEN
        UPDATE SET 
            dest.NM_PARTIDA = src.NM_PARTIDA, 
            dest.DS_DETALHES = src.DS_DETALHES,
            dest.URL = src.URL,
            dest.NM_ARBITRO = src.NM_ARBITRO,
            dest.NM_ESTADIO = src.NM_ESTADIO,
            dest.DT_ALTERACAO = CURRENT_TIMESTAMP

    WHEN NOT MATCHED THEN
        INSERT (
            ID_FONTE, 
            NM_PARTIDA, 
            DS_DETALHES,
            URL,
            NM_ARBITRO,
            NM_ESTADIO
        ) 
        VALUES ( 
            src.ID_FONTE, 
            src.NM_PARTIDA, 
            src.DS_DETALHES,
            src.URL,
            src.NM_ARBITRO,
            src.NM_ESTADIO
        ); 

    UPDATE TB_LOGS
    SET 
        ST_STATUS = 'FINALIZADO', 
        DT_ALTERACAO = CURRENT_TIMESTAMP, 
        NR_LINHAS = (SELECT COUNT(*) FROM (
        SELECT 
            ID_PARTIDA AS ID_FONTE,
            HOME || ' X ' || AWAY AS NM_PARTIDA,
            NULL AS DS_DETALHES,
            PARTIDAS_FINAL AS URL,
            REFEREE AS NM_ARBITRO,
            VENUE AS NM_ESTADIO
        FROM ADMIN.BRASILEIRAO_JOGOS 
    ))
    WHERE ID = (SELECT MAX(ID) FROM TB_LOGS WHERE NM_TABELA = 'DIM_PARTIDAS' AND DS_EVENTO = 'MERGE');

EXCEPTION
    WHEN OTHERS THEN
        UPDATE TB_LOGS
        SET 
            ST_STATUS = 'ERRO', 
            DT_ALTERACAO = CURRENT_TIMESTAMP
        WHERE ID = (SELECT MAX(ID) FROM TB_LOGS WHERE NM_TABELA = 'DIM_PARTIDAS' AND DS_EVENTO = 'MERGE');
END SP_DIM_PARTIDAS;
/

```
<br>

**Logs** 

Logs are really important as they keep a trace of everything that happens in a system. So, when you're creating new solutions, always make sure to remember the logs! Our routine logs were saved in a table named **tb_logs**:


<img class="img-fluid" src="/../assets/images/data_logs.png" alt="Resume" style="width:800px;"/>


Finally, the FACT table is loaded in the same way. This entire ETL process was carried out using Oracle's Autonomous Data Warehouse.

**History Data:**

The historical data load was done for the following years: 2019, 2020, 2021, 2022, 2023.

**Scheduling:**

For the 2024 games, a routine was instantiated in the scheduler to automate the entire ETL process. This ensures that data is updated regularly and is always available for analysis.

For that, a job was created inside Oracle Autonomous Data Warehouse to run a procedure responsible for running all the procedures:

> **Procedure's DDL:**


```
CREATE OR REPLACE EDITIONABLE PROCEDURE "ADMIN"."EXECUTE_ALL_IMPORT_PROCEDURES" AS
BEGIN

    SP_IMPORTAR_DADOS_STAGE('BRASILEIRAO_TOTAL_2024.csv','BRASILEIRAO_TOTAL');
    SP_IMPORTAR_DADOS_STAGE_JOGOS('BRASILEIRAO_JOGOS_2024.csv','BRASILEIRAO_JOGOS');
    ADMIN.SP_DIM_JOGADORES;
    ADMIN.SP_DIM_NUMERACOES;
    ADMIN.SP_DIM_PARTIDAS;
    ADMIN.SP_DIM_POSICOES;
    ADMIN.SP_DIM_TIMES;
    ADMIN.SP_FATO;
END;
```

> **Job's DDL:**

```
-- sql
BEGIN 
    SYS.DBMS_SCHEDULER.CREATE_JOB ( 
        job_name => 'ADMIN.FUTELAB_JOB_FINAL',
        job_type => 'PLSQL_BLOCK',
        job_action => 'BEGIN ADMIN.EXECUTE_ALL_IMPORT_PROCEDURES(); END;'
    ); 

    SYS.DBMS_SCHEDULER.SET_ATTRIBUTE( 
        name => 'ADMIN.FUTELAB_JOB_FINAL',
        attribute => 'START_DATE',
        value => TO_TIMESTAMP_TZ('2024-05-03T07:56:42 +00:00','YYYY-MM-DD"T"HH24:MI:SS.FF TZR'));
    SYS.DBMS_SCHEDULER.SET_ATTRIBUTE( 
        name => 'ADMIN.FUTELAB_JOB_FINAL',
        attribute => 'REPEAT_INTERVAL',
        value => 'FREQ=DAILY; BYHOUR=2');
    SYS.DBMS_SCHEDULER.SET_ATTRIBUTE( 
        name => 'ADMIN.FUTELAB_JOB_FINAL',
        attribute => 'STORE_OUTPUT',
        value => TRUE);
    SYS.DBMS_SCHEDULER.SET_ATTRIBUTE( 
        name => 'ADMIN.FUTELAB_JOB_FINAL',
        attribute => 'NLS_ENV',
        value => 'NLS_LANGUAGE=''BRAZILIAN PORTUGUESE'' NLS_TERRITORY=''AMERICA'' NLS_CURRENCY=''$'' NLS_ISO_CURRENCY=''AMERICA'' NLS_NUMERIC_CHARACTERS=''.,'' NLS_CALENDAR=''GREGORIAN'' NLS_DATE_FORMAT=''DD-MON-RR'' NLS_DATE_LANGUAGE=''BRAZILIAN PORTUGUESE'' NLS_SORT=''WEST_EUROPEAN'' NLS_TIME_FORMAT=''HH.MI.SSXFF AM'' NLS_TIMESTAMP_FORMAT=''DD-MON-RR HH.MI.SSXFF AM'' NLS_TIME_TZ_FORMAT=''HH.MI.SSXFF AM TZR'' NLS_TIMESTAMP_TZ_FORMAT=''DD-MON-RR HH.MI.SSXFF AM TZR'' NLS_DUAL_CURRENCY=''$'' NLS_COMP=''BINARY'' NLS_LENGTH_SEMANTICS=''BYTE'' NLS_NCHAR_CONV_EXCP=''FALSE''');


    SYS.DBMS_SCHEDULER.enable(name => 'ADMIN.FUTELAB_JOB_FINAL'); 
END;
/

```

<br>


## Analytics

After all the ETL process, it's easy to create dashboards. Here's a sample that I've created using Oracle Cloud Analytics:

<img class="img-fluid" src="https://github.com/Matheuskempa/matheuskempa.github.io/assets/31332829/103e857e-1c2a-4d12-a115-dbb46e39974e" alt="Resume" style="width:900px;"/>



<br>


## Conclusions

I really liked using Oracle Cloud. If you're already familiar with cloud platforms from other companies like Amazon, Google, or Microsoft, I think it'll be easy to deploy and create your own applications there. Otherwise, I believe it's better to find a minimal course to guide you through your first steps.

I encourage you to think of a real project and try to deploy it there, as you have 30 days with credits to use. It was an amazing experience for me! I learned a lot about PL/SQL, OCI, and OCA. I also found some amazing features within the Oracle Autonomous Database that I will discuss in future posts.

---

<br>



## References
If you're looking for references on Data Warehousing and BI, I would strongly recommend the book bellow:

[1] KIMBALL, R. The Kimball Group Reader: Relentlessly Practical Tools for Data Warehousing and Business Intelligence: Remastered Collection. Indianapolis, Indiana: Wiley, 2016.

---