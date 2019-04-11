# Linux Server Configuration
Linux server configuration instruction for running the application.


## i. The IP address and SSH port.
IP address is as following:
```
3.112.16.53
```

SSH port is set to `2200` for security reason.


## ii. The complete URL to the hosted web application.

The application is currently avaialble on the following URL:
```
http://3.112.16.53.xip.io
```


## iii. A summary of software installed and configuration changes made.

### 1. Software installed
To run the apache server with WSGI module, the following softwares are installed:
```
apache2
libapache2-mod-wsgi
```
During the configuration, the following softwares are installed in the system
in order to run the application:
```
flask
bleach
httplib2
request
oauth2client
sqlalchemy
python-psycopg2
```
Other than the above softwares, the following softwares are also installed:
```
git    # to use git in the system
PostgreSQL    # to use PostgreSQL
python-pip    # to enable pip install
python-virtualenv    # to build a virtual environment

```


### 2. Configuration changes made

#### 2.1. Get the server
Amazon Lightsail is used to prepare Linux Ubuntu server to host the application.

#### 2.2. Secure the server
To update all currently installed packages, the following commands are excecuted:
```
$ sudo apt-get update
$ sudo apt-get upgrade
```

In order to change the port for ssh, edit `/etc/ssh/sshd_config` and change the default ssh port from `22` to `2200`.

To set the firewall, the following commands are executed:
```
$ sudo ufw default deny incoming
$ sudo ufw default deny outgoing
$ sudo ufw allow 2200/tcp
$ sudo ufw allow http
$ sudo ufw allow udp
$ sudo ufw deny 22
$ sudo ufw enable
```

You may also need to set the relevant port on "Networking" tab on AWS Lightsail console page. 


#### 2.3. Give new user access
In order to add a new user `grader`, run the following command:
```
$ sudo adduser grader
```


To give `grader` the permission to `sudo`, run the following command to edit the file:
```
$ sudo nano /etc/sudoers.d/grader
```
Then add the following line:
```
grader ALL=(ALL)
```


In order to set the SSH key pair for `grader`, firstly run `ssh-keygen` on your local machine.

Then copy the generated public key for pasting the server side later.

To set the key on your server, run the following command to edit the file:
```
$ su - grader
$ mkdir .ssh
$ touch .ssh/authorized_keys
$ nano .ssh/authorized_keys
```
Then paste the public key you copied previously.


Reload by running `service ssh restart` and you should be able to login with the private key you generated locally.


#### 2.4. Prepare to deploy the application
To setup and run the apache server, run the following commands:
```
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi
$ sudo apache2ctl restart
```

To prepare a PostgreSQL database with the user named `catalog`, run the following commands:
```
$ sudo apt-get install PostgreSQL
$ sudo su - postgres
$ psql
# CREATE USER catalog WITH PASSWORD 'password';
# ALTER USER catalog CREATEDB;
# CREATE DATABASE catalog WITH OWNER catalog;
# \c catalog
# REVOKE ALL ON SCHEMA public FROM public;
# GRANT ALL ON SCHEMA public TO catalog;
```
Now that the database should be ready to use with `catalog` user.


#### 2.5. Deploy Item Catalog application
##### 2.5.1. Clone Item Catalog repository
Fistly you need to clone Item Catalog repository in the relevant directory as following:
```
$ cd /var/www
$ sudo mkdir catalog
$ cd catalog
$ git clone https://github.com/masamorishita/item-catalog.git catalog
```

Then you add `catalog.wsgi` file on the directory `/var/www/catalog` including the following code:
```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'Add your secret key'
```

Rename `application.py` to `__init__.py` by following command:
```
$ mv application.py __init__.py
```

##### 2.5.2. Modify some code on Item Catalog application
Firstly, modify the database connection on `__init__.py` `database_setup.py` `addcategories.py` `lotsofitems.py` as following:
```
- engine = create_engine('sqlite:///itemwithusers.db')
+ engine = create_engine('postgresql://catalog:password@localhost/catalog')
```

Secondly, modify the path for `client_secrets.json` file on `__init__.py` as following
```
- CLIENT_ID = json.loads(open('client_secrets.json', 'r').read())['web']['client_id']
+ CLIENT_ID = json.loads(open('/var/www/catalog/catalog/client_secrets.json', 'r').read())['web']['client_id']
```
```
- oauth_flow = flow_from_clientsecrets('client_secrets.json', scope='')
+ oauth_flow = flow_from_clientsecrets('/var/www/catalog/catalog/client_secrets.json', scope='')
```

##### 2.5.3. Prepare Flask and prepare its virtual environment
To install and prepare the environment, run the following commands:
```
$ sudo apt-get install python-pip
$ sudo pip install virtualenv
$ sudo virtualenv venv
$ source venv/bin/activate
$ sudo pip install Flask
$ pip install bleach httplib2 request oauth2client sqlalchemy python-psycopg2    # to install all other neccesary softwares
```
Now that you should be able to run the application by executin the following command:
```
$ sudo python __init__.py
```

##### 2.5.4. Configure apache
Create the configuration file for apache by executing following command:
```
sudo nano /etc/apache2/sites-available/catalog.conf
```
The file should include the following code:
```
<VirtualHost *:80>
    ServerName 3.112.16.53
    ServerAdmin admin@3.112.16.53
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
To enable the virtual host, run the following command:
```
$ sudo a2ensite catalog
```

Finally, restart the server with the command:
```
$ sudo service apache2 restart
```

Now that you should be able to access Item Catalog application on [http://3.112.16.53](http://3.112.16.53).

## iv. A list of third-party resources
- [Flask Documentation](http://flask.pocoo.org/docs/0.12/)
- [Apache HTTP Server Version 2.4 Documentation](https://httpd.apache.org/docs/2.4/)
- [PostgreSQL 9.3.25 Documentation](https://www.postgresql.org/docs/9.3/index.html)
- [How To Deploy a Flask Application on an Ubuntu VPS - DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
