## 1) Présentation

##### OCS Inventory est un logiciel d’inventaire et de télé déploiement très utilisé. Elle permet de réaliser des inventaires matériels et logiciels de machines du parc. Ces derniers sont consultables sur une interface web et dans GLPI grâce à un plugin. OCS Inventory permet d'effectuer l'inventaire de la configuration des machines d'un réseau, qu'il s'agisse des logiciels ou bien de la configuration matérielle. Ces informations sont disponibles sur l'interface web. Depuis cette dernière nous pouvons aussi déployer à distance des applications sur plusieurs machines.

## 2) Pré-requis

##### -Apache version 2.2 ou plus

```
-Mod_perl version 1.29 ou plus
```

##### -PHP 5.5 ou plus, avec ZIP et GD support activé

```
php_curl
php_mbstring
php_soap
php_xml
```

##### -PERL 5.6 ou plus

```
Perl module XML::Simple version 2.12 ou plus
Perl module Compress::Zlib version 1.33 ou plus
Perl module DBI version 1.40 ou plus
Perl module DBD::Mysql version 2.9004 ou plus
Perl module Apache::DBI version 0.93 ou plus
Perl module Net::IP version 1.21 ou plus
Perl module SOAP::Lite version 0.66 ou plus
Perl module Mojolicious::Lite
Perl module Plack::Handler
Perl module Archive::Zip
Perl module YAML
Perl module XML::Entities
Perl module Switch
```

##### -MariaDB 4.1 ou plus

##### -Make et GCC

## 3) Installation

1. Installation de Apache 2.4 et de ses modules

```
sudo apt install apache2 libapache2-mod-php libapache-dbi-perl libapache-db-perl
```

2. Installation de PHP 7.2 et de ses modules

```
sudo apt-get install php php-zip php-pclzip php-mysql php-gd php-curl php-mbstring php-soap php-xml
```

3. Installation de PERL ???? et de ses modules

```
sudo apt install perl libxml-simple-perl libcompress-zlib-perl libdbi-perl libdbd-mysql-perl libnet-ip-perl libsoap-lite-perl libio-compress-perl libapache-dbi-perl  libapache2-mod-perl2 libapache2-mod-perl2-dev
```

4. Installation de Make et GCC

```
sudo apt install make
sudo apt install gcc
```

5. Installation des modules de PERL

```
sudo perl -MCPAN -e 'install Apache2::SOAP'
sudo perl -MCPAN -e 'install XML::Entities'
sudo perl -MCPAN -e 'install Net::IP'
sudo perl -MCPAN -e 'install Apache::DBI'
sudo perl -MCPAN -e 'install Mojolicious'
sudo perl -MCPAN -e 'install Switch'
sudo perl -MCPAN -e 'install Plack::Handler'
```

(Archive::Zip ne s’installe pas directement, il faut passer par le shell)

```
sudo perl -MCPAN -e shell 
install Archive::Zip
exit
```

6. Installons MariaDB

```
sudo apt install mariadb-server
sudo mysql_secure_installation
sudo mysql -u root -p
```

```sql
CREATE DATABASE ocs;
USE ocs;
CREATE USER 'ocs'@'localhost' IDENTIFIED BY '0000';
GRANT ALL PRIVILEGES ON ocs.* TO 'ocs'@'localhost';
exit;
```

7. Installation d’OCS

```
sudo wget https://github.com/OCSInventory-NG/OCSInventory-ocsreports/releases/download/2.8/OCSNG_UNIX_SERVER_2.8.tar.gz
```

8. Extraire le dossier puis on se délace dedans

```
sudo tar xfvz OCSNG_UNIX_SERVER_2.8.tar.gz
cd ../../opt/OCSNG_UNIX_SERVER_2.8
sudo sh setup.sh
```

9. On active ensuite les modules suivants, puis on restart apache.

```
sudo a2enconf ocsinventory-reports
sudo a2enconf z-ocsinventory-server
```

10. On donne les droits d’accès au dossier ocsinventory-reports pour le serveur web

```
sudo chown -R www-data:www-data var/lib/ocsinventory-reports/
```

11. On restart ensuite proprement apache

```
sudo systemctl reload apache2
sudo service apache2 restart
```

12. Sur Windows maintenant, entrons le mot de passe root définis précédemment afin de permettre à OCS Inventory d’utiliser la base de données mysql. Faire « Valider ». L’écran d’accueil d’OCS s’ouvre. Les identifiants sont « admin » et « admin ». L’installation est terminée !

13. Ne pas oublier de supprimer le fichier `install.php` dans ../ocsreports/

14. Enfin dans `php/7.2/apaché/php.ini`

```
max_execution_time = 180
max_input_time = 1800
memory_limit = 256M
upload_max_filesize = 300M
post_max_size = 300M
```

15. Reboot !
