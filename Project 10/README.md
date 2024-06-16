# **Devops Tooling Website Solution**


Embarking on a journey to build a comprehensive DevOps tooling website solution and exploring the integration of various tools and technologies to create a unified platform that enhances collaboration, automation, and efficiency for software development and operations teams.

In previous Projects (Project 6), we implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. Moving further we will add some more value to our solutions that your DevOps team could utilize. We want to introduce a set of DevOps tools that will help our team in day to day activities in managing, developing, testing, deploying and monitoring different projects.

The tools we want our team to be able to use are well known and widely used by multiple DevOps teams, so we will introduce a single DevOps Tooling Solution that will consist of:

1. **Jenkins** - free and open source automation server used to build CI/CD pipelines.
2. **Kubernetes** - an open-source container-orchestration system for automating computer application deployment, scaling, and management.
3. **Jfrog Artifactory** - Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.
4. **Rancher** - an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.
5. **Grafana** - a multi-platform open source analytics and interactive visualization web application.
6. **Prometheus** - An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
7. **Kibana** - Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.



## **Setup and technologies**

As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the coorporate infrastructure easily accessible. In this project you will implement a solution that consists of following components as deployed in Project 7:

- **Infrastructure**: AWS
- **Webserver Linux**: Red Hat Enterprise Linux 8
- **Database Server**: Ubuntu 20.04 + MySQL
- **Storage Server**: Red Hat Enterprise Linux 8 + NFS Server
- **Programming Language**: PHP
- **Code Repository**: GitHub

**Note**: for Rhel 8 server use this ami: ```RHEL-8.6.0_HVM-20220503-x86_64-2-Hourly2-
GP2 (ami-035c5dc086849b5de)```

![alt text](<Images/Screenshot 1.jpg>)



On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware - for Web Servers it look like a local file system from where they can serve the same files.


![alt text](<Images/Screenshot 2.png>)



It is important to know what storage solution or system is suitable for your solution by answering he below questions:
- what data will be stored.
- In what format, how this data will be accessed.
- By whom, from where, how frequently, etc. 



## **Implementing a business website using NFS for the backend file storage**

- Step 1 - **Prepare NFS Server**

1. Spin up a new EC2 instance with RHEL Linux 8 Operating System.


![alt text](<Images/Screenshot 1.jpg>)

2. Configure LVM on the Server.

- Instead of formating the disks as ext4 you will have to format them as xfs


![alt text](<Images/Screenshot 3a.png>)

![alt text](<Images/Screenshot 3b.png>)

![alt text](<Images/Screenshot 3c.png>)

![alt text](<Images/Screenshot 3d.png>)

- Ensure there are 3 Logical Volumes. lv-opt, lv-apps, and lv-logs.

![alt text](<Images/Screenshot 3e.png>)


![alt text](<Images/Screenshot 3f.png>)


![alt text](<Images/Screenshot 3g.png>)

- Create mount points on ```/mnt``` directory for the logical volumes as follow: 
- a. Mount ```lv-apps``` on ```/mnt/apps``` - To be used by webservers.
- b. Mount ```lv-logs``` on ```/mnt/logs``` - To be used by webserver logs.
- c. Mount ```lv-opt``` on ```/mnt/opt``` - To be used by Jenkins server later.


![alt text](<Images/Screenshot 3h.png>)

![alt text](<Images/Screenshot 3i.png>)



![alt text](<Images/Screenshot 3j.png>)



3. Install NFS server, configure it to start on reboot and make sure it is up and running

```console
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

![alt text](<Images/Screenshot 4a.png>)


![alt text](<Images/Screenshot 4b.png>)


4. Export the mounts for webservers' ```subnet cidr``` to connect as clients. For simplicity, you will install all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security. To check your ```subnet cidr``` - open your EC2 details in AWS web console and locate 'Networking' tab and open a Subnet link:



![alt text](<Images/Screenshot 5.png>)


- Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:

```console
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
```

![alt text](<Images/Screenshot 6.png>)


5. Configure access to NFS for clients within the same subnet (example of Subnet CIDR - ```172.31.32.0/20```):
```sudo vi /etc/exports```

```console
/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

Esc + :wq!

sudo exportfs -arv
```

![alt text](<Images/Screenshot 7.png>)

![alt text](<Images/Screenshot 7b.png>)


6. Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)
```rpcinfo -p | grep nfs```


![alt text](<Images/Screenshot 8.png>)


*Note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049*


![alt text](<Images/Screenshot 9.png>)




## **Configure Backend Database as part of 3-Tier Architecture**

- **Step 2 — Configure the database server**
By now we know how to install and configure a MySQL DBMS to work with remote Web Server:

1. Install MySQL server
2. Create a database and name it ```tooling```
3. Create a database user and name it ```webaccess```
4. Grant permission to webaccess user on tooling database to do anything only from the webservers ```subnet cidr```


![alt text](<Images/Screenshot 10.png>)



- **Step 3 — Prepare the Web Servers**

We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case - NFS Server and MySQL database.

We already know that one DB can be accessed for reads and writes by multiple clients.

For storing shared files that our Web Servers will use - we will utilize NFS and mount previously created Logical Volume ```lv-apps``` to the folder where Apache stores files to be served to the users ```/var/www```.

This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and the integrity of the data (in the database and on NFS) will be preserved.

During the next steps we will do following:

- Configure NFS client (this step must be done on all three servers)
- Deploy a Tooling application to our Web Servers into a shared NFS folder
- Configure the Web Servers to work with a single MySQL database.


1. Launch a new EC2 instance with RHEL 8 Operating System.
2. Install NFS client

    ```sudo yum install nfs-utils nfs4-acl-tools -y```

3. Mount ```/var/www/``` and target the NFS server's export for apps.

```console
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```


![alt text](<Images/Screenshot 11.png>)


![alt text](<Images/Screenshot 12.png>)



4. Verify that NFS was mounted successfully by running ```df -h```.

    Make sure that the changes will persist on Web Server after reboot:
```sudo vi /etc/fstab```

    add following line

```console
NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```


![alt text](<Images/Screenshot 13.png>)


5. Install Remi's repository, Apache and PHP

```console
sudo yum install httpd -y

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo dnf module reset php

sudo dnf module enable php:remi-7.4

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

setsebool -P httpd_execmem 1
```


![alt text](<Images/Screenshot 14.png>)


**Note**: *Repeat steps 1-5 for 2 more Web Servers*.

6. Verify that Apache files and directories are available on the Web Server in ```/var/www``` and also on the NFS server in ```/mnt/apps```.
If you see the same files - it means NFS is mounted correctly. You can try to create a new file ```touch test.txt``` from one server and check if the same file is accessible from other Web Servers.


![alt text](<Images/Screenshot 15a.png>)


![alt text](<Images/Screenshot 15b.png>)


![alt text](<Images/Screenshot 15c.png>)



7. Locate the log folder for Apache on the Web Server and mount it to NFS server's export for logs. Repeat step *No.4* to make sure the mount point will persist after reboot.


![alt text](<Images/Screenshot 16a.png>)


![alt text](<Images/Screenshot 16b.png>)



8. Fork the tooling source code from Darey.io Github Account to your Github account.

![alt text](<Images/Screenshot 17.jpg>)


9. Deploy the tooling website's code to the Webserver. Ensure that the html folder from the repository is deployed to ```/var/www/html```

![alt text](<Images/Screenshot 18.png>)


*Note 1*: Do not forget to open TCP port 80 on the Web Server.

*Note 2*: If you encounter 403 Error - check permissions to your ```/var/www/html``` folder and also disable SELinux ```sudo setenforce 0```
To make this change permanent - open following config file

```console
sudo vi /etc/sysconfig/selinux 
```

and set SELINUX=disabled then restart httpd.


![alt text](<Images/Screenshot 19.png>)


![alt text](<Images/Screenshot 19b.png>)


10. Update the website's configuration to connect to the database (in ```/var/www/html/functions.php file```).

Apply ```tooling-db.sql``` script to your database using this command ```mysql -h <databse-private-ip> -u <dbusername> -p <db-database-name> < tooling-db.sql```


![alt text](<Images/Screenshot 20.png>)


![alt text](<Images/Screenshot 21.png>)



11. Create in MySQL a new admin user with username: and password:
```console
INSERT INTO 'users' (`id`, `username`, `password`, `email`, `user_type`, `status`) VALUES -> (1, 'myuser', '5f4dcc3b5aa765d61d8327deb882cf99', 'user@mail.com', 'admin', '1');
```

![alt text](<Images/Screenshot 22.jpg>)


12. Open the website in your browser ```http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/```index.php and make sure you can login into the website with myuser user.


![alt text](<Images/Screenshot 23.png>)


![alt text](<Images/Screenshot 24.png>)


Successfully implemented a web solution for a DevOps team using LAMP stack with remote Database and NFS servers.


Thank you.