## I/ Présentation

#### GLPI (Gestion libre de parc informatique) est un logiciel libre de gestion des services informatiques (gestion de parc, inventaire, budget…) et de gestion des services d’assistance (déclaration d’incident, tickets, statistiques).

## II/ Pré-requis

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
php_ldap (facultatif)
```

##### -MariaDB 10.0 ou plus

## III/ Installation

1. Créons une database et un user dans la base de donnée mysql déjà installé.

```
sudo apt install mysql-server mysql-client
sudo mysql_secure_installation
sudo mysql -u root -p
```

```sql
CREATE DATABASE glpi;
USE glpi;
CREATE USER 'glpi'@'localhost' IDENTIFIED BY '0000';
GRANT ALL PRIVILEGES ON glpi.* TO 'glpi'@'localhost';
exit;
```

2. Installons ensuite Apache et PHP avec les extensions suivantes :

```
sudo apt-get install apache2 php libapache2-mod-php php-json php-mysqli php-curl php-mbstring php-gd php-xml php-fileinfo php-zlib php-simplexml php-xml php-intl php-ldap
```

3. Dans `php/7.2/apache2/php.ini` décommenter les extensions

4. Installer GLPI

```
wget https://github.com/glpi-project/glpi/releases/download/9.5.2/glpi-9.5.5.tgz
tar xvf glpi-9.5.5.tgz
```

5. On bouge le dossier extrait dans le dossier du serveur apache

```
sudo mv glpi/ ../../var/www/html/
```

6. On donne les droits du serveur web sur le serveur apache

```
sudo chown -R www-data:www-data /var/www/html/
```

7. Passons maintenant au serveur web avec l'adresse `adresseip/glpi`
- Sélectionner le language
- Accepter les termes de licences
- Installer
- localhost / glpi / 0000
- Selectionner la database « glpi »
- Continuer
- L’installation est terminée ! Les identifiants sont « glpi » et « glpi ».

8. L'installation est terminée !

## IV/ Mode Opératoire

Une fois connecté sur l’interface web, nous pouvons facilement accéder aux services proposés par GLPI (tickets, planning, notes, contrats, etc…).

https://adrisio.files.wordpress.com/2018/06/glpi.pdf
