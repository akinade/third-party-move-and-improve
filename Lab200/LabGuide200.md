# Lab 200: High Availability for the OsCommerce instance by leveraging standby instance

## Introduction
Below, we have a demo of how you can setup DR easily in cloud leveraging different availability domains or across regions. One of the key principles of designing high availability solutions is to avoid single point of failure.
 We will deploy Compute instances that perform the same tasks in multiple availability domains. This design removes a single point of failure by introducing redundancy. The following diagram illustrates how we can achieve high availability. 

![](/Lab200/images/1.png "")

### Objectives
* Learn how to replicate data across multiple compute instances using Rsync, MySQLdump
* Learn how to provision and configure DNS Failover with Traffic Management Steering policy 


### Required Artifacts
* 2 OsCommerce compute servers that both have rsync installed
* Make sure you have setup ssh access from server 1 to server 2 and vice-versa
* DNS Zone master file

Estimated time to complete this lab is three hours.

### Additional Resources
* To learn about provisioning Networks and Network Security check out this [link](https://docs.cloud.oracle.com/en-us/iaas/Content/Network/Concepts/overview.htm)

## Part 1. Installing rsync utility 
### Step 1: Download and installing rsync
Download rsync command on both the compute instances as follows:
```sudo apt-get install rsync```


**Configure osCommerce Database and User**

