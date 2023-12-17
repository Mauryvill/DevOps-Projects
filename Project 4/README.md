# **WEB STACK IMPLEMENTATION (LEMP STACK)** 

LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)



## **Lemp Stack implementation**

### **Prerequisites**:
You need an AWS Account where you will create an Ubuntu 22.04LTS(HVM) server. While creating the server, ensure you create a Pem Key pair and download it as this will be used to connect to the EC2 instance. We will be using Git Bash as our shell following the below steps;

1. Launch Git Bash and run the below command to connect to the EC2 instance.

```console
ssh -i <Your-private-key.pem> ubuntu@<EC2-Public-IP-address>
```

![Alt text](<Images/Screenshot 1.png>)


### **Installing the Nginx Web Server**
Below are the steps to install Nginx Server;

1. Run the below commands to update the list of packages in the package manager and the second command is to run the nginx package installer.

```console
$ sudo apt update
$ sudo apt install nginx
```
![Alt text](<Images/Screenshot 2.png>)

![Alt text](<Images/Screenshot 3.png>)


2. Verify that nginx is running as a service in the OS by running the below command and if it is green and running, then you have just launched your first Web Server in the cloud.
```console
$ sudo systemctl status nginx
```

![Alt text](<Images/Screenshot 4.png>)


3. Open port 80 which is the default port that web servers use to access web pages on the internet. This should be done on the AWS console where the SSH port was enabled.

![Alt text](<Images/Screenshot 5.png>)

4. Run the below commands to test how the nginx server responds to curl with some payload after the HTTP port is configured running the below commands. The first command is trying to access the webserver via a DNS name while the second by IPs address(the Ip specified, corresponds to the DNS name 'localhost' and the process of converting the DNS name to Ip is called resolution).
```console
$ curl http://localhost:80
or
$ curl http://127.0.0.1:80
```
![Alt text](<Images/Screenshot 6.png>)


5. Once you get a positive response, you can try accessing from your browser via this address ```https://<insert your Public Ip address>:80``` . This is the page you should see and it is the same content as seen in the curl local host command ran just that on the web, the HTML formatting is represented well.

![Alt text](<Images/Screenshot 7.png>)


Another way to retrieve your Public Ip address without going to the AWS console is to run the below command.
```console
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
![Alt text](<Images/Screenshot 8.png>)

## **Installing MySQL**
The next step is to install a Database Management System inorder to store and manage the data of your site in a relational database and MySQL is the popular one used with PHP environments.
Follow the below steps to install MySQL:

1. Run the below command to install MySQL software and when prompted to confirm installation, type Y.
```console
sudo apt install mysql-server
```
![Alt text](<Images/Screenshot 9.png>)

2. Login to the MySQL console running and this will connect to the MySQL server as the administrative database User **root** which is inferred by teh use of **sudo** when running the command.
```console
sudo mysql
```

Output should be:

``` **Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.22-0ubuntu0.22.04.3 (Ubuntu)

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

3. Exit the MySQL shell with 
```console
mysql> exit
```
![Alt text](<Images/Screenshot 10.png>)


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



For the rest of questions press *Y* and hit *Enter* at each prompt.This will prompt you to change root password, remove some anonymous users and test database, disable remote root logins and load these new rules so that MySQL immediately respects the changes made.

![Alt text](<Images/Screenshot 11.png>)


5. When finished, test if login is successful running the below command. The -p flag will prompt for password used after changing the root user password. You can then log out with the command ```*mysql> exit*```

```console
sudo mysql -p
```


![Alt text](<Images/Screenshot 12.png>)


Note: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead. We’ll demonstrate how to do that in Step 6.



## **Installing PHP**
PHP is the content that will help process code to display dynamic content to the end user. Nginx requires an external program to handle PHP processing and act as a bridge bwtween PHP interpreter itself and the webserver. You will need to install ```php-fpm``` which stands for PHP fast CGI process manager and tell nginx to pass the PHP requests to this software for processing. There also need for ```php-mysql``` to allow PHP communicate with MySQL based databases. The Core PHP packages will be installed as dependencies.

Below are the steps to install PHP:

1. To install the 2 packages run the below command
```console
sudo apt install php-fpm php-mysql
```

![Alt text](<Images/Screenshot 13.png>)


### **Configuring Nginx to use PHP Processor**
When using Nginx, we can create server blocks to encapsulate configuration details and host more than one Domain on a single server. 

Ubuntu has a default server block and is configired to serve documents out of directory ```/var/www/html```. to have multiple sites, we will leave hmtl dorectory and just creates directories on ```/var/www/``` for the domain website.

1. Create root web directory for your project domain named```projectlemp``` runing the below command
```console
$ sudo mkdir /var/www/projectlemp
```
![Alt text](<Images/Screenshot 14.png>)

2. Assign ownership of the directory with the ```&USER``` environment variable, which will reference the current system user.
```console
$ sudo chown -R $USER:$USER /var/www/projectlemp
```
![Alt text](<Images/Screenshot 15.png>)


3. Create and open a new configuration file in Apache's ```sites-available``` directory using any command line editor.
```console
$ sudo nano /etc/nginx/sites-available/projectlamp.conf
```
![Alt text](<Images/Screenshot 16.png>)

4. Paste the below bare-bones configuration on the blank file.

```console
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

```

![Alt text](<Images/Screenshot 17.png>)

When you are done editing, save and close the file using CTRL + X and then Y and ENTER to confirm.

6. Activate cobfiguration by linking the config file from Nginx's ```sites-enabled``` directory

```console
$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
![Alt text](<Images/Screenshot 18.png>)


**Note**: with this configuration, we are telling Nginx to use this configuration next time it is loaded. You can test the configuration for syntax errors by typing

```console
sudo nginx -t
```
output should be;

```nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
If you see any error, go back to the configuration file and review its content.

![Alt text](<Images/Screenshot 19.png>)

8. You might want to disable the default Nginx host that is currently configured to listen to port 80 for this run;

```console
sudo unlink /etc/nginx/sites-enabled/default
```
![Alt text](<Images/Screenshot 20.png>)

10. reload Nginx so the changes can take effect
```console
$ sudo systemctl reload nginx
```
![Alt text](<Images/Screenshot 21.png>)

11. Your new website is now active but **/var/www/projectlemp** is still empty. Create an index.html file in that location so that you can test the Virtual host works as expected.
```console
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
``` 
![Alt text](<Images/Screenshot 22.png>)

12. Access the website from the browser
```console
http://<Public-IP-Address>:80
```
You can also access Website on your browser by public DMS name.
```console
http://<Public-DNS-Name>:80
```

13. If you see the text from the **'echo'** command written to the html file, then the Nginx site is working well. You should see your server's public hostname(DNS name) and Public Ip address.

![Alt text](<Images/Screenshot 23.png>)

You can leave this file on your temporary landing page for your application until you set up an index.php file to replace it. 

You LEMP stack is now fully configured.


### **Testing PHP with Nginx**
1. Create a test PHP file in your document root. Open a new file called ```info.php``` within your document root in your text editor.
```console
nano /var/www/projectLEMP/info.php
```

2. Paste the below PHP script that returns an information about your server on the new file created.
```<?php
phpinfo();
```
![Alt text](<Images/Screenshot 24.png>)

3. You can now access this page on your web browser visiting the domain name or Public IP you set up on the configuration file followed by ```/info.php```

```console
http://`server_domain_or_IP`/info.php
```
![Alt text](<Images/Screenshot 25.png>)

4. Remove the file after testing as the infomation displayed is sensitive.
```console
$ sudo rm /var/www/your_domain/info.php
```
![Alt text](<Images/Screenshot 26.png>)


### **Retrieving Data from MySQL databse with PHP
1. Create a database named **example_database** and user named **example_user** and connect to mySQL using root account and adding the -p to request for password as access will be denied is you do not log in using password.
```console
sudo mysql -p
```
![Alt text](<Images/Screenshot 27.png>)

2. Create a new dabase running this command from your SQl console
```console
mysql> CREATE DATABASE `EXAMPLA_DATABASE`;
```
![Alt text](<Images/Screenshot 27_B.png>)

3. Create a new user and grant user full priviledges on the database you have just created. the below command creates the user **example_user** with password **PassWord.1**.

```console
CREATE USER 'example_user'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password.1';
```

![Alt text](<Images/Screenshot 28.png>)

4. Give the user permission over the database.
```console
mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
```
![Alt text](<Images/Screenshot 29.png>)

5. Now exit the SQL shell.
```console
mysql> exit
```
![Alt text](<Images/Screenshot 30.png>)

6. Test if user has proper permission. Glag -p will prompt for password.
```console
$ mysql -u example_user -p
```
![Alt text](<Images/Screenshot 31.png>)

7. Confirm you have access to database
```console
mysql> SHOW DATABASES;
```
![Alt text](<Images/Screenshot 32.png>)

8. Create a test table named *8todo_list**. 
```console
CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
```
![Alt text](<Images/Screenshot 33.png>)

9. Insert few rows of contant in the test table
```console
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
```
![Alt text](<Images/Screenshot 34.png>)

10. To confirm the data was successfully saved, run and exit with ***exit***.
```console
mysql>  SELECT * FROM example_database.todo_list;
```
![Alt text](<Images/Screenshot 35.png>)


11. Now create a PHP script to connect to my SQL and query your content. Create a new file in the custom web root directory 
```console
$ nano /var/www/projectLEMP/todo_list.php
```
![Alt text](<Images/Screenshot 36.png>)

12. Copy the below into the todolists.php file and save/close.

```
<?php
$user = "example_user";
$password = "PassWord.1";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

![Alt text](<Images/Screenshot 37.png>)


Re-access the Web page via 
```
http://<Public_domain_or_IP>/todo_list.php
```
![Alt text](<Images/Screenshot 38.png>)




Thank you.




