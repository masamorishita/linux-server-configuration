# Linux Server Configuration
Linux server configuration instruction for running the application.


## i. The IP address and SSH port.
IP address is as following:
```
3.112.16.53
```

SSH port is set to `2200` for security reason.


## ii. The complete URL to the hosted web application.

The application is currently avaialble in the following URL:
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
sudo apt-get update
sudo apt-get upgrade
```

In order to change the port for ssh, edit `/etc/ssh/sshd_config` and change the default ssh port from `22` to `2200`
To set the firewall, the following commands are executed:
```
sudo ufw default deny incoming
sudo ufw default deny outgoing
sudo ufw allow 2200/tcp
sudo ufw allow http
sudo ufw allow udp
sudo ufw deny 22
sudo ufw enable
```

You may also need to set the relevant port on "Networking" tab on AWS Lightsail console page. 


#### 2.3. Give new user access


#### 2.4. Prepare to deploy the application


#### 2.5. Deploy the Item Catalog application


## iv. A list of third-party resources
- [Flask Documentation](http://flask.pocoo.org/docs/0.12/)
- [Apache HTTP Server Version 2.4 Documentation](https://httpd.apache.org/docs/2.4/)
- [PostgreSQL 9.3.25 Documentation](https://www.postgresql.org/docs/9.3/index.html)
- [How To Deploy a Flask Application on an Ubuntu VPS - DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
