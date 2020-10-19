## 1) Présentation

#### GLPI (Gestion libre de parc informatique) est un logiciel libre de gestion des services informatiques (gestion de parc, inventaire, budget…) et de gestion des services d’assistance (déclaration d’incident, tickets, statistiques).

## 2) Pré-requis

##### -Apache version 2.0 ou plus

##### -PHP 7.2 ou plus

```
php_curl
php_fileinfo
php_gd
php_mbstring
php_json
php_mysqli
php_session
php_zlib
php_simplexml
php_xml
php_intl
```

##### -MySQL version 5.6 ou plus

## 3) Installation

Commençons pour l’installation, créons dans un premier temps une database et un user dans la base de donnée mysql déjà installé.

```
sudo apt install mysql-server mysql-client
sudo mysql_secure_installation
```


```sql
sudo mysql -u root -p
CREATE DATABASE glpi;
USE glpi;
CREATE USER ‘glpi’@'localhost' IDENTIFIED BY ‘0000’;
GRANT ALL PRIVILEGES ON glpi.* TO ‘glpi’@'localhost';
exit;
```

Installons ensuite Apache et PHP avec les extensions suivantes :

```
sudo apt-get install apache2 php libapache2-mod-php php-json php-mysql php-curl php-mbstring php-gd php-xml php-fileinfo 
```

Il ne reste plus qu’à installer GLPI.

```
wget https://github.com/glpi-project/glpi/releases/download/9.4.5/glpi-9.4.5.tgz
tar xvf glpi-9.4.5.tgz
```

On bouge le dossier extrait dans le dossier du serveur apache

```
sudo mv glpi/ ../../var/www/html/
```

On donne les droits du serveur web sur le serveur apache

```
sudo chown -R www-data:www-data /var/www/html/
```

Passons maintenant au serveur web. Sur la machine Windows Server 2016 relié (avec le pare-feu Windows désactivé), entrer « 192.168.1.107/glpi » puis faire « suivant ».
Accepter les termes de licences puis faire installer
localhost / glpi / 0000
Selectionner la database « glpi »
L’installation est terminée ! Les identifiants sont « glpi » et « glpi ».

Une fois connecté sur l’interface web, nous pouvons facilement accéder aux services proposés par GLPI (tickets, planning, notes, contrats, etc…).
