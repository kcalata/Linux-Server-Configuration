# Linux Server Configuration
The Linux Server Configuration project is a requirement to finish Udacity's Full Stack Nanodegree program. The goal of the project is to take a baseline installion of a Linux server and prepare it to host web applications. I need to secure the server from a number of attack vectors, install and configure a database server, and deploy one of my exisiting web applications onto it. This page wil explain how to do all of this. Part of the instructions come from Udacity.

You can visit http://52.54.46.55 or http://ec2-52-54-46-55.compute-1.amazonaws.com

## Step by step walkthrough
### Get your server
1. Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
* Log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.
* Create an instance
* Pick a plain Ubunti Linux image after choosing "OS Only". I chose Ubuntu 16.04 LTS.
* Choose your instance plan. Picking the lowest one to get free-tier access for a month.
* Give your instance a hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it.
* Wait for it to start up.
  * While your instance is starting up, Lightsail shows you a grayed-out display.
  * Once your instance is running, the display gets brighter.
* The public IP address of the instance is displayed along with its name.
2. Follow the instructions provided to SSH into the server.
* From the ```Account``` Menu on Amazon Lightsail, click on the ```SSH keys``` tab and download the Default Private Key.
* Move the file ```LightsailDefaultKey-*.pem```(the * represents where the server is located) into the local folder ```~/.ssh``` and rename it ```udacity_key.rsa```.
* In your terminal, run the following command: ```chmod 600 ~/.ssh/udacity_key.rsa```.
* To connect to the server, run the following command in your terminal, ```ssh -i ~/.ssh/udacity_key.rsa ubuntu@52.54.46.55```, where ```52.54.46.55``` is the public IP address of the server.

### Secure your server
3. Update all currently installed packages.
* In your terminal, run the following commands:
```
sudo apt-get update
sudo apt-get upgrade
```
4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
* Edit the ```/etc/ssh/sshd_config``` by running the following command: ```sudo nano /etc/ssh/sshd_config```.
* Change the port number on line 5 from ```22``` to ```2200```.
* Find the ```PermitRootLogin``` and edit it to ```no```.
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
* Restart the SSH connection by running the following command: ```sudo service ssh restart```.
5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
* Run the following commands:
```
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow 123/udp
sudo ufw deny 22
sudo ufw enable
y
exit
```
* Go to your Amazon Lightsail Instances and click on the three vertical dots in the instance and click on ```Manage```. Click on the ```Networking``` tab and change the firewall configuration to match the instance firewall settings above.
* Allows ports 2200 (TCP), 80 (TCP), and 123 (UDP). Deny the default port 22.
* In your terminal, run the following command: ```ssh -i ~/.ssh/udacity_key.rsa -p 2200 ubuntu@52.54.46.55```.
6. Install fail2ban
```
sudo apt-get install fail2ban
sudo apt-get install sendmail
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```
* Set the ```destemail``` field to admin user's email address.
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
7. Set scripts to automatically manage package updates
```
sudo apt-get install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### Give grader access
8. Create a new user account named ```grader```.
* In your terminal, run the following command: ```sudo adduser grader```.
* Enter a password twice and fill out information for ```grader```.
9. Give ```grader``` the permision to ```sudo```.
* In your terminal, run the following command: ```sudo visudo```.
* Under the line ```root ALL=(ALL:ALL) ALL```, add this line ```grader ALL=(ALL:ALL) ALL```.
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
10. Create an SSH key pair for ```grader``` using the ```ssh-keygen``` tool.
* On the local machine:
 * In your terminal, run the following commands:
 ```
 cd ~/.ssh
 ssh-keygen -f ~/.ssh/grader_key.rsa
 grader
 grader
 cat ~/.ssh/grader_key.rsa.pub
 ```
 * Copy the contents of ```grader_key.rsa.pub```
 * On ubuntu's terminal, run the following commands:
 ```
 touch /home/grader/.ssh/authorized_keys
 ```
 * Paste the content into this file, save and exit.
 * On graders's terminal, run the following commands:
 ```
 sudo chmod 700 /home/grader/.ssh
 sudo chmod 644 /home/grader/.ssh/authorized_keys
 sudo chown -R grader:grader /home/grader/.ssh
 sudo service ssh restart
 ```
 * You are now able to ssh as grader by running the following command: ```ssh -i ~/.ssh/grader_key.rsa -p 2200 grader@52.54.46.55```

### Prepare to deploy your project
11. Configure the local timezone to UTC.
* Run the following command, ```sudo dpkg-reconfigure tzdata```, choose ```Etc``` or ```None of the above```, and finally ```UTC```.
12. Install and configure Apache to server a Python mod_wsgi application.
* While logged in as ```grader```, run the following command: ```sudo apt-get install apache2```.
* If the Apache2 Ubuntu Default Page loads after entering ```52.54.46.55``` into the browser, Apache was successfully installed.
* Run the following commands:
```
sudo apt-get install libapache2-mod-wsgi-py3
sudo a2enmod wsgi
sudo service apache2 start
```
13. Install ```git```.
* While logged in as ```grader```, run the follow command: ```sudo apt-get install git```.

### Deploy the Item Catalog project.
14. Clone and setup my Item Catalog project.
* While logged in as ```grader```, run the following commands:
```
sudo mkdir /var/www/catalog
cd /var/www/catalog
sudo git clone https://github.com/kcalata/Item-Catalog.git catalog
cd /var/www
sudo chown -R grader:grader catalog/
cd /var/www/catalog/catalog
mv app.py __init__.py
nano __init__.py
```
* Near the bottom of ```__init__.py``` are the lines:
```
app.debug = True
app.run(host='0.0.0.0', port=8000)
```
* Delete the two lines and add the following line: ```app.run()```.
* Near the top of ```__init__.py``` is the line: ```engine = create_engine('sqlite:///itemcatalog.db',connect_args={'check_same_thread': False})```
* Replace the line with the following line: ```engine = create_engine('postgresql://catalog:catalog@localhost/catalog',connect_args={'check_same_thread': False})```
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
* Run the following command: ```nano database_setup.py```.
* Near the bottom of ```database_setup.py``` is the line: ```engine = create_engine('sqlite:///itemcatalog.db')```
* Replace the line with the following line: ```engine = create_engine('postgresql://catalog:catalog@localhost/catalog')```
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
* Run the following commmand: ```sudo nano /var/www/catalog/catalog.wsgi``` and add these lines:
```
activate_this = '/var/www/catalog/catalog/venv3/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/catalog/")
sys.path.insert(1, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'
```
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
15. Install virtual environment
* Run the following commands:
```
sudo apt-get install python3-pip
sudo pip install virtualenv
cd /var/www/catalog/catalog
virtualenv -p python3 venv3
sudo chown -R grader:grader venv3/
. venv3/bin/activate
```
16. Install Flask
* Run the following command: ```pip install Flask```
17. Install the project's dependencies
* Run the following command:
```
pip install httplib2
pip install requests
pip install --upgrade oauth2client
pip install sqlalchemy
sudo apt-get install libpq-dev
pip install pyscopg2
deactivate
```
18. Configure and enable a new virtual host
* Run the following command: ```sudo nano /etc/apache2/mods-enabled/wsgi.conf```
* Below where it says ```#WSGIPythonPath directory|directory-1:directory-2:...``` add the following line: ```WSGIPythonPath /var/www/catalog/catalog/venv3/lib/python3.5/site-packages```
* Run the following command: ```sudo nano /etc/apache2/sites-available/catalog.conf``` and paste the following code:
```
<VirtualHost *:80>
    ServerName 52.54.46.55
  ServerAlias ec2-52-54-46-55.compute-1.amazonaws.com
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
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
* Run the following command: ```sudo a2ensite catalog```
* Run the following command: ```sudo service apache2 reload```
19. Install and configure PostgreSQL
* Run the following commands:
```
sudo apt-get install libpq-dev python-dev
sudo apt-get install postgresql postgresql-contrib
sudo su - postgres
psql
CREATE USER catalog WITH PASSWORD 'catalog';
ALTER USER catalog CREATEDB;
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
\q
exit
. venv3/bin/activate
python /var/www/catalog/catalog/database_setup.py
sudo nano /etc/postgresql/9.5/main/pg_hba.conf
deactivate
```
* Paste the following into ```pg_hba.conf```:
```
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
```
* Save and exit using ```CTRL+X``` and confirm the changes with ```Y```.
* Run the following command: ```sudo service apache2 restart```

#### Special thanks to [iliketomatoes](https://github.com/iliketomatoes/linux_server_configuration) for his detailed README
