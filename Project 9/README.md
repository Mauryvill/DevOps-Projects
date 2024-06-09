# **IMPLEMENT WORDPRESS WEBSITE WITH LVM STORAGE MANAGEMENT**

## **Understanding 3 Tier Architecture**

### **Web Solution with WordPress**

As a DevOps engineer you will most probably encounter PHP-based solutions since, even in 2021, it is the dominant web programming language used by more websites than any other programming language. In this project we are tasked to prepare a storage infrastructure on two Linux servers and implement a basic web solution using WordPress.

WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

This project consists of two parts:

1. **Configure storage subsystem for Web and Database servers based on Linux OS**.

The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

2. **Install WordPress and connect it to a remote MySQL database server**.

This part of the project will solidify our skills of deploying Web and DB tiers of Web solution.

As a DevOps engineer, your deep understanding of core components of web solutions and ability to troubleshoot them will play essential role in further progress and development.


### **Three-tier Architecture**

Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

**Three-tier Architecture** is a client-server software architecture pattern that comprise of 3 separate layers.

![alt text](<Image/Screenshot 1.jpeg>)


1. **Presentation Layer (PL)**: This is the user interface such as the client server or browser on your laptop.
2. **Business Layer (BL)**: This is the backend program that implements business logic. Application or Webserver
3. **Data Access or Management Layer (DAL)**: This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server In this project, you will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.


Your 3-Tier Setup

1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (For Wordpress)
3. An EC2 Linux server as a database (DB) server

We shall use RedHat OS for this project


## **Implementing LVM on Linux servers (Web and Database servers)**

### **Step 1 — Prepare a Web Server**

1. Launch an EC2 instance that will serve as "Web Server".
Create 3 volumes in the same Availability Zone as your Web Server EC2, each of 10 GiB.

![alt text](<Image/Screenshot 2.jpg>)

![alt text](<Image/Screenshot 3.png>)

![alt text](<Image/Screenshot 4.jpg>)

2. Attach all three volumes one by one to your Web Server EC2 instance and Open up the Linux terminal to begin configuration.

![alt text](<Image/Screenshot 5.jpg>)

3. Use ```lsblk``` command to inspect what block devices are attached to the server. Notice names of your newly created devices.
All devices in Linux reside in ```/dev/``` directory.
Inspect it with ```ls /dev/``` and make sure you see all 3 newly created block devices there - their names will likely be *nvme1n1*, *nvme2n1*, *nvme3n1*.

![alt text](<Image/Screenshot 6.jpg>)

4. Use ```df -h``` command to see all mounts and free space on your server

![alt text](<Image/Screenshot 7.jpg>)

5. Install lvm2 package using ```sudo yum install lvm2```.
Run ```sudo lvmdiskscan``` command to check for available partitions. Note: Previously, in Ubuntu we used ```apt``` command to install packages, in RedHat/CentOS a different package manager is used, so we shall use ```yum``` command instead.

![alt text](<Image/Screenshot 8.jpg>)

6. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```console
sudo pvcreate /dev/nvme1n1
sudo pvcreate /dev/nvme2n1
sudo pvcreate /dev/nvme3n1
```

![alt text](<Image/Screenshot 9.jpg>)

7. Verify that your Physical volume has been created successfully by running ```sudo pvs```
8. Use ```vgcreate``` utility to add all 3 PVs to a volume group (VG). Name the VG "webdata-vg" by running:
```sudo vgcreate webdata-vg /dev/nvme1n1 /dev/nvme2n1 /dev/nvme3n1```


![alt text](<Image/Screenshot 10.jpg>)



9. Verify that your VG has been created successfully by running ```sudo vgs```
10. Use ```lvcreate``` utility to create 2 logical volumes. "apps-lv" (Use half of the PV size), and "logs-lv" Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

```console
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

11. Verify that your Logical Volume has been created successfully by running ```sudo lvs```

12. Verify the entire setup
```console
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

![alt text](<Image/Screenshot 11.jpg>)

![alt text](<Image/Screenshot 12.jpg>)

13. Use ```mkfs.ext4``` to format the logical volumes with ext4 filesystem
```console
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

![alt text](<Image/Screenshot 13.jpg>)

14. Create /var/www/html directory to store website files:

    ```sudo mkdir -p /var/www/html```
15. Create /home/recovery/logs to store backup of log data:

    ```sudo mkdir -p /home/recovery/logs```
16. Mount /var/www/html on apps-lv logical volume:

    ```sudo mount /dev/webdata-vg/apps-lv /var/www/html/```

![alt text](<Image/Screenshot 14.jpg>)


17. Use ```rsync``` utility to backup all the files in the log directory /var/log into /home/recovery/logs (Thisisrequired before mounting the file system):

```sudo rsync -av /var/log/. /home/recovery/logs/```
18. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 14 above is very important):

```sudo mount /dev/webdata-vg/logs-lv /var/log```
19. Restore log files back into /var/log directory:

```sudo rsync -av /home/recovery/logs/ /var/log```

![alt text](<Image/Screenshot 15.jpg>)

20. Update ```/etc/fstab``` file so that the mount configuration will persist after restart of the server.
The UUID of the device will be used to update the /etc/fstab file;

```sudo blkid```

s```udo vi /etc/fstab```

Update ```/etc/fstab``` in this format using your own UUID and rememeber to remove the leading and ending quotes.

![alt text](<Image/Screenshot 16.jpg>)

![alt text](<Image/Screenshot 17.jpg>)


21. Test the configuration and reload the daemon

```console
sudo mount -a
sudo systemctl daemon-reload
```
Verify your setup by running ```df -h```, output must look like this:

![alt text](<Image/Screenshot 18.jpg>)


## **Installing wordpress and configuring to use MySQL Database**

**Step 2 — Prepare the Database Server**

1. Launch a second RedHat EC2 instance that will have a role - 'DB Server'.

![alt text](<Image/Screenshot 19.jpg>)

2. Repeat the same steps as for the Web Server, but instead of apps-lv, create db-lv and mount it to /var/db directory instead of /var/www/html/.

![alt text](<Image/Screenshot 20.jpg>)

![alt text](<Image/Screenshot 21.jpg>)

![alt text](<Image/Screenshot 22.jpg>)


**Step 3 — Install Wordpress on your Web Server EC2**

1. Update the repository

```sudo yum -y update```

2. Install wget, Apache and it's dependencies

```sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json```

![alt text](<Image/Screenshot 23.jpg>)

3. Start Apache

```console
sudo systemctl enable httpd
sudo systemctl start httpd
```
4. Install PHP and it's dependencies

```console
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```
5. Restart Apache

```sudo systemctl restart httpd```

6. Download wordpress and copy wordpress to var/www/html

```console
mkdir wordpress
cd wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```

![alt text](<Image/Screenshot 24.jpg>)

![alt text](<Image/Screenshot 25.jpg>)


7. Configure SELinux Policies

```console
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
```

![alt text](<Image/Screenshot 26.jpg>)


***Step 4 — Install MySQL on your DB Server EC2***

1. Update the machine and install Mysql

```console
sudo yum update
sudo yum install mysql-server
```
2. Verify that the service is up and running by using ```sudo systemctl status mysqld```, if it is not running, restart the service and enable it so it will be running even after reboot:

```console
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

**Step 5 — Configure DB to work with WordPress**

1. login into MySQL ```sudo mysql```
2. Input the following MySQL commands:

```console
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

![alt text](<Image/Screenshot 27.jpg>)


**Step 6 — Configure WordPress to connect to remote database**.

*Note*: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server's IP address, so in the Inbound Rule configuration specify source as ```ip.address/32```

![alt text](<Image/Screenshot 28.jpg>)


1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysqlclient

```console
sudo yum install mysql
sudo mysql -u user -p -h <DB.Server.Private.IP-address>
```

![alt text](<Image/Screenshot 29.jpg>)

2. Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

![alt text](<Image/Screenshot 30.jpg>)

3. Change permissions and configuration so Apache could use WordPress:

```sudo vi /var/www/html/wordpress/wp-config.php```

![alt text](<Image/Screenshot 31.jpg>)


![alt text](<Image/Screenshot 32.jpg>)

4. Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation's IP)
5. Try to access from your browser the link to your WordPress http://<Web.Server.Public.IPAddress>/wordpress/

![alt text](<Image/Screenshot 33.jpg>)

6. Fill out your DB credentials:

![alt text](<Image/Screenshot 34.jpg>)

If you see this message - it means your WordPress has successfully connected to your remote MySQL database

![alt text](<Image/Screenshot 35.jpg>)

You have learned how to configure Linux storage susbystem and have also deployed a full-scale Web Solution using WordPress CMS and MySQL RDBMS!






