# Windows Development - Install

## Overview

I've put together here a list of steps I've taken in order to hack around with the code for discourse under windows
This is a list of install steps for the setup of a VM under windows

## Install

First we need a virtual machine to house Ubuntu / redis / postgresql / Discourse etc
So far I'm currently usinb VMWare but VirtualBox should work just as fine.

### Windows 10 VM Fix

If using Windows 10 then you may need to turn off a couple of options under Local Policy to get Virtual machines working

  * https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2146361

Note when doing this I didn't need to delete the EFI variables <br>
I just removed Hyper-V under Windows Features, and set Virtualization Based Security to disabled under Local Policy

### Linux OS

The version of Ubuntu I'm using for the VM is 16.04.1 LTS

  * http://www.ubuntu.com/download/desktop

After initial VM install, run this to update the package index
```
# Update Package database
apt-get update
# Update Packages
apt-get upgrade
# Update kernel level packages
apt-get dist-upgrade
```

### Depends

Next we need to install a couple of depends
```
bash <(wget -qO- https://raw.githubusercontent.com/techAPJ/install-rails/master/linux)
```

To setup phantomjs
```
cd /usr/local/share
wget https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-1.9.8-linux-x86_64.tar.bz2
tar xvf phantomjs-1.9.8-linux-x86_64.tar.bz2
rm phantomjs-1.9.8-linux-x86_64.tar.bz2
ln -s /usr/local/share/phantomjs-1.9.8-linux-x86_64/bin/phantomjs /usr/local/bin/phantomjs
cd
```

Next to install ruby bundler under the root user
```
apt install ruby-bundler
gem install bundler
apt-get install ruby-dev
```


### Discourse

Next we need to download discourse
At the time of writing using version 1.6.3

```
git clone https://github.com/discourse/discourse.git ~/discourse
cd discourse
git checkout v1.7.0.beta4
```

## Setup

### Postgresql

Next we need to setup the database
```
sudo -u postgres psql postgres
```

The username discourse will be running as needs to match the below (in this case I've used richard)
```
# Create the database role
CREATE ROLE richard WITH LOGIN ENCRYPTED PASSWORD 'discourse' CREATEDB SUPERUSER;
# Check to see it's been created
\du
# Create test and development databases
CREATE DATABASE discourse_development WITH OWNER richard ENCODING 'UTF8' TEMPLATE template0;
CREATE DATABASE discourse_test WITH OWNER richard ENCODING 'UTF8' TEMPLATE template0;
```
Exit the postgresql console with Ctrl-D

Enter postgrese as your user / discourse_development and create a couple of extensions
```
# Password should be discourse
psql -d discourse_development -U richard -h localhost
CREATE EXTENSION pg_trgm;
CREATE EXTENSION hstore;
```

Enter postgrese as your user / discourse_test and create a couple of extensions
```
# Password should be discourse
psql -d discourse_test -U richard -h localhost
CREATE EXTENSION pg_trgm;
CREATE EXTENSION hstore;
```

### Bootstrap Discourse

```
cd ~/discourse
source ~/.bashrc
bundle install
```

```
# Database setup
bundle exec rake db:migrate db:test:prepare db:seed_fu
```

### Setting up Admin user

To setup an admin user that we can use to do the initial login
```
RAILS_ENV=development
bundle exec rake admin:create
```

## Starting Discourse

Start the rails server
```
bundle exec rails server -b 0.0.0.0
```

The discourse server should now be accessible on http://localhost:3000 via the vm <br>
or if port forwarding has been setup in vmware workstation http://192.168.142.128:3000/ <br>
where 192.168.142.128 is the IP of the VM

You may also have to disable ubuntu's firewall if accessing the vm from the host machine
```
sudo ufw disable
```
