# Linux-Server-Configuration
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

IP ADDRESS: 3.6.92.91

HTTP ADDRESS: http://3.6.92.91/

SSH PORT: 2200

## Table of Contents

- [Tasks](#tasks)
- [Open Issues](#open-issues)
- [References](#references)

## Tasks

### Update all currently installed packages.
```sh
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo reboot
```

#### Reference - 
https://serverfault.com/questions/265410/ubuntu-server-message-says-packages-can-be-updated-but-apt-get-does-not-update

https://askubuntu.com/questions/94102/what-is-the-difference-between-apt-get-update-and-upgrade


#### Status - 
After Performing all the commands the Update still does not work for me. it shows 17 packages can be updated.
2 updates are security updates. However in response it shows below logs.
```sh
eading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
grader@ip-172-26-15-157:~$ sudo apt-get update && sudo apt-get dist-upgrade
Hit:1 http://ppa.launchpad.net/deadsnakes/ppa/ubuntu xenial InRelease
Hit:2 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu xenial InRelease
Get:3 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu xenial-updates InRelease [109 kB]
Get:4 http://security.ubuntu.com/ubuntu xenial-security InRelease [109 kB]
Get:5 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu xenial-backports InRelease [107 kB]
Fetched 325 kB in 1s (243 kB/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Create User grader and provide sudo access to it.
```sh
sudo adduser grader
sudo touch /etc/sudoers.d/grader
sudo vim /etc/sudoers.d/grader
```
In sudoers file add - `grader ALL=(ALL:ALL) ALL`

Password for grader to access sudo commands - `grader`

### SSH key-pair for grader user.
Generate ssh key on local machine - `ssh-keygen -f ~/.ssh/<key-name>` (leave passphrase empty)
```sh
su - grader
mkdir .ssh
touch .ssh/authorized_keys
vim .ssh/authorized_keys  [Copy the key-name.pub content]
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
ssh -i <key-name> grader@3.6.92.91
```

### Change the SSH port from 22 to 2200 and configure firewall
Reference -  https://knowledge.udacity.com/questions/83268
```sh
sudo vim /etc/ssh/sshd_config [change Port to 2200]
sudo service ssh restart
```
#### Configure firewall
```sh
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable
ssh -i <key-name> grader@3.6.92.91 -p 2200
```
### Set local timezone to UTC

```sh
sudo dpkg-reconfigure tzdata
```
Select UTC in the list.

### Install and Configure PostgreSQL
```sh
sudo apt-get install postgresql
sudo su - postgres
psql
postgres=# CREATE USER catalog WITH PASSWORD 'grader';
postgres=# ALTER USER catalog CREATEDB;
postgres=# CREATE DATABASE catalog WITH OWNER catalog;
postgres=# \c catalog
catalog=# REVOKE ALL ON SCHEMA public FROM public;
catalog=# GRANT ALL ON SCHEMA public TO catalog;
\q
exit
```

### Apache, Python and Mod wsgi setup.
By default mod_wsgi works with python3.5, to run it with python 3.6 i used below commands - 
#### Install python 3.6
```sh
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.6
ls /usr/bin/python*
```
#### Install pip and packages for python 3.6
```sh
sudo curl https://bootstrap.pypa.io/get-pip.py | sudo -H python3.6
sudo python3.6 get-pip.py

sudo pip3 install flask
sudo pip3 install oauth2client
sudo pip3 install psycopg2
sudo pip3 install requests
sudo pip3 install sqlalchemy
sudo pip3 install psycopg2-binary
```
#### Install mod_wsgi with python 3.6
```sh
sudo apt install apache2-dev
sudo apt-get install python3.6-dev
sudo apt-get install libpython3.6-dev

python3.6 -m pip install mod_wsgi
```
#### Install apache2 
```sh
sudo apt-get install apache2
```
#### Configure apache to run with mod_wsgi
Run below command - 
```sh
mod_wsgi-express module-config
```
Result - 
`LoadModule wsgi_module "/home/grader/.local/lib/python3.6/site-packages/mod_wsgi/server/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so"
WSGIPythonHome "/usr"`

Put the above result config in /etc/apache2/apache.conf file.
Restart apache service
```sh
 sudo service apache2 restart
```

### Deploy application
### Install git and project repo
```sh
 sudo apt-get install git
 cd /var/www
 sudo git clone https://github.com/nikitachouhan/fsnd-item-catalog.git catalog
```
#### Configure db_setup.py, populate_db_data.py, and application.py for PostgreSQL.
```python
#replace existing engine config with below
engine = create_engine('postgresql://catalog:grader@localhost/catalog')
```
#### Run application files.
```sh
python3.6 db_setup.py
python3.6 populate_db_data.py
python3.6 application.py
```
#### Configure web server
Create a wsgi.py file
```sh
sudo touch /var/www/catalog/wsgi.py
```

Configure above file with below script
```python
# Provide application path
import sys
sys.path.insert(0, "/var/www/catalog")

# Import Flask instance
from application import app as application

if __name__ == '__main__':
    app.config['SESSION_TYPE'] = 'filesystem'
    app.run(host='0.0.0.0', port=8000)
```

Create a conf file
```sh
sudo touch /etc/apache2/sites-available/catalog.conf
```

Configure above file with below script
```sh
<VirtualHost *:80>
        ServerName 3.6.92.91
        ServerAdmin nikitachouhan@gmail.com
        ServerAlias 3.6.92.92.nip.io
        WSGIDaemonProcess application
        WSGIScriptAlias / /var/www/catalog/wsgi.py
        <Directory /var/www/catalog/>
                WSGIProcessGroup application
                WSGIApplicationGroup %{GLOBAL}
                Require all granted
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Enable virtual host
```sh
sudo a2ensite catalog
sudo service apache2 restart
```
At this point on http://3.6.92.91/ I am able to access my web application.

## Open Issues

### Issue with Google Sign-in
I changed "redirect_uris" and  "javascript_origins" to `http://3.6.92.91.nip.io,http://3.6.92.91` in client_secrets.json.

While clicking on Google-signin button i get this error - `Error 400: invalid_request Permission denied to generate login hint for target domain.`

I have similar issue mentioned in this thread - https://knowledge.udacity.com/questions/28323 .It is mentioned that Google OAuth working is not required for submission.
The issue still persists with my application.

### Duplicated Categories
Categories are duplicated on home page. This is due to multiple runs of populate_db_data.py. Since category name is not a unique field the data was duplicated with new id. 

## References
https://titanwolf.org/Network/Articles/Article?AID=ca581545-474a-4aa0-83dc-c57c6c344d32#gsc.tab=0

https://newbedev.com/change-mod-wsgi-from-python3-5-to-3-6

https://nip.io/

https://stackoverflow.com/questions/20627327/invalid-command-wsgiscriptalias-perhaps-misspelled-or-defined-by-a-module-not

https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps

https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29
