# AWS LEMP STACK IMPLENTATION
## Setting up LEMP Stack on Ubuntu
Lemp Stack Overview
You may come across terminologies like MEAN, MERN, LEMP, and LAMP when surfing the internet. These are web stacks, which are sets of tools, frameworks, and libraries used to create full-stack online applications. A database, server-side and client-side technologies, a web server, and a specific operating system are common components of a stack. LEMP is an open-source web application stack for developing web applications. It's an acronym for


* L = Linux Operating System.
* E - Nginx (pronounced engine-x, thus the E in the name), a web server.
* M = MySQL database.
* P = PHP is the programming language. When a web browser requests a web page, the web server handles the request and passes it to server-side technologies such as a server-side scripting language like PHP to connect with the server and database.

## Step 1
### INSTALLING THE NGINX WEB SERVER
In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.
First update a list of packages in package manager
``` 
sudo apt update -y
```
Install the Nginx package
```
sudo apt install nginx
```
Start the Nginx service
```
sudo systemctl start nginx
```
To verify that nginx was successfully installed and is running as a service in Ubuntu, run:
```
sudo systemctl status nginx
```
![Screenshot (55)](https://user-images.githubusercontent.com/111396874/205458022-4971eda1-5f55-4154-947a-c9eff5e1d51a.png)
Open a web browser of your choice and try to access following url
```
http://<Public-IP-Address>:80
```
The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.
If you see following page, then your web server is now correctly installed and accessible through your firewall.![Screenshot (56)](https://user-images.githubusercontent.com/111396874/205458096-d824cc22-bbbb-4262-9c91-5973f4f39f7b.png)

## Step 2
### INSTALLING MYSQL
Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

Again, use ‘apt’ to acquire and install this software:
```
sudo apt install mysql-server 
```
When the installation is finished, log in to the MySQL console by typing:
```
sudo mysql
```
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:
```
Welcome to the MySQL monitor.  Commands end with; or \g.
Your MySQL connection id is 11
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)
 
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
 
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 
mysql>
```
Before running the script, you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as **PassWord.1**. You can set the password by running this command:
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
Validate password plugin by running the interactive script:
```
sudo mysql_secure_installation
```
When you’re finished, test if you’re able to log in to the MySQL console by typing:
```
sudo mysql -p
```
![Screenshot (58)](https://user-images.githubusercontent.com/111396874/205458173-fe533061-1362-4cee-bdd9-23e3ef5e2547.png)

## Step 3
### 4. INSTALLING PHP
You have Nginx installed to serve your content and MySQL installed to store and manage your data. Now you can install PHP to process code and generate dynamic content for the web server.

You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases.

In order to install these two together we will run:
```
sudo apt install php-fpm php-mysql
```
![Screenshot (59)](https://user-images.githubusercontent.com/111396874/205458417-5f09dbac-ce5c-4f83-b969-6f6236feb509.png)

## Step 4
### 5. CONFIGURING NGINX TO USE PHP PROCESSOR
When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use project LEMP as an example domain name.

Create the root web directory for your domain as follows:
```
sudo mkdir /var/www/projectLEMP
```
Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:
```
sudo chown -R $USER:$USER /var/www/projectLEMP
```
Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:
```
sudo nano /etc/nginx/sites-available/projectLEMP
```
This will create a new blank file. Paste in the following bare-bones configuration:
```
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
When you’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:
```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:
```
sudo nginx -t
```
You shall see following message:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
![Screenshot (60)](https://user-images.githubusercontent.com/111396874/205458472-461bf04b-23f1-4b1c-9b43-5dc715587a31.png)
When you are ready, reload Nginx to apply the changes:
```
sudo systemctl reload nginx
```
Your new website is now active, but the web root **/var/www/projectLEMP** is still empty. Create an **index.html** file in that location so that we can test that your new server block works as expected:
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
Now go to your browser and try to open your website URL using IP address:
```
http://<Public-IP-Address>:80
```
If you see the text from ‘echo’ command you wrote to **index.html** file, then it means your Nginx site is working as expected. In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP – try it out, the result must be the same (port is optional)
```
http://<Public-DNS-Name>:80
```
Your LEMP stack is now fully configured. In the next step, we’ll create a PHP script to test that Nginx is in fact able to handle .php files within your newly configured website.

## Step 5
### 6. TESTING PHP WITH NGINX
Your LEMP stack should now be completely set up.
At this point, your LEMP stack is completely installed and fully operational.
You can test it to validate that Nginx can correctly hand .php files off to your PHP processor.
You can do this by creating a test PHP file in your document root. Open a new file called **info.php** within your document root in your text editor:
```
sudo nano /var/www/projectLEMP/info.php
```
Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:
```
<?php
phpinfo();
```
You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:
```
http://`server_domain_or_IP`/info.php
```
![Screenshot (62)](https://user-images.githubusercontent.com/111396874/205458663-68fbc950-fa21-4090-b1e0-9eb2b03479c7.png)

## Step 6
### 7. RETRIEVING DATA FROM MYSQL DATABASE WITH PHP
In this step you will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

We will create a database named **example_database** and a user named **example_user**, but you can replace these names with different values.

First, connect to the MySQL console using the root account:
```
sudo mysql -p
```
To create a new database, run the following command from your MySQL console:
```
mysql> CREATE DATABASE `example_database`;
```
Now you can create a new user and grant him full privileges on the database you have just created.
We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.
```
mysql> CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```
Now we need to give this user permission over the **example_database** database:
```
mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
```
This will give the example_user user full privileges over the **example_database** database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:
```
mysql> exit
```
You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:
```
mysql -u example_user -p
```
Notice the -p flag in this command, which will prompt you for the password used when creating the **example_user** user. After logging in to the MySQL console, confirm that you have access to the **example_database** database:
```
mysql> SHOW DATABASES;
```
This will give you the following output:
```
Output
+--------------------+
| Database       	|
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
```
Next, we’ll create a test table named **todo_list**. From the MySQL console, run the following statement:
```
CREATE TABLE example_database.todo_list (
mysql> 	item_id INT AUTO_INCREMENT,
mysql> 	content VARCHAR(255),
mysql> 	PRIMARY KEY(item_id)
mysql> );
```
Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:
```
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
```
To confirm that the data was successfully saved to your table, run:
```
mysql> SELECT * FROM example_database.todo_list;
```
You’ll see the following output:
```
Output
+---------+--------------------------+
| item_id | content              	|
+---------+--------------------------+
|   	1 | My first important item  |
|   	2 | My second important item |
|   	3 | My third important item  |
|   	4 | and this one more thing  |
+---------+--------------------------+
4 rows in set (0.000 sec)
```
After confirming that you have valid data in your test table, you can exit the MySQL console:
```
mysql> exit
```
Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred 
editor. We’ll use vi for that:
```
nano /var/www/projectLEMP/todo_list.php
```
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

Copy this content into your **todo_list.php** script:
```
<?php
$user = "example_user";
$password = "password";
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
Save and close the file when you are done editing.
You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:
```
http://<Public_domain_or_IP>/todo_list.php
```
You should see a page like this, showing the content you’ve inserted in your test table:
![Screenshot (65)](https://user-images.githubusercontent.com/111396874/205458692-f34cea45-af05-4ea7-80b0-807e04b6dbc6.png)
That means your PHP environment is ready to connect and interact with your MySQL server.

**THANK YOU!**
