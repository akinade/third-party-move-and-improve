# Lab 300: Integrate Oracle Cloud Platform services with your app

## Introduction
This lab walks you through the steps to integrate your third party app with Oracle Cloud PaaS. First, we will connect MySQL database and then Oracle Autonomous Data warehouse to Oracle Integration Cloud. After both the connections has been established, we will migrate the data from MySQL database to Oracle Autonomous Datawarehouse. Lastly, we will connect Oracle Analytics Cloud service to Oracle Autonomous Data warehouse to perform some analytics. 

### Objectives
* Establish connection from MySQL to Oracle Integration Cloud
* Establish connection from Oracle Autonomous Database and Oracle Integration Cloud
* Establish an integration between the ADW and MySQL connections
* Create an OAC Instance
* Access your new OAC Instance
* Connect an ADW/ATP Instance to OAC


### Required Artifacts
* The following lab requires an Oracle Public Cloud account. You may use your own cloud account, a cloud account that you obtained through a trial, or a training account whose details were given to you by an Oracle instructor.
* Complete Lab 100 and 200
* Access to Oracle Integration Cloud Service
* Access to Oracle Autonomous Data Warehouse
* Access to Oracle Analytics Cloud Service

Estimated time to complete this lab is two hours.

### Additional Resources
* To learn about Oracle Integration Cloud Service, check out this [link](https://docs.oracle.com/en/cloud/paas/integration-cloud/index.html)
* To learn about Autonomous Database, see the following [link](https://www.oracle.com/database/autonomous-database.html)

## Part 1. Establish connections in Oracle Integration Cloud

### Step 1: Establish connection from MySQL to Oracle Integration Cloud

Before moving forward, if you have been provided a wallet file, a username, and password to an ADW or ATP, please save this information and continue to the next step. Otherwise, read the step below:

If you have not been provided a username, password, and wallet file, please follow the lab guide here [link](https://github.com/oracle/learning-library/blob/master/workshops/erp-adw-oac/LabGuide100ProvisionAnADWDatabase.md). Once you complete making an ADW or ATP instance, please download the Wallet file from the console. 

Here, we will establish a connection between our MySQL Database and Oracle Integration Cloud. An Oracle Integration Cloud instance will be provided to you.
In the Oracle Integration Cloud home page, click on the Menu icon in the top left and click on Integration.

![](/Lab300/images/1.png "")



