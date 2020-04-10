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
![](/Lab100/images/1.png "")

Open Virtualbox and click File > Import Applicance. Select the .ova file downloaded earlier.
![](/Lab100/images/2.png "")

Double check that your settings match the screenshot below. Once confirmed, click the import button. This process should take approximately 5 minutes.
![](/Lab100/images/3.png "")

Once complete, you will see the osCommerceDemo virtual box listed. Hit the Green Start Arrow and you will be prompted with a login screen with username *oscommerce*. the Default password is *oscommerce*. Once logged in you may or may not be prompted to update to Ubuntu 16.04.6. Click on “Don’t Upgrade”. You have now successfully imported Ubuntu to VirtualBox that we will now use to initialize our osCommerce application.
![](/Lab100/images/4.png "")

If you you would like to increase the desktop view of the VirtualBox, click on the Gear icon on the top right of the Ubuntu instance. When the System Settings window opens, click on Displays. From here click on your preferred resolution and click apply. The resolution will then change. Click on “Keep This Configuration” to save changes. This will give you more screen space in case the default 800x600 (4:3) resolution was too small.
![](/Lab100/images/5.png "")
![](/Lab100/images/6.png "")

### Step 2. Install and Setup LAMP (Linux, Apache, MySQL, PHP) & SSH
**Verify Internet Connection**

Before installing any of the packages on the Ubuntu image on VirtualBox, make sure that you are connected to the public internet. Shut down the virtual machine, then disable/turn off any VPN applications/programs, then start up the Ubuntu Virtual machine. This will allow the Ubuntu Virtual Machine to download and install Linux packages.

**Install MySQL5**

Run this terminal command to install MySQL: ```sudo apt-get install mysql-server mysql-client```

You will be asked to provide a password for the MySQL “root” user – this password is valid for the user root@localhost as well as root@server1.example.com, so we don't have to specify a MySQL root password manually later on. NOTE: Write this password down. You will need it for multiple parts of the lab going forward.

Run this command to complete the installation: ```mysql_secure_installation```

After running the command you will also be asked to answer prompts within the terminal window. Enter the following below where the password is the one you just set:
```Enter current password for root (enter for none): Type Root Password
Change The Root Password? N
Remove Anonymous Users? Y
Disallow Root Login Remotely? Y
Remove test database and access to it? Y
Reload Privilege Tables now? Y
```

**Install Apache2**

Run this command within the terminal on the VirtualBox environment:
```
sudo apt-get install apache2
```
If you direct your browser to localhost you will see the Apache2 placeholder page.
![](/Lab100/images/7.png "")

**Install PHP5**

Install PHP5 and Apache PHP5 modules with the command below:
```
sudo apt-get install php5 libapache2-mod-php5 php-mysql
```
Restart Apache:
```
sudo service apache2 restart
```

**Configure osCommerce Database and User**

Log on to the database as an administrator using the terminal within the VirtualBox environment:
```
mysql -u root -p
```
Create a database named oscommerce:
```
CREATE DATABASE oscommerce;
```

create a database user named ```oscommerceuser ```. Replace ```type_password_here ``` with a password of your choice. In this example we used ```oscommerce ``` as our password for the sake of simplicity. Regardless of what you choose it is highly recommended that you **copy down your password** as you will need it for other portions of the lab. Please manually typing the following commands due to the formatting of apostraphes.
```
CREATE USER oscommerceuser@localhost IDENTIFIED BY `type_password_here`;
```
grant the user access to the database & flush privileges:
```
GRANT ALL PRIVILEGES ON oscommerce.* TO oscommerceuser@localhost;
FLUSH PRIVILEGES;
```
Exit with Control + C

**Configure Ubuntu for SSH Connections**

Setup SSH:
```
sudo apt-get install openssh-server sudo
apt-get install ssh
```
Install the below package to save persistent iptables updates. Select 'Yes' to save Firewall rules.
```
sudo apt-get install iptables-persistent
```
allow SSH Connection:
```
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```
Check your iptables is updated with SSH access rules & save the updated rules:
```
sudo iptables -L
sudo invoke-rc.d iptables-persistent save
```
![](/Lab100/images/8.png "")

### Step 3: OSCommerce Setup
**Download OSCommerce**
Make a temporary folder named “tmp” where you will download osCommerce to. Download the zip and extract:
```
mkdir tmp
cd /tmp/ && wget http://www.oscommerce.com/files/oscommerce-2.3.4.zip
unzip oscommerce-2.3.4.zip
```
Next, run the commands below to copy osCommerce content to the default root directory for Apache2. You can choose to customize Apache2 root directory, but for this post, we’re going to use the default location which is at /var/www/html

```
sudo cp -rf oscommerce-2.3.4/* /var/www/html/
```
Change permissions on the files and give apache2 ownership of the root directory. Restart Apache:
```
sudo chmod 777 /var/www/html/catalog/includes/configure.php
sudo chmod 777 /var/www/html/catalog/admin/includes/configure.php
sudo chown www-data:www-data -R /var/www/html/
sudo service apache2 start
```

Open firefox and navigate to localhost/catalog. You should see the setup wizard; all steps in this installation need to be completed before we can move on. Follow screenshots below to make sure settings are consistent.
![](/Lab100/images/9.png "")

Start the new installation and log in to the MySQL database using the credentials cerated earlier. If values differ from those in the screenshot, please make sure you enter them appropriately.
![](/Lab100/images/10.png "")

Set where the www address and web server root directory should be saved
![](/Lab100/images/11.png "")

Finally, set the OSCommerce online store settings info. We recommend that you make a note of the administrator username and password for later reference.
![](/Lab100/images/12.png "")

After installation, remove the installation directory to protect your site and change the permissions on sensitive files:
```
sudo rm -rf /var/www/html/catalog/install
sudo chmod 644 /var/www/html/catalog/includes/configure.php
sudo chmod 644 /var/www/html/catalog/admin/includes/configure.php
```

### Step 4: Configure osCommerce for End User Use
Type localhost/catalog/admin in the address bar of the firefox browser you had open earlier. You'll need to log in with the admin username and password that you entered when setting up your osCommerce Online Store Settings. After logging in you'll be redirected to a page that looks similar to the second photo below.
![](/Lab100/images/13.png "")
![](/Lab100/images/14.png "")
