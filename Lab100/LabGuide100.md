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

Double check that your settings match the screenshot below. Once confirmed, click the import button. This process should take approximately 5 minutes.
![](/Lab100/images/3.png)

Once complete, you will see the osCommerceDemo virtual box listed. Hit the Green Start Arrow and you will be prompted with a login screen with username *oscommerce*. the Default password is *oscommerce*. Once logged in you may or may not be prompted to update to Ubuntu 16.04.6. Click on “Don’t Upgrade”. You have now successfully imported Ubuntu to VirtualBox that we will now use to initialize our osCommerce application.
![](/Lab100/images/4.png)

If you you would like to increase the desktop view of the VirtualBox, click on the Gear icon on the top right of the Ubuntu instance. When the System Settings window opens, click on Displays. From here click on your preferred resolution and click apply. The resolution will then change. Click on “Keep This Configuration” to save changes. This will give you more screen space in case the default 800x600 (4:3) resolution was too small.
![](/Lab100/images/5.png)
![](/Lab100/images/6.png)

### Step 2. Install and Setup LAMP (Linux, Apache, MySQL, PHP) & SSH
*Verify Internet Connection*
Before installing any of the packages on the Ubuntu image on VirtualBox, make sure that you are connected to the public internet. Shut down the virtual machine, then disable/turn off any VPN applications/programs, then start up the Ubuntu Virtual machine. This will allow the Ubuntu Virtual Machine to download and install Linux packages.

*Install MySQL5*
Run this terminal command to install MySQL
