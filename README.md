# lin_lab2
1.2
dpkg -l

dpkg -l mc

1.4
sudo iptables -I INPUT -p tcp --dport 22 -s 192.168.150.0/24 -j ACCEPT

sudo iptables -A INPUT -p tcp --dport 22 -j REJECT

Tábla elmentése ezután: sudo /sbin/iptables-save

1.5

iptables -I INPUT -p icmp --icmp-type echo-request -j DROP // -I ez itt egy nagy i betű

Tábla elmentése ezután: /sbin/iptables-save

1.6
adduser mekkelek

Jelszó a NEPTUN kódod. Nyomj utána sok entert, aztán a végén Y.

Megtekintés:

nano /etc/passwd

1.7
sudo nano /etc/sudoers vagy sudo visudo

A # User privilege specification részben a root ALL=(ALL:ALL) ALL alá vedd fel:

mekkelek ALL=(ALL:ALL) ALL

Aztán ctrl + x, utána Y és enter

1.8

mc

nano /etc/ssh/sshd_config

#PermitRootLogin –sort írd át így (ne legyen előtte a # jel): PermitRootLogin no

sudo service sshd restart vagy /etc/init.d/ssh restart 

1.10

sudo apt-get install mysql-server

sudo mysql secure installation

sudo mysql --version

cat /etc/mysql/my.cnf | grep port

cat /etc/mysql/my.cnf | grep -i datadir

vagy

apt-get install 

 mysql-server (mysql-client)
 
mysql --version

service mysql restart (grep port/etc/mysql/my.cnf)

mysql -u root -p

root

1.11

mysql -v

Ezzel a mysql-en belül tudsz parancsokat adni. Ezek végére ; kerül!

SHOW VARIABLES LIKE ‘PORT’;

exit;


1.12 

lsof -i4 -P | grep -i mysql

1.13 

mysql -u root -p

SHOW DATABASES;

exit;

1.14 

Van az úgy, hogy a students.sql fáj nincs igazán a helyén :) , ilyenkor kérj meg valakit akinek ott van,
hogy valami kreatív módon segítsen átvarázsolni hozzád is ezt a fájlt #protip: pastebin

cd /root

mysql -u root -p < students.sql

jelszót kér, ha állítottál be az 1.10-ben: amit megadtál - elvileg a neptunod

1.15 

mysql -u root -p

SHOW DATABASES;

DESCRIBE students.students;

exit;

2.1

apt-get install apache2

2.2 

nano /etc/apache2/sites-enabled/000-default.conf

2.3

cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/irulabor.conf

nano /etc/apache2/sites-available/irulabor.conf

Ezután ebbe a fájlba bele kell írni a következő sorokat (mindegy hova, csak ne legyen előtte #):

ServerName irulabor.vmware

ServerAlias *.irulabor.vmware

DocumentRoot /var/www/irulabor

cd /var/www

mkdir irulabor

chown mekkelek /var/www/irulabor

nano /etc/hosts

Ezután írd bele a már meglévő IP címek alá a következőt:

127.0.0.1 irulabor.vmware

Mentés

a2ensite irulabor

/etc/init.d/apache2 restart

A kicsomagolt git repo web mappájából húzz át minden ott található fájlt a virtuális gép

/var/www/irulabor mappájába


2.4
nano /etc/apache2/sites-available/irulabor.conf

Ezt írd bele, szintén mindegy hova, csak blokkosítva legyen és ne legyen egyik előtt se #

<Directory /var/www/irulabor/vedett>

Require all denied

Require ip 127.0.0.1

</Directory>

Majd végül /etc/init.d/apache2 restart

vagy

<Directory /var/www/irulabor/vedett>

Options Indexes FollowSymLinks MultiViews

AllowOverride None

Order deny,allow

deny from all

Allow from 127.0.0.0/8

</Directory>

2.5

mkdir /etc/apache2/passwd

htpasswd -c /etc/apache2/passwd/.htpasswd mekkelek

Megadod mekkelek jelszavát (NEPTUN kódod).

nano /etc/apache2/sites-available/irulabor.conf

Alakítsd át a <Directory /var/www/irulabor/vedett> részt így:

<Directory /var/www/irulabor/vedett>

Options Indexes FollowSymLinks MultiViews

AllowOverride None

Require all denied

<RequireAll>
  
Require ip 127.0.0.1
  
Require valid-user
  
</RequireAll>

AuthType Basic

AuthName "IRULabor"

AuthUserFile /etc/apache2/passwd/.htpasswd

</Directory>

Majd végül /etc/init.d/apache2 restart

2.6

apt-get install pwauth

sudo a2enmod authnz_external

nano /etc/apache2/sites-available/irulabor.conf

Írd bele, szintén mindegy hova, legyen blokkosítva és # nélkül:

<IfModule mod_authnz_external.c>
  
AddExternalAuth pwauth /usr/sbin/pwauth
  
SetExternalAuthMethod pwauth pipe
  
</IfModule>

A fenti két sor elronthatja a korábbi feladato(ka)t, az ellenőrzőt nem árt lefuttatni a feladat vége után
és megnézni, hogy a korábbiak is jók-e még.

<Directory /var/www/irulabor/nagyonvedett>

Options Indexes FollowSymLinks MultiViews

AllowOverride None

AuthType Basic

AuthName "IRULabor - nagyonvedett"

AuthBasicProvider external

AuthExternal pwauth

Require valid-user

</Directory>

Ctrl + x, majd Y, végül enter.

/etc/init.d/apache2 restart

Ezután amikor böngészőben megnyitjuk a nagyonvedett-et, akkor a laboruser/laboruser
kombóval is működnie kéne.

2.8 

nano /etc/apache2/sites-available/irulabor.conf

Írd bele, szintén mindegy hova, legyen blokkosítva és # nélkül:

<Directory /var/www/irulabor/nyilvanos>

Options Indexes FollowSymLinks MultiViews

AllowOverride All

Order allow,deny

Allow from all

</Directory>

Ctrl + x, majd Y végül enter

/etc/init.d/apache2 restart

2.9

nano /var/www/irulabor/nyilvanos/.htaccess

?????Töröld ki a tartalmát a fájlnak, majd írd bele ezt:????

Options +Indexes

Ctrl + x, majd Y, végül enter

/etc/init.d/apache2 restart

3.0

cd /home/laboruser/

mkdir bin

cd bin

3.1C

nano 3_1.sh

Ebbe írd bele:

lscpu | grep 'Mhz’ | cut -d ":" -f2 | tr -d '[:space:]'

vagy

lscpu | grep -oP "CPU MHz: +\K([0-9]|,)*"

A és B (lscpu | grep 'Vendor ID’ | cut -d ":" -f2 | tr -d '[:space:]')

echo ""

ctrl+x, Y, majd enter

Ellenőrzéshez először futtathatóvá kell tenni, majd lefuttatni

chmod +x 3_1.sh

./3_1.sh

3.2

Hozz létre egy fájlt

nano file_3_2

Ebbe írj bele random sorokat, lehetőleg üreseket is. Jegyezd meg az üres sorok mennyiségét.

Mentsd el: ctrl + x, Y, majd enter

nano 3_2.sh

Ebbe írd bele:

ures_sorok= grep -c '^$'<&0

echo "$ures_sorok"

vagy

BÁRMI=$(grep -c "^$");

echo "$BÁRMI"

Mentsd el: ctrl + x, Y, majd enter

chmod +x 3_2.sh

cat file_3_2 | ./3_2.sh

vagy
 

3.3

#!/bin/bash

while IFS= read -r line;

do

array=(${line// /})

if [ $((array[0] % $1)) -eq 0 ]

then

Módosítani ahogy a feladat kéri:

echo "${array[0]} ${array[1]} ${array[2]} ${array[3]}${array[4]}"

fi

done

3.4

#!/usr/bin/env bash

whoami

date +"%Y. %m. %d."

who | cut -d “ ” -f1 | sort | uniq //sort nem biztos hogy kell

vagy

users | tr " " "\n" | sort -u

who -b | cut -d” ” -f 13-14 //13-14 a tartomány, ha nem jó amit kiír, akkor a számokat kell

változtatni

who -b | grep -oP "system boot  *\K\d{4}-\d{2}-\d{2} \d{2}:\d{2}"

echo $$ vagy $BASHPID

Mentsd el: ctrl + x, y majd enter

chmod 777 3_4.sh

./3_4.sh

ezzel futtatod

3.6

2 db szkriptre lesz szükség, egy ami két fájl összehasonlítását végzi, a másik pedig ami kétszer
lefuttatja, felcserélt paraméterekkel.
Összehasonlító szkript:

nano diffscript.sh

Tartalma:

COUNTER=0;

while IFS= read -r line;

do

if [[ $line == -* && $line != --* ]];

then

COUNTER=$((COUNTER+1))

fi

done

echo $COUNTER;

Ha ezzel megvagy, tedd futtathatóvá:

chmod +x diffscript.sh

Megoldás szkript:

nano 3_6.sh

Tartalma:

diff -u $1 $2 | /home/laboruser/diffscript.sh

diff -u $2 $1 | /home/laboruser/diffscript.sh

