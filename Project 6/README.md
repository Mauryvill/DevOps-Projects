# **IMPLEMENTING A CLIENT SERVER ARCHITECTURE USING MySQL** 

# **Understanding Client-Server Architecture**

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another. In their communictaion, each machine has its own role: the machine sending requests is usually referred to as "**Client**" and the machine responding(serving) is called the "**Server**".

A simple diagram of a Web Client-Server architure is presented below:

![Alt text](Images/Client-server_Architecture_diagram.png)

Below is also a simple diagram adding a Database Server to the above architecture:

![Alt text](<Images/Client-server_Architecture_with DatabaseServer_diagram.png>)

In this case, the Web Server has a role of a "Client" that connects and reads/writes to/from a Database (DB) Server (MySQL, MongoDB, Oracle, SQL Server or any other), and the communication between them happens over a Local Network (it can also be Internet connection, but it is a common practice to place Web Server and DB Server close to each other in local network).

The setup on the diagram above is a typical generic Web Stack architecture that we have already implemented in previous projects (LAMP & LEMP), this architecture can be implemented with many other technologies - various Web and DB servers, from small Single-page applications SPA to large and complex portals.

Take for example, a commercially deployed LAMP website - [www.propitixhomes.com](<http://www.propitixhomes.com/>). This LAMP website server(s) can be located anywhere in the world and you can reach it also from any part of the globe over global network - Internet. Assuming that you go on your browser, and typed in there [www.propitixhomes.com](<http://www.propitixhomes.com/>). It means that your browser is considered the "Client". Essentially, it is sending request to the remote server, and in turn, would be expecting some kind of response from the remote server.

Lets take a very quick example and see Client-Server communicatation in action. Open up your Ubuntu or Windows terminal and run curl command:

```curl -lv www.propitixhomes.com```    Note: if your Ubuntu does not have 'curl', you can install it by running      ```sudo apt install curl```

In this example, your terminal will be the client, while [www.propitixhomes.com](<http://www.propitixhomes.com/>) will be the server. See the response from the remote server in below output. You can also see that the requests from the URL are being served by a computer with an IP address 104.18.144.154 on port 80 . More on IP addresses and ports when we get to Networking related projects.

![Alt text](<Images/Example image 3.png>)

Another simple way to get a server's IP address is to use a simple diagnostic tool like 'ping', it will also show round-trip time -time for packets to go to and back from the server, this tool uses [ICMP protocol](<https://en.wikipedia.org/wiki/Internet_Control_Message_Protocol>)


## **Implementing a Client-Server Architecture using MySQL**

To demonstrate a basic client-server using MySQL RDBMS, follow the below instructions:

1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).

```console
Server A name - mysql server
Server B name - mysql client
```

2. On mysql server Linux Server install MySQL Server software.

```console
Interesting fact: MySQL is an open-source relational database management system. Its name is a combination of "My",
the name of co-founder Michael Widenius daughter, and "SQL", the abbreviation for Structured Query Language.
```

3. On ```mysql client``` Linux Server install MySQL **Client** Software.
4. By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using **local IP addresses**.
Use ```mysql server's``` local IP address to connect from ```mysql client```. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in 'Inbound rules' in 'mysql server' Security Groups. For extra security, do not allow all IP addresses to reach your 'mysql server' - allow access only to the specific local IP address of your 'mysql client'.

![Alt text](<Images/Screenshot 1.png>)

5. You might need to configure MySQL server to allow connections from remote hosts.
```sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf```
Replace '127.0.0.1' to '0.0.0.0' like this:

![Alt text](<Images/Screenshot 2.png>)

6. From ```mysql client``` Linux Server connect remotely to ```mysql server```  Database Engine without using ```SSH```. You must use the ```mysql``` utility to perform this action.
7. Check that you have successfully connected to a remote MySQL server by running ```mysql -u (username) -p -h (local host/private ip)``` and can perform SQL queries:
Run the below command to show all databases.
```console
Show databases;
```
![Alt text](<Images/Screenshot 3.png>)


Thank you.