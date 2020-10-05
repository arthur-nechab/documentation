### OCS Inventory est un logiciel d’inventaire et de télé déploiement très utilisé. Elle permet de réaliser des inventaires matériels et logiciels de machines du parc. Ces derniers sont consultables sur une interface web et dans GLPI grâce à un plugin.

1. On installe apache et ses modules ensuite 

```shell
sudo apt install apache2 libapache2-mod-php libapache-dbi-perl libapache-db-perl
```

2. Installation de PHP7.2 et ses modules

```shell
sudo apt-get install php php-zip php-pclzip php-mysql php-gd php-curl php-mbstring php-soap php-xml php-json
```

3. Installation de PERL 5.6 et ses modules

```shell
sudo apt install perl libxml-simple-perl libcompress-zlib-perl libdbi-perl libdbd-mysql-perl libnet-ip-perl libsoap-lite-perl libio-compress-perl libapache-dbi-perl  libapache2-mod-perl2 libapache2-mod-perl2-dev
```

4. Installation de Make et GCC et des modules PERL

```shell
sudo apt install make
sudo apt install gcc
```

5. On installe maintenant les modules de PERL

```shell
sudo perl -MCPAN -e 'install Apache2::SOAP'
sudo perl -MCPAN -e 'install XML::Entities'
sudo perl -MCPAN -e 'install Net::IP'
sudo perl -MCPAN -e 'install Apache::DBI'
sudo perl -MCPAN -e 'install Mojolicious'
sudo perl -MCPAN -e 'install Switch'
sudo perl -MCPAN -e 'install Plack::Handler'
```

Archive::Zip ne s’installe pas directement, il faut passer par le shell

```shell
sudo perl -MCPAN -e shell 
install Archive::Zip
exit
```

6. Créons un utilisateur qu’on utilisera plus tard dans la configuration Windows

```shell
sudo mysql -u root -p
```

```sql
CREATE DATABASE ocs;
USE ocs;
CREATE USER ‘ocs’@'localhost' IDENTIFIED BY ‘0000’;
GRANT ALL PRIVILEGES ON ocs.* TO 'ocs'@'localhost';
exit;
```

7. Installation d’OCS

```shell
sudo wget https://github.com/OCSInventory-NG/OCSInventory-ocsreports/releases/download/2.7/OCSNG_UNIX_SERVER_2.7.tar.gz
```

Extraire le dossier puis on se délace dedans

```shell
sudo tar xfvz OCSNG_UNIX_SERVER_2.7.tar.gz
cd OCSNG_UNIX_SERVER_2.7
sudo sh setup.sh
```

On active ensuite les modules suivants, puis on restart apache.

```shell
sudo a2enconf ocsinventory-reports
sudo a2enconf z-ocsinventory-server
```

On donne les droits d’accès au dossier ocsinventory-reports pour le serveur web

```shell
sudo chown -R www-data:www-data var/lib/ocsinventory-reports/
```

On restart ensuite proprement apache

```shell
sudo systemctl reload apache2
sudo service apache2 restart
```

Sur Windows maintenant, entrons le mot de passe root définis précédemment afin de permettre à OCS Inventory d’utiliser la base de données mysql.
 
Faire « Valider »

L’écran d’accueil d’OCS s’ouvre. Les identifiants sont « admin » et « admin ».
L’installation est terminée !

### OCS Inventory permet d'effectuer l'inventaire de la configuration des machines d'un réseau, qu'il s'agisse des logiciels ou bien de la configuration matérielle. 

### Ces informations sont disponibles sur l'interface web. Depuis cette dernière nous pouvons aussi déployer à distance des applications sur plusieurs machines.
