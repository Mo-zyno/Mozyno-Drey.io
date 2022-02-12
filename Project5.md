
## CLIENT-SERVER ARCHITECTURE WITH MYSQL
Understanding Client-Server Architecture
Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.

In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".

A simple diagram of Web Client-Server architecture is presented below:
![Screenshot 2022-02-12 010433 image ](https://user-images.githubusercontent.com/97651517/153699309-c353a53e-924f-49ce-b33f-d22a2730fc33.png)

In the example above, a machine that is trying to access a Web site using Web browser or simply ‘curl’ command is a client and it sends HTTP requests to a Web server (Apache, Nginx, IIS or any other) over the Internet.
If we extend this concept further and add a Database Server to our architecture, we can get this picture:

![Screenshot 2022-02-12 010826](https://user-images.githubusercontent.com/97651517/153699411-2563fcd2-9c24-4acc-b7e3-6dd99eeed5d4.png)

## IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).
TASK – Implement a Client Server Architecture using MySQL Database Management System (DBMS).
Create and configure two Linux-based virtual servers (EC2 instances in AWS).
and update both servers using 
"sudo apt update" and also 
"sudo apt upgrade" before installing any packages.

![Screenshot 2022-02-12 011115](https://user-images.githubusercontent.com/97651517/153699477-d63e3d95-f678-4633-9fb5-634f91e8d2d8.png)
On mysql server Linux Server install MySQL Server software.

"sudo apt install mysql-server -y"

![Screenshot 2022-02-11 003714](https://user-images.githubusercontent.com/97651517/153699557-f5352e41-2396-4af2-9e85-39fd31abb022.png)
On mysql client Linux Server install MySQL Client software.
"sudo apt install mysql-client server-y"

By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

![Screenshot 2022-02-12 012947](https://user-images.githubusercontent.com/97651517/153699983-7c371b3c-22c2-4684-9fff-961d8baad890.png)

Then change the IP CIDR block address from the Security Group to the IP address of your mysql client
check IP address using
"ip addr show"

![Screenshot 2022-02-11 004657 check ip addr of client server](https://user-images.githubusercontent.com/97651517/153700239-9451e67f-21d9-4521-b660-53ebc9ac84a0.png)

On the mysql-server you have to enable the service using this command:
"sudo systemctl enable mysql"
![Screenshot 2022-02-11 003803](https://user-images.githubusercontent.com/97651517/153699875-0d424451-b9f7-4954-8aee-30e5747f7d64.png)
Next: we have to create a user for the client-server and a database for the mysql-server
But before that, we have to run the security script for mysql-server which will help us prepare the server.
"sudo mysql_security_installation"

![Screenshot 2022-02-11  mysql secure installation](https://user-images.githubusercontent.com/97651517/153700469-ad43421a-21e1-4547-be3b-98f52d33f2e1.png)

Create a Password for the database, then answer yes for the rest until you are done.
Next step;
run this command "sudo mysql"

![Screenshot 2022-02-11 005434 sudo mysql command](https://user-images.githubusercontent.com/97651517/153700569-a06fd0af-7d29-46ef-9f0a-6cfc34f55404.png)

Next step is to create the user called remote_user to be identified with mysql_native_password BY 'password'
Create a database name test_db
Grant all on test_db to remote_user
Flush Privileges
Finally exit 
see image below:





