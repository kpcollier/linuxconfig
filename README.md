## Log In Info
IP @ 54.190.193.14
SSH Port: 2200

## Update and Upgrade packages
```sudo apt-get update```
```sudo apt-get upgrade```

## Create User grader
```sudo adduser grader```

## Install Finger
Finger is a package that lists information about users
```sudo apt-get install finger```

## Give grader sudo access
Copy root sudoers.d file to grader, update grader file.

## Generate a keypair
Go on your local machine and type ```ssh-keygen```
Go to your grader. ```mkdir .ssh``` ```touch .ssh/authorized_keys```
Paste public key from local machine into grader authorized_keys file.
You can now log in to grader from local machine.

## Change SSH port from 22 to 2200
```sudo nano /etc/ssh/sshd_config```
Find the line with "Port 22" and change to "Port 2200"

## Update Firewall
```sudo ufw allow 2200/tcp```
```sudo ufw allow 80/tcp```
```sudo ufw allow 123/udp```
```sudo ufw enable```

## Install Apache and mod_wsgi file
```sudo apt-get install apache2```
```sudo apt-get install libapache2-mod-wsgi python-dev```
Enable with: ```sudo a2enmod wsgi```
```sudo service apache2 start```

## Install Git and clone ItemCatalog
```sudo apt-get install git```
```cd /var/www```
```mkdir catalog```
Change ownership ```sudo chown -R grader:grader catalog```
move into new directory: ```cd catalog```
Clone ItemCatalog repository: ```git clone https://github.com/kpcollier/ItemCatalog.git```
Update catalog.wsgi file: ```sudo nano catalog.wsgi```
Add: ```import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application```

## Install Virtualenv
```sudo apt-get install python-pip```
```sudo pip install virtualenv```
```cd /var/www/catalog``` ```sudo virtualenv venv```
activate virtual env: ```source venv/bin/activate```
```sudo pip install Flask```
```sudo pip install bleach httplib2 request oauth2client sqlalchemy psycopg2```

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

## 
