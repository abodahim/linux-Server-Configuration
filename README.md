# linux Server Configuration
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.
* IP address: 52.28.0.135
* Accessible SSH port: 2200
## Prerequisites
To complete this project, you'll need a Linux server instance. We recommend using [Amazon Lightsail](https://lightsail.aws.amazon.com/ls/webapp/home/instances) for this. If you don't already have an Amazon Web Services account, you'll need to set one up. Once you've done that, here are the steps to complete this project.
There are a few things you need to do when you create your server instance.
#### 1.Get your server:
* Log in:
First, log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.[Amazon Web Services login page](https://d17h27t6h515a5.cloudfront.net/topher/2017/February/589e45f9_screen-shot-2017-02-10-at-14.59.35/screen-shot-2017-02-10-at-14.59.35.png)
* Create an instance:
Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.[create an instance.](https://d17h27t6h515a5.cloudfront.net/topher/2017/February/589e45a0_screen-shot-2017-02-10-at-14.58.17/screen-shot-2017-02-10-at-14.58.17.png)
* Choose an instance image Ubuntu:
Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications.
For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here. First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.[ Choose an instance image: Ubuntu](https://d17h27t6h515a5.cloudfront.net/topher/2017/February/589e43c6_screen-shot-2017-02-10-at-14.46.15/screen-shot-2017-02-10-at-14.46.15.png)
* Choose your instance plan:
The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, the lowest tier of instance is just fine. And as long as you complete the project within a month and shut your instance down, the price will be zero. [Choose your instance plan](https://d17h27t6h515a5.cloudfront.net/topher/2017/February/589e43ca_screen-shot-2017-02-10-at-14.46.35/screen-shot-2017-02-10-at-14.46.35.png)
* Give your instance a hostname:
Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. Your instance's name will be visible to you and to the project reviewer. [Give your instance a hostname](https://d17h27t6h515a5.cloudfront.net/topher/2017/February/589e43ce_screen-shot-2017-02-10-at-14.47.08/screen-shot-2017-02-10-at-14.47.08.png)
* Wait for it to start up: 
It may take a few minutes for your instance to start up.
* It's running; let's use it!
Once your instance has started up, you can log into it with SSH from your browser.
The public IP address of the instance is displayed along with its name.For example `52.28.0.135`.
Note: When you set up OAuth for your application, you will need a DNS name that refers to your instance's IP address. You can use the [xip.io](http://xip.io/) service to get one; this is a public service offered for free by Basecamp. For instance, the DNS name `52.28.0.135.xip.io` refers to the server above.
Explore the other tabs of this user interface to find the Lightsail firewall and other settings. You'll need to configure the Lightsail firewall as one step of the project.
When you SSH in, you'll be logged as the ubuntu user. When you want to execute commands as root, you'll need to use the sudo command to do it.
* Project time:
Now that you have a working instance, you can get right into the project!
#### 2.Create new user named grader and give it the permission to sudo:
* login to the server with ssh  and Create new user named grader 
Run `sudo adduser grader` to create a new user named grader
* give user the permission to sudo
Create a new file in the sudoers directory with `sudo nano /etc/sudoers.d/grader`
Add this line in file  `grader ALL=(ALL:ALL) NOPASSWD:ALL` to give user grader it the permission to sudo.
* you can connection to instance server in browser using ssh or coonect in terminal local machine run this command `ssh grader@52.28.0.135 -p 22`
* if you found this error `Permission denied (publickey)` after try connect in local machine, Follow the following steps to solve this problem:
* connect instance server in browser 
* then run `sudo nano /etc/ssh/sshd_config`
* change this line `PermitRootLogin prohibit-password` to `PermitRootLogin yes` 
* change this line `PasswordAuthentication no` to `PasswordAuthentication yes`
* save and run `sudo service ssh restart` , then try to connect `ssh grader@52.28.0.135 -p 22`.
#### 3.Secure your server
* Update all currently installed packages:
Download package lists with `sudo apt-get update`
Fetch new versions of packages with `sudo apt-get upgrade`
* Change the SSH port from 22 to 2200:
Run `sudo nano /etc/ssh/sshd_config` Change the port from 22 to 2200
then run `sudo service ssh restart`
* Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):
* `sudo ufw allow 2200/tcp`
* `sudo ufw allow 80/tcp`
* `sudo ufw allow 123/udp`
* `sudo ufw enable`
* you can Configure Firewall in browser instance, Explore browser instance and choose Networking, then configure the Lightsail firewall.
* **Warning:** When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.
#### 4.Change local timezone to UTC
* Run `sudo dpkg-reconfigure tzdata` and then none of the above and will show UTC now choose it.
#### 5.Configure key-based authentication for grader user
* Create an SSH key pair for grader using the ssh-keygen tool. run this command in terminal local machine (**not in server**) `ssh-keygen` to create key pair
* run this command to connection to user `ssh grader@52.28.0.135 -p 2200`
* Run this command in server `sudo mkdir /home/grader/.ssh` then run this `sudo nano /home/grader/.ssh/authorized_keys` and paste your public key.
#### 6.Disable ssh login for root user
* Run `sudo nano /etc/ssh/sshd_config`
Change PermitRootLogin without-password line to PermitRootLogin no
* Restart ssh with `sudo service ssh restart`
* Now you are only able to login using `ssh -i ~/.ssh/linuxCourse -p 2200 grader@52.28.0.135`
#### 7.Install Apache
* run `sudo apt-get install apache2`
#### 8.Install mod_wsgi
* Run `sudo apt-get install libapache2-mod-wsgi python-dev`
* Enable mod_wsgi with `sudo a2enmod wsgi`
* Start the web server with `sudo service apache2 start`
#### 9.Clone from Github
* Install git using: `sudo apt-get install git`
* `cd /var/www`
* run `sudo mkdir catalog`
* Change owner of the newly created catalog folder `sudo chown -R grader:grader catalog`
* `cd /catalog`
* Clone your project from github `git clone https://github.com/abodahim/catalog.git catalog`
* Create a catalog.wsgi file run `sudo nano catalog.wsgi`, with this content:
``` 
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'
```
* `cd catalog`
* Rename `application.py` to `__init.py__` run this `mv application.py __init__.py`
#### 10.Install virtual environment
* First install pip with this command `sudo apt-get install python-pip`
* Install the virtual environment `sudo pip install virtualenv`
* Make sure you are in here `cd /var/www/catalog`
* Create a new virtual environment with `sudo virtualenv venv`
* Activate it with `source venv/bin/activate`
* Change permissions `sudo chmod -R 777 venv`
#### 11.Install Flask and other dependencies
* Install Flask `pip install Flask`
* Install all others project dependencies `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils requests`
#### 12.Update path of client_secrets.json file
* `sudo nano /var/www/catalog/catalog/__init__.py`
* Change all path `client_secrets.json` in this file `__init__.py` to path `/var/www/catalog/catalog/client_secrets.json`
#### 13.updated url and domain in google console 
* go to (https://console.cloud.google.com/apis/credentials)
* add `xip.io` in Authorized domains
* add `http://52.28.0.135.xip.io` in  Authorized JavaScript origins
* add `	http://52.28.0.135.xip.io/login` and `	http://52.28.0.135.xip.io/gconnect` in Authorized redirect URIs
* Save and Download `client_secrets.json` file
* run `sudo nano /var/www/catalog/catalog/client_secrets.json` to replace the contents of the old file `client_secrets.json` with the contents of the new file `client_secrets.json` you downloaded.
#### 14.Configure a new virtual host
* Create a new file with this : `sudo nano /etc/apache2/sites-available/catalog.conf`
* Put this code:
```
<VirtualHost *:80>
    ServerName 52.28.0.135
    ServerAlias http://52.28.0.135.xip.io
    ServerAdmin admin@52.28.0.135
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/ven$
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
* enable an apache2 site / virtual host
 `sudo a2ensite /etc/apache2/sites-available/catalog.conf`
* disable an apache2 default site / virtual host
`sudo a2dissite /etc/apache2/sites-available/000-default.conf`
#### 15.Install and setup PostgreSQL
* `sudo apt-get install libpq-dev python-dev`
* `sudo apt-get install postgresql postgresql-contrib`
* `sudo su - postgres`
* `psql`
* `CREATE USER catalog WITH PASSWORD 'password';`
* `ALTER USER catalog CREATEDB;`
* `CREATE DATABASE catalog WITH OWNER catalog;`
* `\c catalog`
* `REVOKE ALL ON SCHEMA public FROM public;`
* `GRANT ALL ON SCHEMA public TO catalog;`
* `\q`
* `exit`
* now you need to change the create engine line in your `__init__.py` and `database_setup.py` and `lotsofmenus.py` to: 
`engine = create_engine('postgresql://catalog:password@localhost/catalog')`
* Then run `python /var/www/catalog/catalog/database_setup.py` and `python /var/www/catalog/catalog/lotsofmenus.py`
#### 16.Restart Apache
* `sudo service apache2 restart` or `sudo service apache2 reload`
#### 17.Visit [http://52.28.0.135.xip.io/](http://52.28.0.135.xip.io/)
#### Note :
* If errors occur after visit in URL, you can search for these errors in the log file, run `sudo nano -f /var/log/apache2/error.log`
## More Information
* [ Amazon Lightsail ](https://lightsail.aws.amazon.com/ls/webapp/home/instances)
* [SSH: How to access a remote server and edit files](https://youtu.be/HcwK8IWc-a8)
* [Intro to TMux](https://youtu.be/hZ0cUWWixqU)
* [Deploying a Flask App with Heroku](https://youtu.be/5UNAy4GzQ5E)
* [install postgresql ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)
* [SQLAlchemy](https://docs.sqlalchemy.org/en/rel_0_9/orm/query.html)
* [Welcome to Flask](http://flask.pocoo.org/docs/1.0/)
* [Tools for Web Developers](https://developers.google.com/web/tools/chrome-devtools/?utm_source=dcc&utm_medium=redirect&utm_campaign=2018Q2)
* [Getting started with HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/Getting_started)
* [Intro to HTML and CSS](https://classroom.udacity.com/courses/ud197/lessons/3483858580/concepts/35153985360923https://classroom.udacity.com/courses/ud001/lessons/6987421963/concepts/74229205890923)
* [JSON](https://en.wikipedia.org/wiki/JSON)
* [Create an OAuth Server](https://lepture.com/en/2013/create-oauth-server)
* [Session hijacking](https://en.wikipedia.org/wiki/Session_hijacking)
* [OAuth 2.0 Playground](https://developers.google.com/oauthplayground/)
* [console developers](https://console.developers.google.com/apis/dashboard?project=companycars-225023)
* [PEP8](https://pypi.org/project/pep8/)
* [Linux Command Line Basics](https://classroom.udacity.com/courses/ud595-nd)
## Author
**abdulrahman ali alfaifi**
## Acknowledgments
* Hat tip to anyone whose code was used
* Inspiration
* etc











  