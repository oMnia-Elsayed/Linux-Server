Linux Server Configration - Project 6 - Full Stack Web Development Udacity Nanodegree 

My Project is on " http://18.130.184.123 " 

##Packages installed in the VN :-
	python , apache , postgreSQL database , flask , mod_wsgi 
	https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
	https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-ubuntu-18-04

##Steps to run my project :- 

1- you should make any instance (server , machine) , I used Amazon service lightsail (ubuntu)
	download the Key of your instance they will give you file to connect ssh local 
	in your machine with name : LightsailDefaultPrivateKey-eu-central-1.pem

2- SSH into the server from th terminal write 
	" ssh ubuntu@18.130.184.123 -p 22 -i ~/OMN/LightsailDefaultPrivateKey-eu-central-1.pem"

3- Update currently installed packages write " sudo apt-get update ,  sudo apt-get upgrade "

4- Change ssh port to 2200 write " sudo nano /etc/ssh/sshd_config "
	go inside th file and change "Port 22" to "Port 2200"
	uncoment "AuthorizedKeysFile"
	the restart ssh " sudo service ssh restart "

5- Configure the Uncomplicated Firewall (UFW) to only allow incoming connections 
	for SSH (port 2200), HTTP (port 80), and NTP (port 123).
	"sudo ufw allow 2200"
	"sudo ufw allow www"
	"sudo ufw allow ntp"
	"sudo ufw enable"
	"ssh -p 2200 root@18.130.184.123 -i ~/OMN/LightsailDefaultPrivateKey-eu-central-1.pem"

6- Now you can create a user called " grader " , write " sudo adduser grader " and the password is "12345"

7- we will give the user permission  , write " touch /etc/sudoers.d/grader " 
	" sudo nano /etc/sudoers.d/grader " 
	add this code " grader ALL=(ALL) NOPASSWD:ALL "

8- then we will setup a ssh key-pair for the user , write 
	"ssh-keygen" then call the fill " Omneya_keys "
	open the "Omneya_keys.pub" file , copy the contents , and paste it in ~/.ssh/authorized_keys 
	"mkdir .ssh"
	"chmod 700 .ssh"
	"nano .ssh/authorized_keys"
	"chmod 644 .ssh/authorized_keys"
	"sudo service ssh restart"
	Exit current connection
	"ssh -p 2200 -i ~/.ssh/Omneya_keys grader@18.130.184.123"

9- Configure the local timezone to UTC , write " sudo dpkg-reconfigure tzdata " choose UTC

10- Install and configure Apache , write " sudo service apache2 restart "

11- install git , write " sudo apt-get install git " 

12- My project on github https://github.com/oMnia2020/Item-Catalog.git  , write 
	"cd /var/www/html"
	"sudo git clone https://github.com/oMnia2020/Item-Catalog.git Item-Catalog"

13- write " sudo nano /etc/apache2/sites-available/itemconfig.conf "
	It should have the following components.

	<VirtualHost *:80>
                ServerName 18.130.184.123
                ServerAdmin admin@mywebsite.com
                WSGIScriptAlias / /var/www/html/itemcatalog.wsgi
                <Directory /var/www/html/Item-Catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/html/Item-Catalog/static
                <Directory /var/www/html/Item-Catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>

	and also edit this file for wsgi configurations: " sudo nano /var/www/html/itemcatalog.wsgi "
	
	to be like this:
	"
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/html/Item-Catalog/")

	from webserver import app as application
	application.secret_key = 'Add your secret key'

	"

	Once this is done, enable the site and restart Apache.

	"sudo a2ensite itemconfig"
	"sudo service apache2 reload"

The server should be live now. Visit the IP to check " http://18.130.184.123 "

References :

1-https://cloudacademy.com/blog/how-to-set-up-your-first-amazon-lightsail/
2-https://linuxacademy.com/howtoguides/posts/show/topic/12662-getting-started-with-lightsail-a-simple-vps-solution-from-aws

Written By : oMnia Elsayed ( omniamorsi2020@gmail.com )
