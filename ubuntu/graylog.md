#### Graylog est un outil qui permet de centralisation de logs. Un log est un évènement notifié et crédité d’une importance plus ou moins élevée par un service. Il est collecté dans un journal et consultable à tout moment. Les logs permettent de comprendre le fonctionnement d'un service et de nous prévenir de potentiels dysfonctionnements au sein de ce même service, donc d'expliquer d'où proviennent les erreurs et de nous donner ainsi une première piste de résolution. Ils permettent également de tracer le parcours d'un utilisateur, par exemple lors d'une intrusion frauduleuse. Tout système, service et même élément réseaux, produisent des logs qui sont consultables dans un journal local.

Graylog est composée de 4 parties :
•	Elasticsearch permettant le stockage de logs et la recherche textuelle.
•	MongoDB qui assure la gestion des métadonnées.
•	Le serveur Graylog qui va « récolter » les logs.
•	Et l’interface web de Graylog, qui va nous permettre de consulter nos logs et de paramétrer certaines fonctionnalités de Graylog sur une interface plus facile.



1)	Installation d’Elasticsearch :

```
sudo apt install -y apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen curl dirmngr
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
sudo apt install -y elasticsearch
```

Faisons-en sorte que le service Elasticsearch démarre automatiquement au démarrage du système.

```
sudo systemctl enable elasticsearch
sudo nano /etc/elasticsearch/elasticsearch.yml
```

Modifier le cluster en : 

```
cluster.name: graylog
```

Relançons le service Elasticsearch pour mettre à jour les nouvelles configurations.

```
sudo systemctl restart elasticsearch
```

2)	Installation de MongoDB :

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
sudo apt install -y mongodb-org
```

Faisons-en sorte que le service MongoDB démarre automatiquement au démarrage du système.

```
sudo systemctl start mongod
sudo systemctl enable mongod
```

3)	Installation de Graylog :

```
wget https://packages.graylog2.org/repo/packages/graylog-3.0-repository_latest.deb
sudo dpkg -i graylog-3.0-repository_latest.deb
sudo apt install -y graylog-server
```

Pour bien configurer Graylog, il nous faut un mot de passe. Utiliser la commande pwgen et sauvegarder l’outpout sur un fichier txt externe.

```
pwgen -N 1 -s 96
```
 

Modifier maintenant le server.conf

```
sudo nano /etc/graylog/server/server.conf
```

Placer le mot de passe crypté dans « password_secret »

Il nous faut un mot de passe pour se connecter à l’interface web. Il ne peut pas être modifié directement dessus, il faut dont l’éditer dans le fichier de configuration.

```
echo -n 0000 | sha256sum
```

Modifier maintenant le server.conf encore.

```
sudo nano /etc/graylog/server/server.conf
```

Placer le mot de passe crypté dans « root_password_sha2 »

```
http_bind_address = 192.168.1.3:9000
```
 

Relançons le service Graylog pour mettre à jour les nouvelles configurations et faisons-en sorte que le service Elasticsearch démarre automatiquement au démarrage du système.

```
sudo systemctl restart graylog-server
sudo systemctl enable graylog-server
```

4)	Configuration sur le Web
Passons maintenant au serveur web. Sur la machine Windows Server 2016 relié (avec le pare-feu Windows désactivé), entrer « 192.168.1.103:9000 ».
Connectons-nous avec le nom d'utilisateur admin et le mot de passe que nous avons configuré à root_password_sha2 sur server.conf.
 
Sans logs, Graylog est inutile ! Il faut lui en envoyer désormais.
Pour cet exemple, nous allons essayer d’importer des logs de syslog sur le port UDP 1514
Aller dans « System » puis « Inputs » et sélectionner « Syslog UDP ».
-	Title : SyslogUDP
-	Bind address
-	Port : 1514
 

Sur la machine Ubuntu maintenant
```
sudo nano /etc/rsyslog.conf
```
Puis ajouter à la fin
```
*.info ;mail.none ;authpriv.none ;cron.none @192.168.1.103:1514
```
Redémarrons maintenant le service rsyslog pour mettre à jour nos modifications.

Les logs devraient commencé à apparaitre sur l’interface Graylog !
