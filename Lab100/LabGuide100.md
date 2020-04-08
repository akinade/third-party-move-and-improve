# Lab 100: Lifting A Third Party Application to the Cloud

## Introduction
This lab walks you through the steps to capture an image of an existing, on-prem third party application. First you will capture a .ova file of the application and convert it to a cloud compatible .vmdk file. From there you will import the .vmdk to Object Storage and then create a cloud-native server that will use the file as a custom image.

### Objectives
* Learn how to create a cloud-compatible VMDK file
* Learn how to leverage Object Storage
* Learn how to provision cloud infrastructure (networking, routing, compute)
* Learn how to deploy cloud-native servers

### Required Artifacts
* The following lab requires an Oracle Public Cloud account. You may use your own cloud account, a cloud account that you obtained through a trial, or a training account whose details were given to you by an Oracle instructor.
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* Preinstalled Ubuntu Virtual Image (Will be provided to you during workshop from OraDocs repository)
* VNC Viewer

Estimated time to complete this lab is three hours.

### Additional Resources
* To learn about provisioning Networks and Network Security check out this [link](https://docs.cloud.oracle.com/en-us/iaas/Content/Network/Concepts/overview.htm)
* To learn about Object Storage in the cloud please see the following [link](https://docs.cloud.oracle.com/en-us/iaas/Content/Object/Concepts/objectstorageoverview.htm)

## Part 1. Capturing a Snapshot of Third Party OS Commerce Application
### Step 1: Download VirtualBox and Import Ubuntu Instance
If you do not have it on your local machine, make sure to download [VirtualBox](https://www.virtualbox.org/wiki/Downloads). VirtualBox is a free, open-source software that allows users to run multiple operating systems on a single machine and switch between OS Instances. Additionally, download the osCommerceDemo.ova file that will be provided to you by your lab facilitator.
![](/Lab100/images/1.png)
Open Virtualbox and click File > Import Applicance. Select the .ova file downloaded earlier.
![](/Lab100/images/2.png)
