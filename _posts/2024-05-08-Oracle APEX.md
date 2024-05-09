---
layout: post
author: matheus
name_displayed: Matheus Kempa
image: oracle_theme.png
subtitle: A real-life APEX project utilizing Oracle Cloud.
---

#### Table of Contents

1. [Introduction](#introduction)
    - [Apex](#apex)
2. [Project](#project)



<br>

---

## Introduction


The entire industry is having extensive discussions about the future of coding. Many people believe that in the future, coding may no longer be essential ([Nvidia’s CEO On The Democratization Of Coding - Forbes](https://www.forbes.com/sites/timbajarin/2024/03/20/nvidias-ceo-on-the-democratization-of-coding/?sh=7bb77ad7a95a)). Although I do not fully agree with this perspective (at least for now), I genuinely believe that low-code applications have several benefits, especially for medium or small projects. I really believe they will gain much more prominence in the future.

Indeed, we developers need to recognize the value of low-code applications today and acknowledge that there are several advantages over coded platforms:

1. **Low Cost**
2. **Speed**
3. **Productivity**
4. **Accessibility**
5. **Consistency/Quality**
6. **Focus on Business**

That’s where APEX comes into the scene!

### Apex

As described in [Oracle Site](https://apex.oracle.com/pt-br/), Oracle APEX is a **low-code** enterprise application platform that enables you to create scalable, secure web and mobile applications that can be deployed anywhere.

Nowadays Apex is used for:

- Develop Responsive Web Apps
- Visualize and manage data
- Apply SQL and database capabilities

Talking a little about it's caracteristics. It's important to know that Apex does not require any client software, the App development IDE is a web browser, there is no code generation, all the app definitions are stored in databases as metadata, and all the data is processed in the database.

Oracle Apex Infraesctruture is a fully supported no-cost feature of Oracle Autonomous Database and all Oracle Database Distribuitions and Editions. On autonomous Database, Oracle Apex provides a pre-configured, fully managed and secured environmnent to both develop and deploy applications. And it's architecture is composed as described in the image bellow:

<img class="img-fluid" src="/./assets/images/apex_architecture.png" alt="Resume" style="width:800px;"/>

One point that is very good to know is that there is no complex pricing associated with the number of developers that are using Apex or the number of applications and it can be deployed wherever Oracle Database runs, be it on the cloud, dedicated region, third party, or on premises.

After clarifying all the summary aspects of Apex, we can proceed to create some real applications there and see its true value. 

## Project 

For this project, the objective is to understand all the functionalities of Oracle Apex. For that, I am going to complete the entire [Oracle learning path](https://mylearn.oracle.com/ou/course/oracle-apex-developer-professional/),and afterwards, I will create my own application. I won’t post the training path step-by-step, because the purpose is to highlight it's best features and the most important content there.

First of all, the creation of the Environment is necessary. Since I already have an Autonomous Database created, my Apex Environment was almost ready to start. But I still needed to create my own APEX workspace name, workspace user and workspace password. 

**SQL Workshop**

<img class="img-fluid" src="/./assets/images/apex_sql_workshop.png" alt="Apex SQL Workshop" style="width:800px;"/>

SQL Workshop, as the name suggests, is a place where you can perform many SQL operations on your database. You will be able to run scripts, create procedures, create packages, create views as if you were in Oracle SQL Developer. For those who like coding (like me), you will feel at home (*low code does not mean no code*). One thing I would like to emphasize is that even though you don’t really need to know coding itself, knowledge of databases and the principles of ETLs, and basic PL/SQL is still required. Otherwise, you might end up messing things up and not creating good content there.

*Observation:* I really enjoyed writting on *Quick SQL*, it sort of a new way of writting fast SQL statements. It works just like a code generator SQL, and in my opinion it's a very good PL/SQL engine. 

