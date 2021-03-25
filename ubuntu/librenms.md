## 1) Présentation

#### LibreNMS est un outil communautaire qui permet de faire de la surveillance informatique sur un large éventail de systèmes. Il permet de réaliser la métrologie d’infrastructures informatiques en tous genres. Il permet de remonter des métriques de presque toutes les sources SNMP. LibreNMS possède un dashboard entièrement personnalisable permettant de mettre en avant les graphiques les plus intéressants, et possède aussi un puissant moteur d’alerting.

## 2) Pré-requis
##### -?
##### -?

## 3) Installation

1. Installation des paquets necessaires

```
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update
sudo apt upgrade
sudo apt install curl composer fping git graphviz imagemagick mariadb-client mariadb-server mtr-tiny nginx-full nmap php7.3-cli php7.3-curl php7.3-fpm php7.3-gd php7.3-json php7.3-mbstring php7.3-mysql php7.3-snmp php7.3-xml php7.3-zip python-memcache python-mysqldb rrdtool snmp snmpd whois unzip python3-pip
```

2. Ajout d’un utilisateur librenms

```
sudo useradd librenms -d /opt/librenms -M -r
sudo usermod -a -G librenms www-data
```

3. Installation de librenms

```
cd /opt
sudo git clone https://github.com/librenms/librenms.git ../../opt/librenms
```

4. Gestion des droits

```
sudo chown -R librenms:librenms /opt/librenms
sudo chmod 770 /opt/librenms
sudo setfacl -d -m g::rwx /opt/librenms/rrd /opt/librenms/logs /opt/librenms/bootstrap/cache/ /opt/librenms/storage/
sudo setfacl -R -m g::rwx /opt/librenms/rrd /opt/librenms/logs /opt/librenms/bootstrap/cache/ /opt/librenms/storage/
```

5. Installation des dépendances de PHP

```
cd /opt/librenms
sudo su - librenms
./scripts/composer_wrapper.php install --no-dev
exit
```

6. Configuration de MySQL

```
sudo systemctl restart mysql
sudo mysql -u root -p
```

```sql
CREATE DATABASE librenms CHARACTER SET utf8 COLLATE utf8_unicode_ci;
USE librenms;
CREATE USER 'librenms'@'localhost' IDENTIFIED BY '0000';
GRANT ALL PRIVILEGES ON librenms.* TO 'librenms'@'localhost';
FLUSH PRIVILEGES;
exit
```

```
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

En dessous de [mysqld] ajouter :

```
innodb_file_per_table=1
sql-mode=""
lower_case_table_names=0
```

```
sudo systemctl restart mysql
```

7. Configuration du fuseau horaire de PHP

```
sudo timedatectl set-timezone Europe/Paris

sudo nano /etc/php/7.3/fpm/php.ini
date.timezone = Europe/Paris

sudo nano /etc/php/7.3/cli/php.ini
date.timezone = Europe/Paris
```
 
8. Configuration de Nginx

```
sudo systemctl restart php7.3-fpm
sudo service php7.3-fpm start
sudo nano /etc/nginx/conf.d/librenms.conf
```

Coller ce texte dans le fichier .conf :

```
server {
 listen      80;
 server_name librenms.example.com;
 root        /opt/librenms/html;
 index       index.php;

 charset utf-8;
 gzip on;
 gzip_types text/css application/javascript text/javascript application/x-javascript image/svg+xml text/plain text/xsd text/xsl text/xml image/x-

icon;
 location / {
  try_files $uri $uri/ /index.php?$query_string;
 }
 location /api/v0 {
  try_files $uri $uri/ /api_v0.php?$query_string;
 }
 location ~ \.php {
  include fastcgi.conf;
  fastcgi_split_path_info ^(.+\.php)(/.+)$;
  fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
 }
 location ~ /\.ht {
  deny all;
 }
}
```

```
sudo rm /etc/nginx/sites-enabled/default
sudo systemctl restart nginx
sudo cp /opt/librenms/snmpd.conf.example /etc/snmp/snmpd.conf
sudo nano /etc/snmp/snmpd.conf
```

Remplacer RANDOMSTRINGGOESHERE par autre chose (ici « public »).

9. Configuration de SNMP

```
sudo curl -o /usr/bin/distro https://raw.githubusercontent.com/librenms/librenms-agent/master/snmp/distro
sudo chmod +x /usr/bin/distro
sudo systemctl restart snmpd
```

10.	Cron Job

```
sudo cp /opt/librenms/librenms.nonroot.cron /etc/cron.d/librenms
```

11.	Configuration des logs

```
sudo cp /opt/librenms/misc/librenms.logrotate /etc/logrotate.d/librenms
```

Nous pouvons maintenant vérifier l’état de nginx.

```
sudo systemctl restart nginx
sudo nginx -t
```

12. Configuration sur le serveur web
Sur la machine Windows (ne pas oublier d’enlever le pare-feu windows), entrer sur le navigateur l’ip du serveur où est installé librenms. Faire « suivant ».
-	Entrer le mot de passe de la db et faire « Suivant ».
-	Entrer ensuite les identifiants admin pour la connection.
Une fois l’installation terminée, il ne vous reste plus qu’à vous connecter !
