# Setting up postfix / SMTP for recieving mail

## Overview

The first stage for inbound mail is via smtp through postfix on port 25 <br>
Make sure port 5025 on the host is mapped to 25 on the VM


## Setup Catchall


### Catchall User

In order to setup a catchall mailbox for discourse, we need to setup a user to catch the mail
```
# Setup the user
sudo useradd -d /home/catchall -m catchall
sudo passwd catchall

# Lets change some permissions
chmod 1777 /var/mail
# Lets make sure /etc/aliases.db exists
newaliases
```


### Redirect

In order to setup a catchall / redirect within postfix

  * https://ubuntuforums.org/showthread.php?t=1156313
  * http://www.berkes.ca/guides/postfix_virtual.html

Create the file /etc/postfix/virtual and add the following
```
@testin.com    catchall
```

Edit /etc/postfix/main.cf and add the following
```
virtual_alias_domains = testin.com
virtual_alias_maps = hash:/etc/postfix/virtual
```


Run the following command
```
postmap /etc/postfix/virtual
postfix reload
sudo /etc/init.d/postfix restart
```

This should allow all mail sent to @testin.com to be caught and sent to the catchall user


### PostFix Mail Queue

http://www.tech-g.com/2012/07/15/inspecting-postfixs-email-queue/

Show mail queue
```
# Show mail queue
mailq
# flush mail queue
postsuper -d ALL
```


### SMTP Test

Send mail via telnet

https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx

or run
```
mail testmail@testin.com
```

This should result in mail ending up in /var/mail/catchall
