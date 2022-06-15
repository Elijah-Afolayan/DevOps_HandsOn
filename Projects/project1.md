
## WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

### What is SDLC

Software Development Life Cycle (SDLC) is a process used by the software industry to design, develop and test high quality softwares. The SDLC aims to produce a high-quality software that meets or exceeds customer expectations, reaches completion within times and cost estimates.  

A typical software Develpment Life-cycle consist of the following stages;  

-Planning  
-Defining  
-Designing  
-Building or Develompent  
-Testing  
-Deployment 

![alt text](image/SDLC-chat.png) 

Now what is 'chmod' and Chown?  

The command chown, an abbreviation of change owner, is used on Unix and Unix-like operating systems to change the owner of file system files, directories. Unprivileged (regular) users who wish to change the group membership of a file that they own may use chgrp. The ownership of any file in the system may only be altered by a super-user. A user cannot give away ownership of a file, even when the user owns it. Similarly, only a member of a group can change a file's group ID to that group.

Looking up the CHMOD command ; In Unix and Unix-like operating systems, chmod is the command and system call which is used to change the access permissions of file system objects (files and directories). It is also used to change special mode flags. The request is filtered by the umask. The name is an abbreviation of change mode.

## STEP 0
login and crate an Ec2 instance in AWS currently running with SSH access

![final screen of ceated serverin AWS](image/EC2_server.png)  

Step 1 To connect to the created Ec2 instance via ssh. change directory to the directory where i stored my private key download folder

run 'cd Downloads'
![](image/change_directory.png)

Change the permision of .PEM file 700 by removing accesss for other users. the can be down in uni by 'chmod 700 <'pem file location'>. As for Windows you need to got to the file location and right click on the file and click *Properties* then *Security tab* then *Advance* on the popup window selcet the *Promerties tab* then Disable inheritance as per below: ![](image/convert_inherited_permission.png) .   After which you can now *Remove* other user leaving only Admin and urself ![](image/changing_permission.png)



Then to Connect to the instance by running the command on your terminal <'ssh -i Project1.pem ubuntu@54.165.241.149'> ![](image/SSH_connection_intoEc2.png)

### STEP 1
Update a list of packages in package manager using the code below:  
*sudo apt update*  

![](image/udate_instance.png)

Next run apache2 package installation with the code below  

*sudo apt install apache2*  

![](image/installing_apache1.png)

Accept the prompt with typing "Y" and hit Enter key

![](image/installing_apache2.png)

To verify that apache2 is running as a Service in our OS, use following command

*sudo systemctl status apache2*

![](image/Test_apache2_status.png)

 Adding a rule to EC2 configuration to open inbound connection through port 80 for HTTP trafic our EC2

 ![](image/adding_Http_trafic.png)  
 Here is the show that Port 80 has been enabled on AWS console.
 ![](image/Port_80_enabled.png) 

 Accessing it locally in the Ubuntu shell, run:
 *curl http://localhost:80
or
 curl http://127.0.0.1:80*

 ![](image/acess_locally.png)

 How our Apache HTTP server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url

*http://Public-IP-Address:80*

![](image/access_server_via_browser.png)

## STEP 2  
INSTALLING MYSQL
use ‘apt’ to acquire and install this software:

$ sudo apt install mysql-server

![](image/installing_db1.png)  

![](image/installing_db2.png)

When the installation is finished, log in to the MySQL console by typing: 
$ sudo mysql
![](image/login_mysql.png)

 Set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as *PassWord.1* using the command:

 *ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';*
![](image/Set_Rootuser_pw.png)


 Exit the MySQL shell with typing: *exit*


 Start the interactive script by running:

*sudo mysql_secure_installation*
![](image/dbSecurity_conf1.png)  

![](image/dbsecurity_conf2.png)

Test the new login settings with the command :

*sudo mysql -p*

![](image/testing_New_credential.png)

Then exit the prompt
![](image/exit_mysql.png)

STEP 3 — INSTALLING PHP
To integrate between the webserver, datapase and programming language(PHP) you need to insall libapache2-mod-php, php-mysql and PHP. Install these 3 packages at once, run:

sudo apt install php libapache2-mod-php php-mysql

libapache2-mod-php![](image/libapache2.png)

php-mysql![](image/Php-Mysql_install.png)

php installed![](image/)

STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

First create a new  directory called  *projectlamp* using ‘mkdir’ command  in the location *Var/www/* as follows:

sudo mkdir /var/www/projectlamp
Then change the owner of the file to give you the right to edit the folder with 

sudo chown -R $user:$user /var/www/projectlamp
![](image/SSH_connection_intoEc2.png)

Create and open a new configuration file in Apache’s *sites-available* directory called *projectlamp.conf* using your preferred command-line editor

![](image/writing_conf_file.png)

using command *a2ensite* to enable the new virtual host: projectlamp and a2dissite to disable default apache site 000-default  

![](image/enable%20a2.png)

![](image/disable_default_a2.png)

Verifying that the configuration file doesn’t contain syntax errors run command *sudo apache2ctl configtest*  
![](image/reloadsite.png)

web root /var/www/projectlamp is still empty hen create a new index.html to service the virtual host.
![](image/create_index.png)

![](image/weboutput.png)

![](image/dnsout_put.png)  

## STEP 5 — ENABLE PHP ON THE WEBSITE  

 Edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:
 ![](image/edited_dirconf.png)

 Create a new file named index.php inside your custom web root folder: 
 ![](image/index_php%20crate.png)



