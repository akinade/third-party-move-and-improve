# Lab 200: High Availability for the OsCommerce instance 

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

### Step 2: Generate ssh key pair 
![](/Lab200/images/2.png "")

Alternatively, we can use: Method 2 - Create new keys

On each server run:

```ssh-keygen```

Hit enter. You'll have two files:

.ssh/id_rsa & .ssh/id_rsa.pub

On Server A, cat and copy to clipboard the public key:

```cat ~/.ssh/id_rsa.pub```

select and copy to your clipboard

ssh into Server B, and append the contents of that to the it's authorized_keys file:

```cat >> ~/.ssh/authorized_keys```

Paste your clipboard contents. Rsync is configured to use ssh by default**

### Step 3: Replace web server files 

Our web server files are located at /var/www/html. Go to server 2 and perform:

```sudo rm -rf /var/www/html```

This will delete all the web server files from server 2. Now, go to /var/www folder and create an empty folder:

```sudo mkdir html```

Now give the following permissions to the folder: 

```sudo chmod 777 /var/www/html/```

![](/Lab200/images/3.png "")

Now, we will perform the rsync command from server 1 to server 2 to replicate the files. Run the following command on server 1 (primary).

```rsync -r /var/www/html/ oscommerce@129.146.108.71:/var/www/html/```

If you go to server 2, you can see the following files in the /var/www/html directory

![](/Lab200/images/4.png "")

Now that we have successfully replicated the web server files, we can replicate the mysql files as well. We can do this in many ways:
* Using rsync as above
*	Using mysql dump utility
*	Using Golden Gate image in OCI (Learn more)

### Step 3: Replicate mysql files 

Run the following command on primary and backup server:

```sudo nano /etc/my.cnf```

OR

```sudo nano /etc/mysql/my.cnf```


Comment out the line which says 

```bind-address = 127.0.0.1```

![](/Lab200/images/5.png "")


Restart mysql

```sudo service mysql restart```

In order for our database in our backup server to communicate to the database in the primary server, do the following in the primary server- Go to mysql terminal by typing:
 
```mysql -u root -p```

On mysql terminal, run the following commands:

```CREATE USER 'root'@'ip_address' IDENTIFIED BY 'some_pass';```

```GRANT ALL PRIVILEGES ON *.* TO 'root'@'ip_address';```

**Note: ip_address is the ip address of the backup server**


This command should be run from the secondary server:

``` mysqldump --host=1.2.3.4 --user=MYDBUSER -pMYDBPASSWORD --add-drop-table --no-create-db --skip-lock-tables MYDBNAME | mysql --user=MYDBUSER -pMYDBPASSWORD MYDBNAME```

In my case, the command looked like this:
 
```mysqldump --host=150.136.116.169 -P 3306 --user=root -poscommerce --add-drop-table --no-create-db --skip-lock-tables oscommerce | mysql --user=root -poscommerce oscommerce```

Be sure to replace the values above with your actual primary server ip address and user/passwords. Once you’ve verified that this command works, simply run it as a cronjob as we have done with rsync or git. Run ‘crontab -e’, then add your mysqldump command:

```0 0 * * * mysqldump...```

![](/Lab200/images/6.png "")

If we go to the second server and check the mysqlData file, we will see the following:

![](/Lab200/images/7.png "")


Thus, we have the webserver files as well as the database files in a standby server safe and with latest updates. Furthermore, we can setup cron jobs for automation rather than running the commands manually every time.

## Part 2. configure DNS failover
Now that we have made sure the files in the 2 web servers are in sync, lets proceed and configure the failover from the Oracle Cloud console. There are multiple ways to setup a failover like using keepalived, using load balancers and using DNS Traffic Management Steering policies in OCI. For the purpose of this lab, we will use the DNS Traffic Management Steering Policy in OCI.

### Step 1:Login into both primary and secondary servers

ssh into both computes instances 

```ssh oscommerce@<public ip-add>```

Edit Apache config file. This step would make the oscommerce application accessible directly on the public ip address.

```sudo nano /etc/apache2/sites-available/000-default.conf```

Change from ```"DocumentRoot /var/www/html" ```to ```"DocumentRoot /var/www/html/catalog"```

Add ```“DirectoryIndex index.php”```

![](/Lab200/images/8.png "")

### Step 2:Export DNS zone file
**Prequisite**
* Register a domain with a domain name registerer e.g godaddy, goole-domain 
* I’m using google as my domain name provider 

**: DNS will take 4-12hours to propagate 

![](/Lab200/images/9.png "")


Export the resource record. This file would be exported as a .txt file. Store in a secure location, we would need the file later in the lab

![](/Lab200/images/10.png "")

![](/Lab200/images/11.png "")

### Step 2: Create Zone
Navigate back to OCI console to create a zone using the exported file 

![](/Lab200/images/12.png "")

Import file and create zone  
Import - Drag and drop, select, or paste a valid zone file into the Import Zone File window. The zone is imported as a primary zone. 

![](/Lab200/images/13.png "")

Navigate into the zone you just created 

![](/Lab200/images/14.png "")

To make your Oracle Cloud Infrastructure hosted zone accessible through the internet, you must delegate your domain with your domain's registrar. to do that-

Open the navigation menu. Under Core Infrastructure, go to Networking and click DNS Zone Management. Click the Zone Name for the zone you want to delegate. 

Use the Type sort filter to locate the NS records for your zone.

Note the name servers in the RDATA field within each NS record. You can use the noted name servers to change your domain's DNS delegation. Refer to your registrar's documentation for instructions.

![](/Lab200/images/15.png "")

Add name servers to your domain name server’s management console. I’m using google-domain in this case 

![](/Lab200/images/16.png "")


### Step 3: Create a failover traffic steering policy on OCI console

Traffic Management Steering Policies provides automated failover between primary and secondary servers.

![](/Lab200/images/17.png "")

![](/Lab200/images/18.png "")

There are other options here so make sure to select “failover”

![](/Lab200/images/19.png "")

Configure the answer pools by filling in the name and public ip address of the OsCommerce compute instance. In my case I have a pool of 2 compute instances 

**Complete the above step for both Master and Backup node**

![](/Lab200/images/20.png "")

![](/Lab200/images/21.png "")

Set the priority of the pools you created in the previous step
Pool Priority: Failover priority rules specify the priority of answers that are served in a policy. If the primary answer is unavailable, traffic is steered to the next answer in the list.

![](/Lab200/images/22.png "")

Create Health Check. 
In the Create Health Check dialog box, enter the following:
- Health Check Name: The name used for the health check. Avoid entering confidential information.
- Interval: Select the period of time between health checks of the target.
- Protocol: The network protocol used to interact with your endpoint, such as HTTP protocol, which initializes an HTTP handshake with your endpoint.
- Port: The port for the monitor to look for a connection. The default is port 80 for HTTP. For HTTPS, use port 443.
- Path: The specific path on the target to be monitored.
- Method: Select the HTTP method used for the health check.
- Timeout: Select the maximum time to wait for a reply before marking the health check as failed.

![](/Lab200/images/23.png "")

After the failover policy had been created, we need to attached domain name to the policy 

![](/Lab200/images/24.png "")

With a  web browser, access the oscommerce application using the domain name

Test the failover mechanism by stopping apache2 service on the mater node

Login into the master node and stop apache service 

```sudo service apache2 stop```


 check the failover policy overview, the master node’s heath-check status should have changed from “healthy” to “unhealthy “

![](/Lab200/images/25.png "")


