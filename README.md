# Linux Server Configuration Project
Create a server side application to host the menu catalog that was created for Project 4. This is how I completed this.

## Log In Info
IP @ 54.190.193.14
SSH Port: 2200

## Update and Upgrade packages
For best security practices update your packages.
```sudo apt-get update```
```sudo apt-get upgrade```

## Create User grader
Create a new user named grader.
```sudo adduser grader```

## Install Finger
Finger is a package that lists information about users
```sudo apt-get install finger```

## Give grader sudo access
Copy root sudoers.d file to grader and update grader file to change name from "ubuntu" to "grader".

## Generate SSH Keypair
* Go on your local machine and type ```ssh-keygen```
* Go to your grader. ```mkdir .ssh``` ```touch .ssh/authorized_keys```
* Paste public key from local machine into grader authorized_keys file.
You can now log in to grader from local machine.

## Change SSH port from 22 to 2200
```sudo nano /etc/ssh/sshd_config```
Find the line with "Port 22" and change to "Port 2200"

## Update Firewall
Update the firewall to allow connecitions coming from only 2200, 80, and 123.
* ```sudo ufw allow 2200/tcp```
* ```sudo ufw allow 80/tcp```
* ```sudo ufw allow 123/udp```
* ```sudo ufw enable```

## Install Apache and mod_wsgi file
```sudo apt-get install apache2```
Mod_wsgi is an Apache HTTP server mod that enables Apache to serve Flask applications. Install mod_wsgi with the following command:
```sudo apt-get install libapache2-mod-wsgi python-dev```
Enable with: ```sudo a2enmod wsgi```
```sudo service apache2 start```

## Install Git and clone ItemCatalog
Install Git if its not already: ```sudo apt-get install git```
Move directories: ```cd /var/www```
Make directory: ```mkdir catalog```
Change ownership: ```sudo chown -R grader:grader catalog```
move into new directory: ```cd catalog```
Clone ItemCatalog repository: ```git clone https://github.com/kpcollier/ItemCatalog.git```
Update catalog.wsgi file: ```sudo nano catalog.wsgi```
Add: ```import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

activate_this = '/var/www/catalog/venv/bin/activate_this.py'
execfile(activate_this, dict(__file__=activate_this))

from ItemCatalog import app as application ```

## Install Virtualenv
First, install pip:```sudo apt-get install python-pip```
```sudo pip install virtualenv```
Move to directory:```cd /var/www/catalog``` then: ```sudo virtualenv venv```.
Activate virtual env: ```source venv/bin/activate```
Install Flask: ```sudo pip install Flask```
Install requirements: ```sudo pip install bleach httplib2 requests oauth2client sqlalchemy psycopg2```

## Configure Virtual Host
1. Create a virtual host conifg file: `$ sudo nano /etc/apache2/sites-available/catalog.conf`.
2. Paste:
```
<VirtualHost *:80>
    ServerName 54.190.193.14
    ServerAdmin admin@54.190.193.14
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable: ```sudo a2enmod catalog```

## 14 - Install and configure PostgreSQL

1. Install necessary packages for PostgreSQL: ```sudo apt-get install libpq-dev python-dev```.
2. Install PostgreSQL: ```sudo apt-get install postgresql postgresql-contrib```.
3. Log in to user that Postgres automattically made: ```sudo su -postgres``` Connect to database with ```psql```
4. Create a new user 'catalog' with his password: ```# CREATE USER catalog WITH PASSWORD 'goodpassword';```.
5. Give 'catalog' user the CREATEDB capability: ```# ALTER USER catalog CREATEDB;```.
6. Create 'catalog' database owned by *catalog* user: ```# CREATE DATABASE catalog WITH OWNER catalog;```.
7. Connect to the database: ```# \c catalog```.
8. Revoke all rights: ```# REVOKE ALL ON SCHEMA public FROM public;```.
9. Lock down the permissions to only let 'catalog' role create tables: ```# GRANT ALL ON SCHEMA public TO catalog;```.
10. Log out from PostgreSQL: `# \q`. Then return to the 'grader' user: ```$ exit```.
11. Inside the Flask application, the database connection is now performed with: 
```python
engine = create_engine('postgresql://catalog:goodpassword@localhost/catalog')
```
12. Setup the database with: ```python /var/www/catalog/catalog/database_setup.py```.

## Restart Apache to start app
```sudo service apache2 restart```

## ACKNOWLEDGMENTS
Trish from Udacity 1:1.
Rob from Udacity Forums.
Greg Berger from Udacity Forums.
