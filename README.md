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
