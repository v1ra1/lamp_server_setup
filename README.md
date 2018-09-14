# **STEPS TO SETUP LOCAL ENVIROMENT**

CREATE FOLDER IN DOCUMENTS CALLED "Github"

# DOWNLOAD CENTOS 7 MINIMAL - CLICK MINIMAL
https://www.centos.org/download/

# DOWNLOAD MOBAXTERM FOR YOUR SFTP/SSH CLIENT
https://mobaxterm.mobatek.net/download-home-edition.html

# DOWNLOAD VIRTUAL BOX
https://www.virtualbox.org/wiki/Downloads

New->Name: Twerk,Type: Linux, Version: RedHat (64-bit) (If you do not see this option. You need to enable virtualization in your bios)

->Minimum: 1g Ram,Option: Create a virtual hard disk now,Minimum: 10g, VDI, Dynamically aloc

Right click for settings. Network->Adapter 1-> Attached to (Bridged Adapter)->Advanced->Allow All->Cable Connected

Shared Folder->Add Folder->(Path to "Github" created in documents)->Folder Name: websites->Auto-mount (Leave read only unchecked)

Storage->Controller:IDE SUB->Optical Drive (Click the cd icon)-> Find the Centos IOS file->Check live CD/DVD

NOW CLICK OK, YOU ARE DONE WITH THE VIRTUAL BOX SETTINGS

CLICK START ON THE OS YOU JUST CREATED NOW... IN THE TIME BEING WHILE YOU GO THROUGH THE SETUP DO THE FOLLOWING BELOW...

INSTALLING THE OS SHOULD BE STRAIGHT FORWARD CAUSE IT WALKS YOU THROUGH EVERYTHING. MAKE SURE YOU CONNECT TO YOUR NETWORK IN THE INSTALL

ONCE YOU HAVE YOUR OS INSTALLED YOU TECHNICALLY CAN SWITCH OVER TO MOBAXTERM. I LIKE MOBAXTERM CAUSE IT ALLOWS YOU TO BE ABLE TO PASTE

TYPE IFCONFIG IN VIRTUAL BOX ONCE YOU GET TO THE CMD AND THEN TYPE THAT IP IN MOBAX TERM WITH ROOT AS YOUR USERNAME. IT WILL ASK FOR PASSWORD

JUST TYPE IT IN AND WALA YOU WILL ALWAYS HAVE ACCESS TO IT WHILE VIRUTALBOX IS RUNNING NOW.

# DOWNLOAD MYSQL WORKBENCH
https://dev.mysql.com/downloads/workbench/

# DOWNLOAD ATOM (IDE)
https://atom.io/download/windows_x64

Atom->Packages->Settings->Install->Search

THEMES:
SETI
MONAKI

PACKAGES:
- color-picker
- file-icons
- minimap
- pigments

# SETTING UP YOUR OS TO CONNECT TO THE NETWORK AND CREATE A LAMP SERVER

sudo yum install epel-release

rpm -Uvh http://rpms.remirepo.net/enterprise/remi-release-7.rpm

rpm -Uvh http://repo.mysql.com/mysql-community-release-el7-7.noarch.rpm

yum --enablerepo=remi-php72 install php

yum --enablerepo=remi-php72 search php

yum --enablerepo=remi-php72 install php-mysql php-xml \
php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt

yum --enablerepo=epel,remi install httpd

systemctl start httpd.service

systemctl enable httpd.service

sudo yum install mysql-server

systemctl start mysqld.service

sudo systemctl enable mysqld

sudo systemctl start mysqld

firewall-cmd --permanent --add-service=mysql

firewall-cmd --reload

(YOU WILL BE CREATING YOUR MYSQL PASSWORD HERE MAKE SURE YOU SAVE IT!!!!)

mysql_secure_installation

firewall-cmd --permanent --zone=public --add-service=http

firewall-cmd --permanent --zone=public --add-service=https

firewall-cmd --reload

ONCE YOUR MYSQL IS INSTALLED TYPE THE FOLLOWING TO GET IN

mysql -uroot -p

YOU WILL BE PROMPTED FOR YOU PASSWORD NOW...

ONCE YOU ARE LOGGED IN TYPE THE FOLLOWING BELOW BUT REPLACE [MYSQL PASSWORD] with your password for your database

GRANT ALL PRIVILEGES ON [ASTRIX].[ASTRIX] TO 'root'@'%' IDENTIFIED BY '[MYSQL PASSWORD]';

FLUSH PRIVILEGES;

YOU CAN NOW INSTALL THE MYSQL WORKBENCH. TYPE ifconfig to figure out what your ip address is to login to your mysqlworkbench

it would be something like: 

EXAMPLE---

HOST: 192.168.1.20

USERNAME: root (IT WILL ALWAYS BE ROOT UNLESS YOU CREATE ANOTHER USER)

PASSWORD: [MYSQL PASSWORD]

TYPE exit if you havent already in mysql so you can get back to your OS cmd

# LETS MOUT OUR SHARED FOLDER NOW

yum update;yum install gcc kernel-devel make bzip2 nano

!!!!Click: Devices/Install Guest Additions (THIS IS IN THE VIRTUAL BOX MENU)!!!

REBOOT

mkdir /cdrom;mount /dev/cdrom /cdrom;/cdrom/VBoxLinuxAdditions.run

# CREATE SH FILE

mkdir /var/www/websites;sudo nano /var/www/mtweb.sh

PASTE THIS IN THE FILE AND THEN PRESS CTRL+X THEN Y

#!/bin/bash

echo "Mounting Websites.";

systemctl start httpd;

mount -t vboxsf websites /var/www/websites;

# LETS CHANGE SOME CONFIGS NOW

sudo nano /etc/httpd/conf/httpd.conf

Change DocumentRoot "/var/www/html" to DocumentRoot "/var/www/websites"

Change <Directory "/var/www/html"> to <Directory "/var/www/websites">

Change AllowOverride None to AllowOverride All

# LETS CONFIGURE PHP NOW

sudo nano /etc/php.ini

Search "Short" and then turn ShortTag On

apachectl restart

# LETS TURN SELINUX OFF

setenforce 0

nano /etc/sysconfig/selinux

CHANGE "SELINUX=enforcing" to "SELINUX=disabled"

# THINGS YOU SHOULD INSTALL 

yum install git wget python2-pip unzip zip

# SETTING UP STATIC IP ADDRESS

sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3

IPADDR=192.168.1.200

NETMASK=255.255.255.0

GATEWAY=192.168.1.1

DNS1=1.0.0.1

DNS2=1.1.1.1

DNS3=8.8.4.4

service network restart

apachectl restart

