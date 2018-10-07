# Ubuntu16.4
1  exit
2  mkdir .ssh
3  echo "ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAiFHFn8NEmk+AmKnaixNFzZ3XZzHY92V1sP6lTXdU/6cog1VF0TmGND7bKWSD7rEHDrf/SN3frXfOPMu81dJK8xdgfCnqsRD01u09c6mnF3cnKBM8gSiyRu6F2XsoTEugQnVtZR0DL0xv0RrHHiDJ4eFBv71rRFwaqNXc6P5Jq1d7YUbmIKq8ppDT+nhg8DRHWnMmvdyxPtVlF69915YhNSLHW54iDak7KS9F6vqBbXmAXPvzSYD+MLuw/WrPNAmhw8T6HQofLWtJ+Pu3/Re6wKu7qdUFZnx1hQ1u9S40tbcioAAKYJUhygkOWAFhp1uL+RA2N9hzHUEt2EfHI3S1Mw== username@example.com" >> .ssh/authorized_keys
4  exit
5  sudo apt-get update
6  sudo apt-get upgrade -y
7  sudo apt-get install build-essential virtualbox-dkms nano zip unzip curl man-db acpid git module-assistant
8  sudo reboot
9  sudo mount /dev/cdrom /media/cdrom
10  ls -la /media/cdrom
11  sudo sh /media/cdrom/VBoxLinuxAdditions.run --nox11
12  sudo reboot
13  lsmod
14  lsmod | grep vbox
15  ls -la /media
16  sudo usermod -a -G vboxsf om
17  cd /media/sf_sandbox/
18  logout


<!-- cd /media/sf_sandbox/ -->
getent group vboxsf
sudo usermod -a -G vboxsf www-data
sudoedit /etc/apache2/sites-available/vboxsf.conf


```
<VirtualHost *:80 *:8080>
    ServerName sandbox
    ServerAlias *.io


    LogLevel info
    ErrorLog ${APACHE_LOG_DIR}/vboxsf-error.log
    CustomLog ${APACHE_LOG_DIR}/vboxsf-access.log combined


    RewriteEngine On


    <Directory />
        Options FollowSymLinks
        AllowOverride All
    </Directory>


    <Directory /media/>
        Order allow,deny
        Allow from all
        Require all granted
    </Directory>


    <Location /server-status>
        SetHandler server-status
        Order allow,deny
        Allow from all
        Require all granted
    </Location>


    UseCanonicalName Off
    VirtualDocumentRoot /media/sf_%1
</VirtualHost>
```


sudoedit /etc/apache2/ports.conf
```
Listen 8080
```


sudo a2ensite vboxsf
sudo a2dissite 000-default
sudo a2enmod rewrite vhost_alias
sudo service apache2 restart


sudoedit /etc/php/7.0/mods-available/phpcustom.ini
```
; Custom shared config
; priority=01
error_reporting=E_ALL
display_errors=On
display_startup_errors=On
error_log=/var/log/php_errors.log
log_errors_max_len=0
memory_limit=256M
post_max_size=100M
upload_max_filesize=100M
```
sudo phpenmod phpcustom
sudo touch /var/log/php_errors.log
sudo chown www-data: /var/log/php_errors.log
sudo service apache2 restart
<!-- Check for php error showing -->
sudo apt-get install php-mcrypt php-intl php-sqlite3 php-mbstring php-xml php-gd -y
sudo phpenmod mbstring simplexml
sudo service apache2 restart
tail /var/log/php_errors.log


/* PHP is ready  **/


/** Mailcatcher service  **/
sudo apt-get install libsqlite3-dev ruby-dev -y
sudo gem install mailcatcher
mailcatcher --help


21  sudoedit /lib/systemd/system/mailcatcher.service


```
    [Unit]
    Description=MailCatcher Service


    [Service]
    Type=simple
    ExecStart=/usr/local/bin/mailcatcher --foreground --ip 0.0.0.0


    [Install]
    WantedBy=multi-user.target
```
22  sudo service mailcatcher start
23  sudo systemctl enable mailcatcher.service
24  sudo service mailcatcher status
25  sudoedit /etc/php/7.0/mods-available/mailcatcher.ini
```
    sendmail_path=/usr/local/bin/catchmail
    sendmail_from=mailcatcher@sandbox.io
```
26  sudo phpenmod mailcathcer
28  sudoedit /etc/php/7.0/mods-available/mailcatcher.ini
```
    sendmail_path=/usr/local/bin/catchmail
    sendmail_from=mailcatcher@sandbox.io
```
29  sudo phpenmod mailcatcher
33  sudo service apache2 restart
34  php -i | grep sendmail
36  sudoedit /etc/mysql/mysql.cnf
```
[mysqld]
collation-server = utf8_unicode_ci
character-set-server = utf8
bind-address = 0.0.0.0
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 2
```
37  sudo service mysql restart
38  cd /var/log/mysql
39  ls -la
40  tail error.log
41  sudo chgrp adm slow.log
42  tail slow.log
43  sudoedit /etc/mysql/mysql.cnf
44  mysql -uroot -proot
45  sudo apt-get install php-xdebug
46  sudo service apache2 restart
52  sudo apt-get install memcached php-memcached -y
  mysql -uroot -proot
53  sudo reboot
54  sudoedit /etc/mysql/mysql.cnf
55  cd /media/sf_sandbox/
56  getent group vboxsf
57  sudoedit /etc/apache2/sites-available/vboxsf.conf
58  sudoedit /etc/apache2/ports.conf
59  sudo a2ensite vboxsf
60  sudo a2dissite 000-default
61  sudo a2enmod rewrite vhost_alias
62  sudoedit /etc/php/7.0/mods-available/phpcustom.ini
63  sudo phpenmod phpcustom
64  sudo touch /var/log/php_errors.log
65  sudo chown www-data: /var/log/php_errors.log
66  sudo service apache2 restart
67  sudoedit /etc/php/7.0/mods-available/xdebug.ini
68  mkdir /tmp/cachegrind
69  sudo chown www-data: /tmp/cachegrind
70  sudo service apache2 restart
71  sudo apt-get install graphviz
72  cd /media/sf_sandbox/
73  git clone https://github.com/jokkedk/webgrind.git
75  sudo phpdismod xdebug
76  sudo service apache2 restart
78  sudo apt-get install memcached php-memcached -y
79  sudo service apache2 restart
80  echo "stats"
81  echo "stats" | netcat localhost 11211
82  php -i grep memcached
84  sudo apt-get install redis-server php-redis -y
85  sudo service apache2 restart
86  php -i | grep redis
87  redis-cli
91  curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer

**Upgrade to php 7.2**
150  apt-get install python-software-properties
151  sudo apt-get install python-software-properties
152  sudo add-apt-repository ppa:ondrej/php
153  add-apt-repository ppa:ondrej/php
154  sudo apt-get install -y software-properties-common
155  sudo add-apt-repository ppa:ondrej/php
156  apt-get update
157  sudo apt-get update
158  apt-get install php7.2
159  sudo apt-get install php7.2
160  php -v
161  apt-get install php-pear php7.2-curl php7.2-dev php7.2-gd php7.2-mbstring php7.2-zip php7.2-mysql php7.2-xml
162  sudo apt-get install php-pear php7.2-curl php7.2-dev php7.2-gd php7.2-mbstring php7.2-zip php7.2-mysql php7.2-xml
163  sudo service apache2 restart
164  php -v
165  ls -la /etc/php
166  cd /etc/php/7.2/mods-available
167  ls -la
168  sudoedit phpcustom.ini
169  sudo a2dismod php7.0
170  sudo a2enmod php7.2
171  sudo service apache2 restart
172  sudo phpenmod phpcustom

Reinstall guest edition
To use “`Shared Folders`” in  `VirtualBox`, the user should install  `VirtualBox`  guest additions:
```
Unable to insert the virtual optical disk C:\Program Files\Oracle\VirtualBox\VBoxGuestAdditions.iso into the machine CentOS.
```
It means the  `Devices`  ->  `CD/DVD DEvices`  already has  `ISO`  file. Please inject it, and try  `Insert Guest Additions CD image`  again.
(2) Mount the  `ISO`  file:

```
mount /dev/cdrom /mnt

```

(3) Install the  `VirtualBox`  guest additions (Take  `Linux`  as an example):

```
cd /mnt
./VBoxLinuxAdditions.run
```