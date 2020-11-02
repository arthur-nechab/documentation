## 1) Présentation

##### FOG ou Free OpenSource Ghost est une solution gratuite de déploiement d'images systèmes et d'applications sur des postes clients, qui se base sur une architecture L.A.M.P : Linux / Apache / MySQL / PhP. FOG permet de déployer un système d'exploitation préalablement cloné, sur un parc de machines donné via le réseau en s'appuyant sur les protocoles DHCP / PXE et UDPCast, soit en unicast soit en multicast.


## 2) Pré-requis

## 3) Installation

```
sudo wget https://github.com/FOGProject/fogproject/archive/master.tar.gz

sudo tar -xzvf master.tar.gz
cd fogproject-1.5.8/bin

sudo ./installfog.sh
```

L’installation commence
2 -> Type of installation : 
N -> (Si adresse IP est bonne) 
N -> Change Network
FOG DHCP : Y   -> Additionnal packages : N

A l’étape suivante, FOG donne une adresse web avec des identifiants, il faut se connecter depuis un ordinateur situé dans le même réseau que notre serveur Ubuntu. 
Sur la page web et cliquer sur « Install/Upgrade Now », une fois cette étape faite on tape entrée sur le serveur Ubuntu et FOG apparait.
Capturons maintenant une image d’un Windows que l’on a nous-même fait. 
Pour cela on redémarre un Windows situé dans le même réseau que FOG afin de rentrer dans le bios afin d’enregistrer la machine. 
FOG va enregistrer dans sa base de données l’ordinateur. Notre machine apparait maintenant dans la liste des hosts !


Cliquer sur le bouton « capture » afin de créer la tâche pour FOG
Au prochain redémarrage du PC, FOG copiera ce PC dans sa base de données. 
