# Notes - Discourse


## Test Login

testuser@testdomain.com / test
testuser2@testdomain.com / test


## Outbound SMTP Config

The smtp configuration for development is actually stored under

  * config/environments/development.rb

development is specified via the environment variale RAILS_ENV <br>
to use a value other than localhost, comment out the block of code within config/environment.rb
that's designed to throw an error if a value other than localhost is used


## Inbound emails

  * https://meta.discourse.org/t/setup-incoming-emails-e-mail/42026

