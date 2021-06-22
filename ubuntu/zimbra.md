On commence par ajouter le FQDN de notre serveur dans le fichier « hosts » :

```sudo nano /etc/hosts```
```172.16.10.5 s5-mail.m2l.fr```

On télécharge l'archive

```wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-NETWORK-8.8.15_GA_3869.UBUNTU18_64.20190918004220.tgz```

On décompresse

```tar xzvf zcs-NETWORK-8.8.15_GA_3869.UBUNTU18_64.20190918004220.tgz```

On lance ensuite l'installation

```
cd zcs-NETWORK-8.8.15_GA_3869.UBUNTU18_64.20190918004220
./install.sh
```

Suite à ça, il faudra configurer la time zone :

Appuyer sur 1 « 1) Common configuration »
Appuyer sur 7 « 7) Timezone »

Sélectionner la 108 « Europe/Berlin » 

A nouveau sur le menu de base :

Appuyer sur 7 « 7) Zimbra store »
Appuyer sur 4 « 4) Admin password »
Définir le mot de passe admin avec « zimbram2l » 

Il suffit alors de revenir sur le menu principal pour valider la configuration et l’installation va se terminer toute seule.
On peut alors redémarrer notre serveur et vérifier le statu de Zimbra :

```sudo su – 
su zimbra 
zmcontrol status
```

Il ne reste plus qu’à se connecter à Zimbra avec l’url suivante :

https://172.16.10.5:7071 (Interface ADMINISTRATION)

ou

https://s5-mail.m2l.fr/ (Interface CLIENT)
