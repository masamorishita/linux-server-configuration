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
### 1. A summary of software installed:
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
git # to use git in the system
PostgreSQL # to use PostgreSQL
python-pip # to enable pip install
python-virtualenv # to build a virtual environment

```


## iv. A list of any third-party resources you made use of to complete this project.
