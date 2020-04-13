# Lab 200: High Availability for the OsCommerce instance by leveraging standby instance

## Introduction
This lab walks you through how you can setup DR easily on Oracle cloud leveraging different availability domains or across regions. One of the key principles of designing high availability solutions is to avoid single point of failure.
 We will deploy Compute instances that perform the same tasks in multiple availability domains. This design removes a single point of failure by introducing redundancy. The following diagram illustrates how we can achieve high availability. 

![](/Lab200/images/1.png "")


### Objectives
* Learn how to create secondary instance from custom image
* Learn how to replicate data across multiple compute instances using Rsync, MySQLdump
* Learn how to provision and configure DNS Failover with Traffic Management Steering policy 


### Required Artifacts
* DNS Zone master file

Estimated time to complete this lab is three hours.

### Additional Resources
* To learn about provisioning Networks and Network Security check out this [link](https://docs.cloud.oracle.com/en-us/iaas/Content/Network/Concepts/overview.htm)

## Part 1. Capturing a Snapshot of Third Party OS Commerce Application
