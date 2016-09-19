# Windows Development - Remote Debugging

## Overview

One idea is to try and remotely debug a discourse.
Discourse itself won't run directly under windows because of the hassle of setting up ruby.
But we may be able to attach a debugger to it within Windows for single stepping the code etc for the development of plugins
I'm currently using RubyMine here from Jetbrains


## VM File Share

In order to alter the files running on the server we need to setup a file share on the Ubuntu VM.
We can then edit the files from RubyMine as a project

Open the Ubutunu file explorer, right click properties, selecting sharing <br>
when prompted to install software select yes, samba should be installed <br>
Make sure guest access / write access is ticked <br>
restart the machine / session

File share should be visible under
\\192.168.142.128\discourse


## Server Side debug

To get remote debugging to work with RubyMine we need to make a slight alteration to the GemFile

Comment out the byebug line, and add in the debase and ruby-debug-ide lines
```
  #gem 'byebug', require: ENV['RM_INFO'].nil?
  gem 'ruby-debug-ide'
  gem 'debase'
```

we also need to install ruby-debug-ide
```
sudo gem install ruby-debug-ide
```

To start server debugging
```
# The normal command to start the development server would be
bundle exec rails server -b 0.0.0.0
# Instead we can use this to start a remote debug session
bundle exec rdebug-ide --port 1234 --dispatcher-port 26162 --host 0.0.0.0 -- bin/rails s -b 0.0.0.0 -p 3000 -e development
```
The discourse server won't start untill the debugger is connected to from the outside, or Ctrl-C is pressed


## RubyMine Settings

The below links are details on using RubyMine for remote debugging

  * https://www.jetbrains.com/help/ruby/2016.2/remote-debugging.html
  * https://blog.jetbrains.com/ruby/2009/05/ruby-remote-debug/
  * http://bzzt.io/posts/running-the-rails-debugger-in-a-docker-container-using-rubymine
  * https://confluence.jetbrains.com/display/RUBYDEV/How+to+setup+and+run+ruby+remote+debug+session

Create a local copy of the source code on the dev macnine first

```
git clone https://github.com/discourse/discourse.git discourse
cd discourse
git checkout v1.7.0.beta4
```

Next we need to setup RubyMine

  * Open RubyMine
  * Select Open Project and browse to the directory where the git clone cloned to
  * To view the Project files use alt+1

To add in a new remote debug session

  * Select Run -> Edit Configurations
  * Click the + Add button and add a new "Ruby Remote Debug" entry

For the Settings

  * Give it a name of remotedebug1 or something similar
  * Remote Host: 192.168.142.128 (IP of VM Server)
  * Remote Port: 1234
  * Remote root folder: \\192.168.142.128\discourse (file share on VM)
  * Local Port: 26162
  * Local Root Folder: (path to the directory git cloned to)

To start the debugging session select

  * Run -> Debug (remotedebug1)

This should trigger the server on the VM to start
