# Setting up a POP3 server for inbound mail testing

## Overview

There's a couple of different ways for Discourse to receive mail <br>
Ether via a web API or polling a POP3 Account <br>
In order to test discourse for end to end as a mailing list, we need to add in a POP3 server to the test VM

  * https://meta.discourse.org/t/setup-incoming-emails-e-mail/42026
  * https://help.ubuntu.com/community/Dovecot

For the first step Postfix will do the job of catching mails sent in via smtp and delivering them to <br>
/var/mail/catchall

Dovecot by default is already configured to pick up from this and present via POP3 or IMAP <br>
Discourse will then poll the POP3 mailbo for new mails via Dovecot

## Dovecot

### Install DoveCot

For the discourse vagrant vm, dovecot should already be installed
but for info the apt-get command for it is
```
sudo apt-get install dovecot-imapd dovecot-pop3d
```


### Configure Dovecot

We shouldn't need to do any additional configuration from the default for Dovecot
Config files of note include

  * /etc/dovecot/dovecot.conf
  * /etc/dovecot/conf.d/10-mail.conf

The mail_location setting defines where to pull the mail from where postfix has put it <br>
But this should already be set to the correct setting
```
mail_location = mbox:~/mail:INBOX=/var/mail/%u 
```

### Starting Dovecot / POP3 Server

```
# Start the service
start dovecot
# Check it's running
ps -A | grep dovecot
# Test the connection
telnet localhost pop3
```

## Discourse POP3 Setup

TODO Configure Discourse for POP3
