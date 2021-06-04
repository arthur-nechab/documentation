## Présentation

#### Le protocole CARP, pour *Common Address Redundancy Protocol*, permet à plusieurs hôtes d’utiliser une même IP pour effectuer de la redondance. Avec ce protocole, nous utiliserons aussi les protocoles pfSync et XML-RPC, qui permettent respectivement de synchroniser l’état des connexions en cours entre deux hôtes pfSense et pour le second de répliquer la configuration.
#### Cela va nous permettre de mettre en place un Failover, afin d'assurer un service en haute-disponibilité.

## Pré-requis
##### -2 pfSense sur le même réseau LAN
##### -2 adresses disponibles pour les IP virtuelles (1 sur le WAN et 1 sur le LAN)

## Installation

1. Configuration des adresses IP virtuelles

Afin de fonctionner, chaque serveur pfSense doit disposer d'une adresse IP sur son interface, ainsi qu'une adresse IP virtuelle qui sera partagée entre les deux serveurs pfSense. De ce fait, nous utilisons ![Uploading image.png…]() adresses IP par réseau.

![lan](https://i.imgur.com/4L5nqFG.png)
![wan](https://i.imgur.com/s9H7GDS.png)

- **Mot de passe d'IP virtuelle :** mot de passe permettant de sécuriser les échanges au sein du groupe d'hôtes se partageant la VIP.
- **Groupe VHID :** Virtual Host Identifier. Un serveur peut faire parti de plusieurs groupes de VIP. Afin d'identifier chaque groupe, un ID unique lui est assigné.
- **Fréquence d'annonce :** la valeur du champ "Skew" à 0 désigne le master (pfSense primaire). Une valeur plus élevée désignera l'esclave (pfSense de secours). La valeur de "Base" correspond au timeout en seconde au bout duquel l'hôte sera considéré comme inaccessible.

Il faut faire la même configuration sur le pfsense secondaire (en pensant bien à passer la valeur du champ "Skew" à 1).

![ip](https://i.imgur.com/c96j69W.png)

2. Vérification de l'état des addresses

![carp1](https://i.imgur.com/K4aAArJ.png)
![carp1](https://i.imgur.com/C1Jd1wC.png)

3. Forcer l'utilisation des adresses IP virtuelles.

Les adresses IPv sont déclarées, mais non-utilisées. Il reste à configurer pfSense pour qu'il utilise les adresses Ipv plutôt que les adresses IP attribuées à ses interfaces logiques. Dans l'onglet "PARE-FEU" > "NAT" > "SORTIE", cocher la case "Création manuelles de règles NAT sortantes". Puis modifier les adresses comme ceci :

![nat](https://i.imgur.com/p1To9H1.png)

4. Mise en place de la haute-disponibilité

Se rendre dans "Système" > "Haute disponibilité". 2 parties à configurer :

![1](https://i.imgur.com/0rBQ55c.png)
![2](https://i.imgur.com/ByfmuWf.png)

- **Etat de la synchronisation** : Cocher.
- **Synchroniser l'interface** : Ici nous avons mis en place une interface spécifique, utilisons là.
- **IP de synchronisation pfsync du pair** : IP de l'interface du serveur secondaire.

Aussi dans XMLRPC Sync :

- **Synchroniser la configuration avec IP** : IP de l'interface du serveur secondaire.
- **Nom utilisateur du système distant** : Utilisateur pour se connecter sur le serveur secondaire.
- **Mot de passe du système distant** : Mot de passe pour se connecter sur le serveur secondaire.

Sur le serveur secondaire, **ne faire uniquement que la première partie**, en changeant l'IP.

![3](https://i.imgur.com/74OqRMZ.png)

5. Autorisation des flux de réplication au niveau des règles de pare-feu.

Sur l'interface CARP, mettre cette règle puis vérifier la réplication :

![règle](https://i.imgur.com/tiXDoX7.png)

C'est terminé !
