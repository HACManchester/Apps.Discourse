# Windows Development - Vagrant

## Overview

Using vargrant seems to be the best and simplest way to develop under windows.
However it is limited to the user of VirtualBox

  * http://blog.discourse.org/2013/04/discourse-as-your-first-rails-app/
  * https://github.com/discourse/discourse/blob/master/docs/DEVELOPER-ADVANCED.md
  * https://github.com/discourse/discourse/blob/master/docs/VAGRANT.md


## Install

First install the windows version of vagrant (1.8.5)

  * https://www.vagrantup.com/downloads.html

Next install virtualbox (5.1.6) <br>
virtualbox is needed since vagrant's plugin for vmware isn't free

  * https://www.virtualbox.org/wiki/Downloads


## Discourse Setup

Next lets clone the version of discourse we'll be using

```
git clone https://github.com/discourse/discourse.git discourse
cd discourse
git checkout v1.7.0.beta4
```

Let's use vagrant to create the VM
```
# Create the VM / Start the VM
vagrant up
# Connect via ssh
vagrant ssh
```

At this point we can now use putty to connect to the VM via 127.0.0.1 / 2222 <br>
Using the username / password vagrant / vagrant

Lets finish the setup
```
cd /vagrant
bundle install
bundle exec rake db:create db:migrate db:test:prepare
```

Create an Admin user
```
bundle exec rake admin:create
```


## Development

### Starting everything up

First we need to start mailcatcher to catch all mails
```
cd ~/
mailcatcher --ip 0.0.0.0
```

Next we need to start discourse in one shell window
```
cd /vagrant/
RAILS_ENV=development
bundle exec rails s -b 0.0.0.0
```

Next to process outgoing mails into mailcatcher etc we need to start sidekiq in a 2nd shell window
```
bundle exec sidekiq -l log/sidekiq.log -q critical,low,default
```


### Development URLs

The following should now be accessible

  * http://127.0.0.1:4000 - Discourse instance
  * http://127.0.0.1:4000/sidekiq - Sidekiq queues
  * http://127.0.0.1:4080 - mailcatcher


### Stopping the VM

To stop the VM
```
# Hit Ctrl-C to stop the server for both sidekiq and discourse
# To exit the VM
exit
# To Shutdown the VM
vagrant halt
```

## Notes

### File Changes

Any changes to files in the downloaded source directory will be mirroed within the VM
However after making any changes the discourse server needs to be restarted.
Also you'll need two consoles to run both sidekiq and the rails server.

### Docker Config Files

The "app.yml" file often referenced is actually a docker file to define how the docker image is built.
In this case this doesn't apply with a development setup.
"./launcher rebuild app" is actually just a re-creation of the docker image which again doesn't apply


### TODO

Can't seem to get MailCatcher to work / recieve mails
tried swaping the smtp address from localhost to 127.0.0.1 in case it was using the ip6 localhost address
