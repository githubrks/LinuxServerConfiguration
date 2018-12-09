# LinuxServerConfiguration

This is the fifth project for "Full Stack Web Developer Nanodegree" on Udacity.

In this project, a Linux virtual machine needs to be configurated to support the Item Catalog website.

IP ADDRESS: 18.216.71.137 HTTP ADDRESS: http://ec2-18-216-71-137.compute-1.amazonaws.com/

Tasks
Launch your Virtual Machine with your Udacity account
Follow the instructions provided to SSH into your server
Create a new user named grader
Give the grader the permission to sudo
Update all currently installed packages
Change the SSH port from 22 to 2200
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
Configure the local timezone to UTC
Install and configure Apache to serve a Python mod_wsgi application
Install and configure PostgreSQL:
Do not allow remote connections
Create a new user named catalog that has limited permissions to your catalog application database
Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!
Launch Virtual Machine
Instructions for SSH access to the instance
Download Private Key below

Move the private key file into the folder ~/.ssh (where ~ is your environment's home directory). So if you downloaded the file to the Downloads folder, just execute the following command in your terminal. mv ~/Downloads/udacity_key.rsa ~/.ssh/

Open your terminal and type in chmod 600 ~/.ssh/udacity_key.rsa

In your terminal, type in ssh -i ~/.ssh/udacity_key.rsa root@52.24.125.52

Development Environment Information

Public IP Address

52.24.125.52

Private Key ( is not provided here. )

Create a new user named grader
sudo adduser grader
vim /etc/sudoers
touch /etc/sudoers.d/grader
vim /etc/sudoers.d/grader, type in grader ALL=(ALL:ALL) ALL, save and quit
Set ssh login using keys
generate keys on local machine usingssh-keygen ; then save the private key in ~/.ssh on local machine

deploy public key on developement enviroment

On you virtual machine:

$ su - grader
$ mkdir .ssh
$ touch .ssh/authorized_keys
$ vim .ssh/authorized_keys
Copy the public key generated on your local machine to this file and save

$ chmod 700 .ssh
$ chmod 644 .ssh/authorized_keys
reload SSH using service ssh restart

now you can use ssh to login with the new user you created

ssh -i [privateKeyFilename] grader@52.24.125.52

Update all currently installed packages
sudo apt-get update
sudo apt-get upgrade
Change the SSH port from 22 to 2200
Use sudo vim /etc/ssh/sshd_config and then change Port 22 to Port 2200 , save & quit.
Reload SSH using sudo service ssh restart
Configure the Uncomplicated Firewall (UFW)
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable 
Configure the local timezone to UTC
Configure the time zone sudo dpkg-reconfigure tzdata
It is already set to UTC.
Install and configure Apache to serve a Python mod_wsgi application
Install Apache sudo apt-get install apache2
Install mod_wsgi sudo apt-get install python-setuptools libapache2-mod-wsgi
Restart Apache sudo service apache2 restart
Install and configure PostgreSQL
Install PostgreSQL sudo apt-get install postgresql

Check if no remote connections are allowed sudo vim /etc/postgresql/9.3/main/pg_hba.conf

Login as user "postgres" sudo su - postgres

Get into postgreSQL shell psql

Create a new database named catalog and create a new user named catalog in postgreSQL shell

postgres=# CREATE DATABASE catalog;
postgres=# CREATE USER catalog;
Set a password for user catalog

postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
Give user "catalog" permission to "catalog" application database

postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
Quit postgreSQL postgres=# \q

Exit from user "postgres"

exit
Install git, clone and setup your Catalog App project.
Install Git using sudo apt-get install git
Use cd /var/www to move to the /var/www directory
Create the application directory sudo mkdir FlaskApp
Move inside this directory using cd FlaskApp
Clone the Catalog App to the virtual machine git clone https://github.com/kongling893/Item_Catalog_UDACITY.git
Rename the project's name sudo mv ./Item_Catalog_UDACITY ./FlaskApp
Move to the inner FlaskApp directory using cd FlaskApp
Rename website.py to __init__.py using sudo mv website.py __init__.py
Edit database_setup.py, website.py and functions_helper.py and change engine = create_engine('sqlite:///toyshop.db') to engine = create_engine('postgresql://catalog:password@localhost/catalog')
Install pip sudo apt-get install python-pip
Use pip to install dependencies sudo pip install -r requirements.txt
Install psycopg2 sudo apt-get -qqy install postgresql python-psycopg2
Create database schema sudo python database_setup.py
Configure and Enable a New Virtual Host
Create FlaskApp.conf to edit: sudo nano /etc/apache2/sites-available/FlaskApp.conf

Add the following lines of code to the file to configure the virtual host.

<VirtualHost *:80>
	ServerName ec2-18-216-71-137.compute-1.amazonaws.com
	ServerAdmin danielpaladar@gmail.com
	WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
	<Directory /var/www/FlaskApp/FlaskApp/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/FlaskApp/FlaskApp/static
	<Directory /var/www/FlaskApp/FlaskApp/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
Enable the virtual host with the following command: sudo a2ensite FlaskApp

Create the .wsgi File
Create the .wsgi File under /var/www/FlaskApp:

cd /var/www/FlaskApp
sudo nano flaskapp.wsgi 
Add the following lines of code to the flaskapp.wsgi file:

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'
Restart Apache
Restart Apache sudo service apache2 restart
References:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

© 2018 GitHub, Inc.
Terms
Privacy
Security
Status
Help
Contact GitHub
Pricing
API
Training
Blog
About
Press h to open a hovercard with more details.
