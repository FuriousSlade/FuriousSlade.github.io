Title:Install Redmine on Centos.
Date:2015-05-13
Category:Blog
Tags:Redmine
Summary:Install Redmine on Centos.搭建Redmine的过程。

# Install Redmine on Centos 65
## dependencies packages

These are the basic software packages for environment settings and utility tools to compile other packages in the next section.
Copy the block command and execute in the Putty Windows :
This is a long command line, copy all and implementation.

	yum -y install nano zip unzip libyaml-devel zlib-devel curl-devel openssl-devel httpd-devel apr-devel apr-util-devel mysql-devel gcc ruby-devel gcc-c++ make postgresql-devel ImageMagick-devel sqlite-devel perl-LDAP mod_perl perl-Digest-SHA

![](http://ww2.sinaimg.cn/large/0067jSM2jw9es2omiwa33j30kj0c8tft.jpg)

## Install Apache and MySQL

Apache is a server application for communicating over the HTTP protocol. Apache runs on operating systems such as Unix, Linux, Microsoft Windows, and other operating systems.
Apache play an important role in the development of the internet and the world wide web.
MySQL is the database management free open source most popular on the world, MySQL has high speed, stability and ease of use, portability, operating on multiple operating systems offer a large system is very powerful utility functions.
With the speed and high security, MySQL is well suited for applications that access databases on the internet.
Use the following command to install :

`yum -y install httpd mysql mysql-server`

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omiu2i7j30kj0c80x0.jpg)

Allow start services when OS boot :
chkconfig httpd onchkconfig mysqld onservice httpd startservice mysqld start
Set the password for MySQL
/usr/bin/mysql_secure_installation
Because we not have a password for the root account so you press Enter to skip.
Enter current password for root (enter for none):
Select Yes to set the password for the MySQL root account.
Set root password? [Y/n] y
Enter and confirm your password, remove the anonymous user, select Yes
Remove anonymous users? [Y/n] y
Allow remote login to MySQL as root account, select No.
Disallow root login remotely? [Y/n] n
Delete the test database, select Yes
Remove test database and access to it? [Y/n] y
Reload privilege tables, select Yes
Reload privilege tables now? [Y/n] y

## Turn off SELinux

SELinux is a security feature advanced for Linux operating system, when installing the system you need to turn off this feature to get the process done smoothly, after successful you can turn on back if you want.
nano /etc/selinux/config
Change the file content :
SELINUX=disabled

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omiub74j30ku0ctq8k.jpg)

Press CTRL + O to save the file and press CTRL + X to exit.
## Set up the Hostname

By default when installing a new OS Centos not set the hostname, so we need to setting with the command :
nano /etc/hosts

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omjawb7j30ku0ctjui.jpg)

Add your domain name or host name that you set on both the command line, save the file and exit, the server name will be changed when restarting.
## Configuring the Firewall

We do not want to turn off the firewall because it's quite important, so you need to add rules to allow port 80 for HTTP and port 443 for HTTPS.
In the Centos OS, you can configuration firewall by editing files iptables and ip6tables.
nano /etc/sysconfig/iptables

![](http://ww2.sinaimg.cn/mw690/0067jSM2jw9es2omiuqqhj30k80ctwla.jpg)

Press Enter to create a new line after the line of port 22, copy the following two commands and right click on the window to the Paste command.
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
Press CTRL + O to save the file and press CTRL + X to exit.
The same applies for IP6 firewall :
nano /etc/sysconfig/ip6tables
Add these lines to the file.
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
After you finish editing both files, run the commands to apply the new rules for firewall.
/etc/init.d/iptables restart/etc/init.d/ip6tables restart
Allow turn on the firewall when reboot the operating system.
chkconfig iptables onchkconfig ip6tables on
Finally, we need to restart the system to apply the changes to the SELinux and Hostname.
reboot

## Install Ruby

Ruby is a object-oriented programming language, capable of reflection. Syntax inherited from Ada and Perl with object-oriented features of Smalltalk, and also share some features with Python, Lisp, Dylan and CLU, Ruby is a single phase interpreter.
Ruby provides programming patterns, including functional programming, object-oriented, imperative, reflective, it uses dynamic variable and automatic memory management.
Install Ruby interpreter with version management program RVM.

`\curl -L https://get.rvm.io | bash`

After successful, we will launch RVM

`source /etc/profile.d/rvm.sh`

The following command will list the versions of Ruby to install :

`rvm list known`

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omjbn80j30l50ctjud.jpg)

We choose the stable version [ruby-] 1.9.3 [-p545], and execute the following command :

`rvm install 1.9.3`

![](http://ww3.sinaimg.cn/mw690/0067jSM2jw9es2omiudd4j30l50cttfj.jpg)

The installation process is pretty long time, but you do not need any intervention, after successful, you check with the following command :

`ruby -v`

## Install Rubygems

Rubygems is a Ruby's packages management program, very popular in applications written by Ruby language and the Ruby On Rails framework.

`yum -y install rubygems`

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omiu51aj30l50ctwkf.jpg)

## Install Passenger

The full name of the Passenger is Phusion Passenger, known as mod_rails or mod_rack, it is a web application intergrate with Apache and it can operate as a standalone web server support for the Ruby On Rails applications.
Execute the following commands :

`gem install passengerpassenger-install-apache2-module`

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omiwh1cj30l50ctn2n.jpg)

After completed, we copy a notification block in the window to create the configuration file in the next steps (select block notification and press C to copy).
LoadModule passenger_module /usr/local/rvm/gems/ruby-1.9.3-p545/gems/passenger-4.0.37/buildout/apache2/mod_passenger.so 
    
     PassengerRoot /usr/local/rvm/gems/ruby- 
     1.9.3-p545/gems/passenger- 
     4.0.37 PassengerDefaultRuby /usr/local/rvm/gems/ruby- 
     1.9.3-p545/wrappers/ruby 
    
Create a new virtual host file for Passenger :

`nano /etc/httpd/conf.d/passenger.conf`

Paste the command blocks into the empty file and save it, then restart the Apache service.

`service httpd restart`

## Create Database for Redmine

Use MySQLAdmin to create an empty database for Redmine, saved password to fill in the configuration file in the next steps.

	mysql --user=root --password=root_password_mysqlcreate database redmine_db character set utf8;create user 'redmine_admin'@'localhost' identified by 'your_new_password';grant all privileges on redmine_db.* to 'redmine_admin'@'localhost';quit;

![](http://ww4.sinaimg.cn/mw690/0067jSM2jw9es2omjc7poj30l50ctq7x.jpg)
## Install Redmine

Redmine is a main program of the project management system, we will download and install the program from the website of Redmine.
Download Redmine version 2.5.x to directory "/var/www" on the Centos OS.

`cd /var/wwwwget http://www.redmine.org/releases/redmine-2.5.0.tar.gz`

Extract the folder and rename directory

`tar xvfz redmine-2.5.0.tar.gzmv redmine-2.5.0 redminerm -rf redmine-2.5.0.tar.gz`

Configuring the Database
The next, we need to configure the database was created from the above steps.

`cd /var/www/redmine/configcp database.yml.example database.ymlnano database.yml`

![](http://ww1.sinaimg.cn/mw690/0067jSM2jw9es2omixlf0j30l50ctn2r.jpg)

Enter name for database, enter username and password of the database. Press CTRL + O to save the file and CTRL + X to exit.

## Setting up Rails

Install the package library support for Rails using the Bundle.

	cd /var/www/redminegem install bundlerbundle installrake generate_secret_token

![](http://ww4.sinaimg.cn/mw690/0067jSM2jw9es2omiud01j30l50ctwjt.jpg)

The next, we create the database table for the Redmine application.
RAILS_ENV=production rake db:migrateRAILS_ENV=production rake redmine:load_default_data
## Activate FCGI

	cd /var/www/redmine/publicmkdir plugin_assetscp dispatch.fcgi.example dispatch.fcgicp htaccess.fcgi.example .htaccess

## Setting up Apache and FastCGI

	cd /var/www/rpm --import https://fedoraproject.org/static/0608B895.txtwget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpmrpm -ivh epel-release-6-8.noarch.rpmyum -y install mod_fcgidrm -rf epel-release-6-8.noarch.rpm

## Creating Files Directory

This directory contains data files generated during the operation of Redmine as document or image file, we create a new directory in the "/opt".

	mkdir -p /opt/redmine/fileschown -R apache:apache /opt/redminecd /var/www/redmine/configcp configuration.yml.example configuration.ymlnano configuration.yml

![](http://ww4.sinaimg.cn/mw690/0067jSM2jw9es2omiu1kej30l50ct0zk.jpg)

Enter the directory path containing the data files you just created in the previous step into the line "attachments_storage_path".
Note: You must add a space at the begin of the path "/opt/redmine/files" after character ":"

## Configuring Email

Another very important function of Redmine is using email to notify members when the contents of each project changes, Redmine can use many different methods to send email that is Sendmail, SMTP, GMail ...
To configure the email we will edit the configuration file.

`nano /var/www/redmine/config/configuration.yml`

The simplest is you use features of the default SendMail in the Centos OS by settings :

`email_delivery:delivery_method: :sendmail`

Note : Do not use the Tab key to indent when editing the configuration file, you need to use the space bar on the keyboard.
If you use GMail's SMTP, you need to register an email account with the login methods used password normal and disable two-step authentication by smart phone.
Enter your Gmail account as below :

	email_delivery:delivery_method: :smtp smtp_settings: enable_starttls_auto: true address: "smtp.gmail.com" port: 587 domain: "smtp.gmail.com" authentication: :plain user_name: "your_email@gmail.com" password: "your_password"

Save the file configuration and exit.

## Create Virtual Host for Redmine

Create an Apache configuration file for the Redmine application at the port 80.

`nano /etc/httpd/conf.d/redmine.conf`

Copy the text below and paste into the editor window, note the information to change your domain name.
80> ServerName your_domain ServerAdmin your_domain 

     @domain.com DocumentRoot /var/www/redmine/public/ ErrorLog logs/redmine_error_log 
     "/var/www/redmine/public/"> Options Indexes ExecCGI FollowSymLinks Order allow,deny Allow from all AllowOverride all 
     
Save the file configuration and exit.

## Running Redmine

Before execute Redmine in the first time, we must permission for the directory installed Redmine and restart Apache service.

	cd /var/wwwchown -R apache:apache redminechmod -R 755 redmineservice httpd restart

![](http://ww2.sinaimg.cn/mw690/0067jSM2jw9es2omja76jj30ip0b5t9p.jpg)

Redmine will run at the following address URL :
http://your-domain
Login to system with an administrator account : admin / admin
You can change your password after successful login.
We can see Redmine has running but very primitive, in the next steps we will install the support plugins and customized Redmine to use professional.

## Install Subversion

Subversion, also known as SVN, it is a version management system is very popular and easy to use, most programmers can use it competently.
We need to create a folder to store data for Redmine, the following command creates a directory and permissions for the Apache service.

	mkdir -p /opt/repositories/svnchown -R apache:apache /opt/repositories/chmod 0755 /opt/repositories

The following command install Subversion and the packages :

	yum install mod_dav_svn subversion subversion-ruby

![](http://ww4.sinaimg.cn/mw690/0067jSM2jw9es2omjdnw1j30l50ctn1r.jpg)

The next, we will create a directory and copy the file called "Redmine.pm", it responsible for interface data repository with Redmine and it is written by Perl language programming.
	mkdir /usr/lib64/perl5/vendor_perl/Apacheln -s /var/www/redmine/extra/svn/Redmine.pm /usr/lib64/perl5/vendor_perl/Apache/Redmine.pm

Note : If you are using 32 bit Centos, change the path "/usr/lib64" to "/usr/lib"
After installation is complete, from the Redmine application, go to the page Administration> Settings> Repositories to check the results.
To support the authentication and access to data repository for each member, we need to create a virtual host for the Apache service can access Redmine database.

`nano /etc/httpd/conf.d/subversion.conf`

Add the following lines to the end and still retain the old contents of the file :
PerlLoadModule Apache::Redmine 
    
      DAV svn SVNParentPath 
     "/opt/repositories/svn" SVNListParentPath on Order deny,allow Deny from all Satisfy any LimitXMLRequestBody 
     0 SVNPathAuthz off PerlAccessHandler Apache::Authn::Redmine::access_handler PerlAuthenHandler Apache::Authn::Redmine::authen_handler AuthType Basic AuthName 
     "Subversion Repository" Require valid-user RedmineDSN 
     "DBI:mysql:database=redmine_db;host=localhost:3306" RedmineDbUser 
     "redmine_admin" RedmineDbPass 
     "your_password_database_redmine"

Note : You need to change the password in the "RedmineDbPass" to correct the database password of Redmine.
At this point, we have finished the basic settings for Redmine.
Thank you!