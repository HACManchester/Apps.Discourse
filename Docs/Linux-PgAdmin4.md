# PgAdmin4

## Overview

The latest pgadmin is version 4 which is at release candidate status.
This differs from the older versions in that it's web based

  * http://askubuntu.com/questions/788457/how-to-install-pgadmin-4-on-ubuntu-16-04

Version 4 the web one seems to have issues with querying, the below works at the console though. <br>
```
psql -U vagrant -d discourse_development -c 'SELECT * FROM categories LIMIT 20'
```

For now still using PgAdmin3 to query the database which is a gui app


## Install

First upgrade the system
```
apt-get update
apt-get upgrade
apt-get dist-upgrade
```

Next install some additional depends
```
sudo apt-get install python-pip python-dev build-essential 
sudo pip install virtualenvwrapper
```

## Setup

### Setup Virtual Environment

Next lets setup a virtual environment
```
cd ~
virtualenv pgadmin
cd pgadmin
source bin/activate
```

Next install pgadmin4 via pip
```
wget https://ftp.postgresql.org/pub/pgadmin3/pgadmin4/v1.0-rc1/pip/pgadmin4-1.0rc1-py2-none-any.whl
pip install pgadmin4-1.0rc1-py2-none-any.whl
pip install cryptography
exit
```

### Setup pgadmin config file

Setup the config file
```
cd ~/pgadmin/lib/python2.7/site-packages/pgadmin4
cp config.py config_local.py
```

Note change the default server setting from localhost to 0.0.0.0 in the config to be accessed on other IP address's


### Ports forwarding

In order for this to work from the host, setup the ports 5050 and 5432 to be forwarded from the VM by editing the Vagrant file


### Postgresql Setup

Next we need to set Postgresql to allow connections from the outside

Edit /etc/postgresql/9.5/main/pg_hba.conf <br>
and add the following line
```
host all all 0.0.0.0/0 trust
```

Edit /etc/postgresql/9.5/main/postgresql.conf <br>
and add the following line
```
listen_addresses = '*'
```

Restart postgresql
```
/etc/init.d/postgresql restart
```


## Startup

To startup the web server

```
cd ~/pgadmin/
source bin/activate

cd ~/pgadmin/lib/python2.7/site-packages/pgadmin4
python  ./pgAdmin4.py
```

The instance should now be visible from the host on http://localhost:5050


### Setup Server Connection

In the Servers tab to connect to the discourse postgres database

  * Hostname Address: 127.0.0.1
  * Database: discourse_development
  * Username: vagrant
  * Password: vagrant

