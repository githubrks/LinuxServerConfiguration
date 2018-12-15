# Linux Configuration - Movie Catalog Project

This is the Linux configuration project for "Full Stack Developer Nanodegree" on Udacity.
In this project, a Linux virtual machine needs to be configurated to support the Movie Catalog website.

Server IP ADDRESS: 54.205.147.108

Movie Catalog App: http://movies.54.205.147.108.xip.io/

## Tasks

1. Launch Amazon Lightsail Ubuntu VM
2. Follow the instructions provided to SSH into Ubuntu VM
3. Create a new user named grader
4. Give the grader the permission to sudo
5. Update all currently installed packages
6. Change the SSH port from 22 to 2200
7. Configure the local timezone to UTC
8. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
9. Install and configure Apache to serve a Python mod_wsgi application
10. Install and configure PostgreSQL:
	- Do not allow remote connections
	- Create a new user named catalog that has limited permissions to your catalog application database
11. Install git, clone and setup Movie Catalog App project.

## Launch Amazon Lightsail Ubuntu VM

## Instructions for SSH access to the instance
1. Download Private Key from Amazon Lightsail account page
2. Move the private key file into the folder `~/.ssh`
3. Open the terminal and type in
	```chmod 600 ~/.ssh/LightsailDefaultKey-us-east-1.pem```
4. In your terminal, type in
	```ssh -i ~/.ssh/LightsailDefaultKey-us-east-1 ubuntu@54.205.147.108```


## Create a new user named grader

1. `sudo adduser grader`
3. `sudo nano /etc/sudoers.d/grader`
4. Paste the following `grader ALL=(ALL:ALL) ALL`, save and quit.

## Set ssh login using keys

1. Generate keys via `ssh-keygen` and save the private key in `~/.ssh` on local machine.
2. Deploy public key on Ubuntu VM

	On virtual machine:
	```
	$ sudo su - grader
	$ mkdir .ssh
	$ touch .ssh/authorized_keys
	$ vim .ssh/authorized_keys
	```
	Copy the public key generated on your local machine to this file and save
	```
	$ chmod 700 .ssh
	$ chmod 600 .ssh/authorized_keys
	```
	
3. Reload SSH using `sudo service ssh restart`
4. Use ssh to login with the new user `grader`

	`ssh -i [~/.ssh/privateKeyFilename] grader@54.205.147.108`

## Update all currently installed packages

	sudo apt-get update
	sudo apt-get upgrade

## Change the SSH port from 22 to 2200

1. Use `sudo nano /etc/ssh/sshd_config` and then change Port 22 to Port 2200 , save & quit.
2. Reload SSH using `sudo service ssh restart`

## Change the timezone to UTC

1. Configure the time zone sudo dpkg-reconfigure tzdata


## Configure the Uncomplicated Firewall (UFW)

Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

	sudo ufw allow 2200/tcp
	sudo ufw allow 80/tcp
	sudo ufw allow 123/udp
	sudo ufw deny ssh
	sudo ufw enable 

## Install and configure Apache to serve a Python mod_wsgi application

1. Install Apache `sudo apt-get install apache2`
2. Install mod_wsgi `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. Restart Apache `sudo service apache2 restart`

## Install and configure PostgreSQL

1. Install PostgreSQL `sudo apt-get install postgresql`
2. Check if no remote connections are allowed `sudo vim /etc/postgresql/10/main/pg_hba.conf`
3. Login as user "postgres" `sudo su - postgres`
4. Get into postgreSQL shell `psql`
5. Create a new database named catalog  and create a new user named catalog in postgreSQL shell
	
	```
	postgres=# CREATE DATABASE catalog;
	postgres=# CREATE USER catalog;
	```
5. Set a password for user catalog
	
	```
	postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
	```
6. Give user "catalog" permission to "catalog" application database
	
	```
	postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
	```
7. Quit postgreSQL `postgres=# \q`
8. Exit from user "postgres" 
	
	```
	exit
	```
 
## Install git, clone and setup your Catalog App project

1. Install Git using `sudo apt-get install git`
2. Use `cd /var/www` to move to the /var/www directory 
3. Create the application directory `sudo mkdir MovieCatalog`
4. Move inside this directory using `cd MovieCatalog`
5. Clone the Catalog App to the virtual machine `sudo git clone https://github.com/githubrks/MovieCatalog.git`
6. Move to the inner MovieCatalog directory using `cd MovieCatalog`
7. Rename `application.py` to `__init__.py` using `sudo mv application.py __init__.py`
8. Install pip `sudo apt-get install python-pip`
9. Use pip to install dependencies `sudo pip install -r requirements.txt`


## Configure and Enable a New Virtual Host

1. Create FlaskApp.conf to edit: `sudo nano /etc/apache2/sites-available/MovieCatalog.conf`
2. Add the following lines of code to the file to configure the virtual host. 
	
	```
	<VirtualHost *:80>
		ServerName movies.54.205.147.108.xip.io
		ServerAdmin prag.sn12@gmail.com
		WSGIScriptAlias / /var/www/MovieCatalog/moviecatalog.wsgi
		<Directory /var/www/MovieCatalog/MovieCatalog/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/MovieCatalog/MovieCatalog/static
		<Directory /var/www/MovieCatalog/MovieCatalog/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>
	```
3. Enable the virtual host with the following command: `sudo a2ensite MovieCatalog`

## Create the .wsgi File

1. Create the .wsgi File under /var/www/MovieCatalog: 
	
	```
	cd /var/www/MovieCatalog
	sudo nano moviecatalog.wsgi 
	```
2. Add the following lines of code to the flaskapp.wsgi file:
	
	```
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/MovieCatalog/")

	from MovieCatalog import app as application
	application.secret_key = 'Add your secret key'
	```

## Restart Apache

1. Restart Apache `sudo service apache2 restart`

## References:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
