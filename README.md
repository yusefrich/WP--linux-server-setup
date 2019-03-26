
# Ubuntu server setup

Setup of a ubuntu server on amazon aws as a udacity project.


## Server details

* Server public ip

```
54.167.22.7
```

* SSH port

```
2200
```
* Application URL

```
http://54.167.22.7.xip.io
```
## The stored application uses
* Apache
* Apache mod_wsgi
* python 2.7
* PostgreSQL
* Flask
* SqlAlquemy
## Setup process used

### Updating ubuntu
----
* get and update all the current packages
```
apt-get update
```
```
apt-get upgrade
```
* installing unattended-upgrades to automate future updates
* intalling the package
```
sudo apt install unattended-upgrades
```
* enable automatic updates
```
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```
* eddit the file, final file should be like this. 
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```
* install finger
```
sudo apt-get install finger
```

### UFW configuration 
---
* Started by block all incoming connections on all ports and allow outgoing:

```
sudo ufw default deny incoming
```
```
sudo ufw default allow outgoing
```
* setting up all the ports needed:
```
sudo ufw allow 2200/tcp
```
```
sudo ufw allow 22
```
```
sudo ufw allow www
```
```
sudo ufw allow ntp
```
* check if the rules are ok using:
```
sudo ufw show added
```
* enable firewall
```
sudo ufw enable
``` 
* firewall status
```
sudo ufw status
```
* set the ssh connection port to 2200
```
sudo nano /etc/ssh/sshd_config
```
> edit line > ``Port 22`` to > ``port 2200``

* block connections from ssh port 22
```
ufw deny 22
```
* Restart the ssh server
```
sudo service ssh restart
```
### Add grader user
---
* Creating the grader user
```
sudo adduser grader
```
* add grader user to admin group
```
usermod -aG sudo grader
```
* edit sudoers file to list the grader user
* added the following line after > root    ALL=(ALL:ALL) ALL
> grader  ALL=(ALL:ALL) ALL

* the final file should look like this
```
# User privilege specification
root    ALL=(ALL:ALL) ALL
grader  ALL=(ALL:ALL) ALL
```
* generate the ssh keys for the user on the local machine, **do not run this next line on the server**
```
ssh-keygen
```
* create the folder that will store the key
```
sudo mkdir /home/grader/.ssh
```
```
sudo nano /home/grader/.ssh/authorized_keys
```
* inside authorized_keys file place a copy of the public key generated
> use `cat generated/key/path/<keyName>.pub`<br>
> copy paste all the content inside authorized_keys

* modify the files permition 
```
sudo chown grader:grader /home/grader/.ssh
``` 
```
sudo chmod 700 /home/grader/.ssh

``` 
```
sudo chown grader:grader /home/grader/.ssh/authorized_keys
``` 
```
sudo chmod 644 /home/grader/.ssh/authorized_keys
``` 
* Disable root login
```
sudo nano /etc/ssh/sshd_config
```
* edit the following lines
> PermitRootLogin no <br> (if commented, uncomment) PasswordAuthentication no
* restart the ssh server
```
sudo service ssh restart
```
### setting timezone to UTC
---
* set the timezone date to:
```
sudo timedatectl set-timezone UTC
```
### Installing needed packages for the application
---
### **apache and wsgi mod**
* installing apache:
```
sudo apt-get install apache2
```
```
sudo apt-get install libapache2-mod-wsgi
```
### **PostgreSQL**
* installing:
```
sudo apt-get install postgresql postgresql-contrib
```
* creating catalog user
```
sudo -u postgres createuser -P catalog
```
* creating database called catalog
```
sudo -u postgres createdb -O catalog catalog
```
### **Flask, SQLAlchemy and others imports**
* installing flask
```
sudo apt-get install python-psycopg2 python-flask
```
* installing sqlalchemy
```
sudo apt-get install python-sqlalchemy python-pip
```
* installing oauth2client
```
sudo pip install oauth2client
```
* installing requests
```
sudo pip install requests
```
* installing git
```
sudo apt-get install git
```

### Deplying application
---
* cloning the appliction repository
```
cd /var/www
```
* using git to clone the [app repo](https://github.com/yusefrich/WP--catalog-wsgi)
```
sudo git clone https://github.com/yusefrich/WP--catalog-wsgi
```
> the structure of the app was created based on this [article](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

* editing the apache .conf file
```
sudo nano /etc/apache2/sites-available/FlaskApp.conf
```
* the file should look like this
```
<VirtualHost *:80>
                ServerName catalog.com
                ServerAdmin admin@mywebsite.com
                WSGIDaemonProcess catalog user=www-data group=www-data t$
                WSGIProcessGroup catalog
                WSGIApplicationGroup %{GLOBAL}

                WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
                <Directory /var/www/FlaskApp/FlaskApp/>
                        Require all granted
                </Directory>
                Alias /static /var/www/FlaskApp/FlaskApp/static
                <Directory /var/www/FlaskApp/FlaskApp/static/>
                        Require all granted
                </Directory>

                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
* disable the default .conf
```
sudo a2dissite 000-default.conf
```
* Enable the created .conf
```
sudo a2ensite FlaskApp.conf
```
* run the `__init__.py` file
```
sudo python __init__.py
``` 
* restart the server
```
sudo service apache2 restart
```

## Authors


* **yusef richard** - *application short description*

## Reference links
 * [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
  * [Patterns for flask](http://flask.pocoo.org/docs/0.12/patterns/packages/)
  * [Hide Git Repos on Public Sites](https://davidegan.me/hide-git-repos-on-public-sites/)
  
### Other udacity students used as reference
 * [Tarja M](https://github.com/otsop110/fullstack-nanodegree-linux-server-configuration)
* [Steve Wooding](https://github.com/SteveWooding/fullstack-nanodegree-linux-server-config)
 * [Sean Holcomb](https://github.com/Sean-Holcomb/Linux-Server-Configuration)

# Udacity student data

 *NAME*: Yusef Richard de Oliveira Alves <p>
 *COURSE*: udacity Nanodegree fullstack <p>
 *EMAIL*:yusef.rick@gmail.com <p>
