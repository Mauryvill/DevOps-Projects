# **WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS** 

## **What is a Technology Stack?**
It is a set of frameworks and tools used to develop a software product. This tools and framework are specifically chosen to work together in creating a well-functioning software. Below are the acronymns for the individual technologies used;

- LAMP(Linux, Apache, MySQL, PHP or Python, or Perl)
- LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)
- MERN (MongoDB, ExpressJS, ReactJS, NodeJS)
- MEAN 9MongoDB, ExpressJS, AngularJS, NodeJS


## **Lamp Stack implementation**

### **Prerequisites**:
You need an AWS Account where you will creat an Ubuntu 20.04LTS(HVM) server. While creating the server, ensure you create a Pem Key pair and download it as this will be used to connect to the EC2 instance following the below steps;

1. Dowload your windows terminal via this link [Windows Terminal](https://apps.microsoft.com/detail/9N0DX20HK701?activetab=pivot%3Aoverviewtab&hl=en-us&gl=US) 
2. On the Terminal, type the command ```cd downloads``` to navigate to your download folder. Your downloaded Key pair will be seated in downloads.
3. When you are in the downloads folder, run the below command to connect to your EC2 instance successfully.

![Alt text](<Images/Screenshot 1.png>)


## **Installing Apache and Updating the Firewall**

Apache HTTP Server is the most widely used web server software developed by Apache Software foundation. It is an open source software available for free that runs on 67% of all webservers in the world. It is fast, reliable, secure and can be highly customized to meet the needs of different environments by using extensions and modules. Most Wordpress hosting providers use this software as well as other softwares like Nginx, Microsoft IIS etc.

Below are the steps to install Apache;

1. Run the below commands to update the list of packages in the package manager and the second command is to run the Apche2 package installer.

```console
$ sudo apt update
$ sudo apt install apache2
```
![Alt text](<Images/Screenshot 2.png>)
![Alt text](<Images/Screenshot 2_1.png>)



2. Verify that apache2 is running as a service in the OS by running the below command and if it is green and running, then you have just launched your first Web Server in the cloud.
```console
$ sudo systemctl status apache2
```
![Alt text](<Images/Screenshot 3.png>)



3. Open port 80 which is the default port that web servers use to access web pages on the internet. This should be done on the AWS console where the SSH port was enabled.

![Alt text](<Images/Screenshot 4.png>)

4. Run the below commands to test how the Apache server responds to curl with some payload after the HTTP port is configured running the below commands. The first command is trying to access the webserver via a DNS name while the second by IPs address(the Ip specified, corresponds to the DNS name 'localhost' and the process of converting the DNS name to Ip is called resolution).
```console
$ curl http://localhost:80
or
$ curl http://127.0.0.1:80
```
![Alt text](<Images/Screenshot 7.png>)



5. Once you get a positive response, you can try accessing from your browser via this address ```https://<insert your Public Ip address>:80``` . This is the page you should see and it is the same content as seen in the curl local host command ran just that on the web, the HTML formatting is represented well.

![Alt text](<Images/Screenshot 6.png>)


Another way to retrieve your Public Ip address without going to the AWS console is to run the below command.
```console
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
![Alt text](<Images/Screenshot 5.png>)


## **Installing MySQL**
The next step is to install a Database Management System inorder to store and manage the data of your site in a relational database and MySQL is the popular one used with PHP environments.
Follow the below steps to install MySQL:

1. Run the below command to install MySQL software and when prompted to confirm installation, type Y.
```console
sudo apt install mysql-server
```
![Alt text](<Images/Screenshot 8.png>)

2. Login to the MySQL console running and this will connect to the MySQL server as the administrative database User **root** which is inferred by teh use of **sudo** when running the command.
```console
sudo mysql
```

Output should be:

``` **Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>** 
```
**Note**: it is recommended that a security script that comes pre-installed with MySQL is ran  as this script will remove some insecure default settings and lock down access to your database system. before running the script, you will set a password for the root User, using mysql_native _password as default authentication method. We are defining this user's password here as ```Password.1```

```console
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
![Alt text](<Images/Screenshot 10.png>)

3. Exit the MySQL shell with 
```console
mysql> exit
```
![Alt text](<Images/Screenshot 14.png>)


4. Start the interactive script by running the below. 
```console
$ sudo mysql_secure_installation
```

Note that it will ask if you want to configure the Validate Password login(note that enabling this feature is a judgement call, and if enabled, passwords which do not match the specified criteria will be rejected by MySQL with an error), so it is safe to leave it as disabled but use strong and unique passwords.


``` 
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y for Yes, any other key for No:
```
After you answer yes, you will asked for select a level of password verification (selecting 2 means strongest level as you will get errors when password does not contain numbers, upper and lowercases letters, and special characters). 

```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary              file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
```

*Do note that this password asked is the MySQL root user as the frst password set is the database root User password*. You will be shown the password strength that you just entered if password validation is enabled and if you happy with the password enter *Y* for yes at the prompt. 

```
Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
```
![Alt text](<Images/Screenshot 11.png>)


For the rest of questions press *Y* and hit *Enter* at each prompt.This will prompt you to change root password, remove some anonymous users and test database, disable remote root logins and load these new rules so that MySQL immediately respects the changes made.

![Alt text](<Images/Screenshot 12.png>)

5. When finished, test if login is successful running the below command. The -p flag will prompt for password used after changing the root user password. You can then log out with the command ```*mysql> exit*```

```console
sudo mysql -p
```
![Alt text](<Images/Screenshot 13.png>)



## **Installing PHP**
PHP is the content that will help process code to display dynamic content to the end user. For the PHP package, the ```php-mysql``` module that allows PHP to cmummunicate with MySQL-based databases is needed. There also need for ```libapache2-mod-php``` to enable Apache handle PHP files. The Core PHP packages will be installed as dependencies.

Below are the steps to install PHP:

1. To install the 3 packages run the below command
```console
sudo apt install php libapache2-mod-php php-mysql
```

![Alt text](<Images/Screenshot 15.png>)


2. Once the installation is complete, run the below command to confirm PHP version.
```console
php -v
```
![Alt text](<Images/Screenshot 16.png>)

At this point the LAMP stack (Linux, Apache, MySQL and PHP) is completely installed and fully operational.


## **Enable PHP on Website**
To test the setup with a PHP script, it is best to set up the Apache Virtual Host properly to hold the website files and folders. The Virtual host allows one to host multiple websites on a single machine and users of websites will not even notice it.

1. With the default **DirectoryIndex**, settings on Apache, a file named ```index.html``` will always take precedence over an ```index.php``` file. This is useful for setting up maintenance pages in PHP application, by creating a temporary ```index.html``` file containing an informative message to visitors. Once maintenance is over, the ```index.html``` is renamed or removed from the document root, bringing back the regular application page. 

To change this, you will need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the Directoryindex directive.

```console
sudo vim /etc/apache2/mods-enabled/dir.conf
```
![Alt text](<Images/Screenshot 19.png>)


```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
![Alt text](<Images/Screenshot 17.png>)


2. After saving and closing the file, reload Apache so the changes can take effect running the below command.
```console
sudo systemctl reload apache2
```
![Alt text](<Images/Screenshot 19.png>)


3. We will create a PHP script to confirm that Apache is able to handle and process requests for PHP files. 

- Create a new file named ```index.php``` inside the custom web root folder.
```console
sudo vim /var/www/html/index.php
```
This will open a blank file, then add the below text which is valid PHP code inside the file. Once you are done, close the file and refresh the page to see a page that provides information about the server.
```
<?php
phpinfo();
```
![Alt text](<Images/Screenshot 27.png>)

![Alt text](<Images/Screenshot 21.png>)


- After confirmation, it is safe to remove the file as it contains sensitive information about your PHP environment and Ubuntu Server running the below command.

```console
sudo rm index.php
```
![Alt text](<Images/Screenshot 28.png>)


## **Creating a Virtual Host for your Website using Apache**
We will be setting up a domain called ```projectlamp``` . Apache on ubuntu 20.04 has a server block enabled by default that is configured to serve documents from the */var/www/html* directory and we will be leaving this confirguration as it is and create another directory next to the default one.

1. Create directory for ```projectlamp``` runing the below command
```console
$ sudo mkdir /var/www/projectlamp
```
![Alt text](<Images/Screenshot 29.png>)


2. Assign ownership of the directory with the ```&USER``` environment variable, which will reference the current system user.
```console
$ sudo chown -R $USER:$USER /var/www/projectlamp
```
![Alt text](<Images/Screenshot 31.png>)

3. Create and open a new configuration file in Apache's ```sites-available``` directory using any command line editor.
```console
$ sudo vi /etc/apache2/sites-available/projectlamp.conf
```
![Alt text](<Images/Screenshot 32.png>)


4. Paste the below bare-bones configuration by hitting on ```i``` on the keyboard to enter insert mode and paste the below. 
```console
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
![Alt text](<Images/Screenshot 25.png>)


5. To save and close, follow the below steps;
    
    - Hit the ```esc``` button on the Keyboard
    - Type ```:```
    - Type ```wq```. **w** for write and **q** for quit.
    - Hit ```ENTER``` to save file.

6. Use the ```ls``` command to show the new file in the **sites-available** directory.
```console
$ sudo ls /etc/apache2/sites-available
You will see something like this
000-default.conf  default-ssl.conf  projectlamp.conf
```
**Note**: with this configuration, we are telling Apache to serve ```projectlamp``` using **/var/www/projectlamp** as its root directory. If you want the PAche server to test without Domain name, /comment the options serverName and Server Alias by adding **#** in the beginning of each option's lines to tell the program to skip processing those lines.  

7. You can use ***a2ensite*** to enable the new virtual host.
```console
$ sudo a2ensite projectlamp
```
![Alt text](<Images/Screenshot 33.png>)

8. You might want to disable the default website that comes installed with apache running the below command.
```console
$ sudo a2dissite 000-default
```
![Alt text](<Images/Screenshot 34.png>)

9. To make sure your configuration does not containsyntax error, run.
```console
$ sudo apache2ctl configtest
```
![Alt text](<Images/Screenshot 35.png>)

10. reload Apache so the changes take effect
```console
$ sudo systemctl reload apache2
```
![Alt text](<Images/Screenshot 36.png>)


11. Your new website is now active but **/var/www/projectlamp** is still empty. Create an index.html file in that location so that you can test the Virtual host works as expected.
```console
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
``` 
![Alt text](<Images/Screenshot 37.png>)

12. Access the website from the browser
```console
http://<Public-IP-Address>:80
```
13. If you see the text from the **'echo'** command written to the html file, then the Virtual Host is working well. You should see your server's publicn hostname(DNS name) and Public Ip address. You can also access Website on your browser by public DMS name.
```console
http://<Public-DNS-Name>:80
```
![Alt text](<Images/Screenshot 26.png>)



Thank you.







