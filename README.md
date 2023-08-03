Project 1 : WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

The goal of this project is to describe the step-by-step implementation of a Lamp Stack on AWS. LAMP is an acronym for Linux, Apache, MySQL, PHP, or Python. Its one of the common web stacks that is used for software development. 
Prerequisites
•	An Aws account to run an EC2 instance or a virtual server with Ubuntu server.
•	Basic Linux command line skills.
STEP 1: Establishing a LINUX Environment
a)	Provisioning an EC2 instance on AWS
•	Open a web browser and login to your AWS account as IAM user.
 
•	From EC2 dashboard, click on launch instance.
 
•	Give the instance a name of your choice.
 
•	Select Ubuntu from the Application and OS Images section and select a t2. micro from the instance type or anyone that is free tier eligible.
 
•	Select an existing key pair if you have one or create a new key pair on the fly. 
N. B if you are creating a new key pair, remember to save the downloaded private key (.pem file) in a safe place on your PC and do not share with anyone. I will be needed to gain access into the instance.
 
•	Under network settings, select an existing security group if you have one or create a new security group on the fly. 
 
Leave every other setting as default and click on launch instance, wait a few seconds as the new instance gets provisioned.






b)	Let us now connect to our EC2 instance via SSH.
•	Select the instance, you want to connect to and copy the public IP address.  
 
•	Open your preferred console on your computer (e.g., visual studio, mobaxterm, termius, git bash) and run the following command.
ssh -i <private key name>. pem ubuntu@<Public-IP-address>
 
Type yes when prompted.
 

Congratulations, you have successfully connected to your EC2 instance and completed the first step of the LAMP stack i.e., LINUX.

STEP 2: Installing Apache2 and Updating Firewall
a)	Installing Apache2
To install Apache, we will make use of Ubuntu’s package manager called ‘apt’
# run the following command to update the list of packages in package manager
$ sudo apt update
# run the following command to install apache2
$ sudo apt install apache2 -y
# run the following command to start apache2
$ sudo systemctl start apache2
# run the following command to enable apache2 (this will ensure that apache2 starts up automatically if the server reboots
$ sudo systemctl enable apache2
# run the following command to check that apache2 is running
$ sudo systemctl status apache2
 
b)	Updating Firewall
Now that apache2 webserver is installed and running on our machine, we need to configure the firewall of our server to allow web clients communicate to our server. To do this, we need to update the security group of our AWS instance by adding port 80 to the inbound rules and allow connection from anywhere. 
By default, we already have port 22 opened on the security group of our AWS instance to allow connection via SSH

# run the following command to access the server locally from our Ubuntu shell
$ curl http:// localhost:80 
OR
$ curl http:// 127.0.0.1:80
To confirm that our server can respond to requests from the internet, open your web browser and type in the following
$ http:// <Public-IP-address>:80
You should see the image below
 

STEP 3: Install MySQL
# run this command to install MySQL
$ sudo apt install mysql-server -y
# run this command to log into the MySQL console
$ sudo mysql 
 
# run this command to set a password for MySQL root user
 ALTER USER ‘root’@’localhost’ IDENTIFIED WITH mysql_native_password BY ‘password’;
# exit MySQL shell by typing exit
mysql> exit
# run this command to start the interactive security script that comes pre-installed with MySQL
$ sudo mysql_secure_installation
# run this command to log into MySQL console as the root user with the password that was set earlier. 
$ sudo mysql -p 
# exit MySQL shell by typing exit
mysql> exit
STEP 4: Install PHP
#run this command to install PHP and it’s required dependencies
$ sudo apt install php libapache2-mod-php php-mysql
# run this command to confirm the version of PHP installed
$ php -v 
 
STEP 5: Creating A VIRTUAL HOST FOR PROJECT LAMP USING APACHE
Apache has a server block that is pre-configured, with a document root from /var/www/html/ directory. However, we will create our own project directory (projectlamp) and a new server block with a different document root within Apache’s virtual host so that the two websites can be served on a single server.
# run this command to create a directory for project lamp
$ sudo mkdir /var/www	projectlamp
# run this command to change the ownership of the directory to the current system user
$ sudo chown -R $USER:$USER /var/www/projectlamp
# run the command below to create and open a new configuration file for projectlamp in Apache’s sites-available directory
$ sudo vi /etc/apache2/sites-available/projectlamp.conf
•	Press I on the keyboard to switch to edit mode, paste the command below into the blank page 

<VirtualHost *:80>
ServerName projectlamp
ServerAlias www.projectlamp
ServerAdmin webmaster@localhost
DocumentRoot /var/www/projectlamp
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

•	Press Esc key on the keyboard to switch to normal mode
•	Type :wq followed by the enter key to write, quit and save the file.
#run the following command to check the content of the sites- available directory
$ sudo ls /etc/apache2/sites-available
You should see the following files listed 
000-default.conf   default-ssl.conf   projectlamp.conf
# run this command to enable the new virtual host
$ sudo a2ensite projectlamp
# run this command to disable Apache’s default website
$ sudo a2dissite 000-default
# run this command to test that the configuration works well
$ sudo apache2ctl configtest
# run this command to reload Apache for the changes to take effect
$ sudo systemctl reload apache2
# run this command to add an index file content to our new site root directory
$ sudo echo 'Hello, LAMP, from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

•	Now open the website from a web browser using the public IP address of the EC2 instance at port 80
http://<Public-IP-Address>:80
 
STEP 6: ENABLE PHP ON THE WEB SERVER
By default, Apache gives precedence to an index.html file over an index.php file when serving a web client, To serve php files from our webserver, we need to change Apache’s default setting to give precedence to index.php files over index.html files.
# run the following command and change the order in which index.php and index.html are listed
$ sudo vim /etc/apache2/mods-enabled/dir.conf
# after saving it, run this command to reload Apache
$ sudo systemctl reload apache2
Now let’s create a php script to test that PHP is correctly configured and that our server can serve PHP files from the document root.
# run this command to create and open index.php file in our document root folder
$ sudo vim /var/www/projectlamp/index.php
•	Copy and paste the following text into it, then save and exit.
$ <?php
phpinfo();
•	Refresh the webserver and you should see the page below.
 


N.B remember to terminate your EC2 instance
 
