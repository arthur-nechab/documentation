## 1) Présentation

#### Bareos est un programme de sauvegarde basé sur le modèle client-serveur, qui permet de réaliser des sauvegardes sur un système d’information par le réseau. En cas de perte de données, de mauvaise manipulation ou d’un ransomware nous aurons la possibilité de récupérer nos données. 

#### L'incrémentielle se base sur la sauvegarde précédente, la différentielle se base sur sa sauvegarde complète. L'incrémentielle nécessite moins d'espace de stockage, mais demande un temps de restauration plus long. La différentielle nécessite plus d'espace de stockage, mais demande un temps de restauration plus court.

## 2) Pré-requis

##### -MySQL version 4.1 - 5.6

## 3) Installation

Commençons par l’installation.
```
sudo sh -c 'echo "deb http://download.bareos.org/bareos/release/latest//xUbuntu_18.04 /" >> /etc/apt/sources.list.d/bareos.list'
```

Télécharger ensuite :

```
wget -q http://download.bareos.org/bareos/release/latest/xUbuntu_16.04/Release.key -O- | sudo apt-key add -
```

sudo apt install mariadb-server bareos bareos-database-mysql

Sélectionner « Site internet » puis « Installer la db ».

Après l'installation, relancer les services du Bareos en exécutant les commandes ci-dessous :

```
sudo service bareos-dir start
sudo service bareos-sd start
sudo service bareos-fd start
```

Installons ensuite l'interface utilisateur Web :

```
sudo apt-get install bareos-webui
```

Les dépendances installées comprennent le serveur web Apache, PHP et un certain nombre d'extensions.

Redémarrons le serveur web Apache pour activer la nouvelle configuration.

```
sudo systemctl restart apache2
```

Exécuter les commandes ci-dessous pour lancer d'autres services Bareos.

```
sudo systemctl start bareos-dir bareos-sd bareos-fd
```

Créons un utilisateur pour nous connecter au service Web. Pour entrer dans la console bareos faire :

```
sudo bconsole
*relaod
*configure add console name=admin password=0000 profile=webui-admin tlsenable=false
exit
```

Enfin, redémarrons les différents service pour mettre à jour les modifications.

```
sudo systemctl restart apache2 bareos-dir bareos-sd bareos-fd
```

Passons maintenant au serveur web. Sur la machine Windows Server 2016 relié (avec le pare-feu Windows désactivé), entrer « 192.168.1.108/bareos-webui »

 
Une fois connecté, sauvegardons le serveur Bareos.

Dans le premier menu cliquez sur « Jobs » puis sur « Lancer ». 

Dans le champ job, sélectionner « backup-bareos-fd » et compléter les autres champs.


La sauvegarde serveur est prête. Essayons maintenant de sauvegarder une machine Ubuntu !

```
sudo apt-get install bareos-client
```

Toujours sur le client, ouvrez le fichier suivant :

```
sudo nano /etc/bareos/bareos-fd.conf
 ```
 
Et ajouter :

```
Director {
 Name = "bareos-dir"
 Password  = "0000"
}
```

Passons sur le serveur Bareos. Définissons le job de sauvegarde pour le client Linux:

```
sudo nano /etc/bareos/bareos-dir.d/job/backup-bareos-fd.conf
```

Ajouter le texte suivant au fichier:

```
Job {
 Name = "backup-linux-fd"
 Jobdefs = "Linux"
 Client = "client-linux-fd"
}
```

Éditons ensuite le fichier ci-dessous :

```
sudo nano /etc/bareos/bareos-dir.d/client/bareos-fd.conf
```

Avec les lignes

```
Client {  
 Name = client-linux-fd 
 Address = 192.168.1.4
 Password = "0000"
}
```

Et créons enfin le job associé :

```
sudo nano /etc/bareos/bareos-dir.d/jobdefs/Linux.conf
```

Avec les lignes :

```
JobDefs {
Name = "Linux"
Type = Backup
Level = Incremental
Client = client-linux-fd
FileSet = "SelfTest" # selftest fileset $
Schedule = "WeeklyCycle"
Storage = File
Messages = Standard
Pool = Incremental
Priority = 10
Write Bootstrap = "/var/lib/bareos/%c.bsr"
Full Backup Pool = Full # write Full Backups into "Full" Poo$
Differential Backup Pool = Differential # write Diff Backups into "Different$
Incremental Backup Pool = Incremental # write Incr Backups into "Increment$
}
```
 

Redémarrons maintenant les services pour prendre en compte les modifications :

```
sudo systemctl start bareos-dir 
sudo systemctl start bareos-fd 
sudo systemctl start bareos-sd
```

Aller sur l’interface Web de Bareos vérifier bien que le client apparaît dans la liste en cliquant sur « Clients » dans le menu en haut:

Il suffit maintenant d’aller dans le menu « Jobs » puis sur « Lancer ». 

Dans le champ « Job », sélectionner le client de la machine linux.

