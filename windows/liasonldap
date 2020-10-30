On souhaite une synchronisation d’utilisateurs entre notre AD et notre serveur Ubuntu.
Pour cela on va utiliser Kerberos.


Etape 1 : Installe les paquets nécessaires :

```
sudo apt update
sudo apt upgrade
sudo apt install sssd heimdal-clients msktutil
```

Etape 2 : Déplace la configuration default de Kerberos et créer un nouveau fichier :

```
sudo mv /etc/krb5.conf /etc/krb5.conf.default
sudo nano /etc/krb5.conf
```

Le nouveau fichier doit contenir ce contenu-ci :

```
[libdefaults]
default_realm = BTS.LAN
rdns = no
dns_lookup_kdc = true
dns_lookup_realm = true
[realms]
BTS.LAN = {
kdc = btsserver.bts.lan
admin_server = btsserver.bts.lan
}
```


Etape 3 : Initialise Kerberos et génère un fichier de clés

```
kinit administrator
klist
msktutil -N -c -b 'CN=COMPUTERS' -s UBUNTU /ubuntu.bts.lan -k my-keytab.keytab --computername UBUNTU-DESKTOP --upn UBUNTU $ --server btsserver.bts.lan --user-creds-only
msktutil -N -c -b 'CN=COMPUTERS' -s UBUNTU /ubuntu -k my-keytab.keytab --computer-name
UBUNTU --upn UBUNTU $ --server btsserver.bts.lan --user-creds-only
>kdestroy
```


Etape 4 : Configure le SSSD :

```
sudo mv my-keytab.keytab /etc/sssd/my-keytab.keytab
sudo nano /etc/sssd/sssd.conf
```

La configuration SSSD doit contenir ceci :

```
[sssd]
services = nss, pam
config_file_version = 2
domains = bts.lan
[nss]
entry_negative_timeout = 0
#debug_level = 5
[pam]
#debug_level = 5
[domain/bts.lan]
#debug_level = 10
enumerate = false
id_provider = ad
auth_provider = ad
chpass_provider = ad
access_provider = ad
dyndns_update = false
ad_hostname = ubuntu.bts.lan
ad_server = btsserver.bts.lan
ad_domain = bts.lan
ldap_schema = ad
ldap_id_mapping = true
fallback_homedir = /home/%u
default_shell = /bin/bash
ldap_sasl_mech = gssapi
ldap_sasl_authid = UBUNTU$
krb5_keytab = /etc/sssd/my-keytab.keytab
ldap_krb5_init_creds = true
Après avoir sauvegarder, mettre les permissions sur ce fichier :
sudo chmod 0600 /etc/sssd/sssd.conf
```

Etape 5 : Configure PAM :

```
sudo nano /etc/pam.d/common-session
```
Trouve la ligne contenant « session required pam_unix.so » près du pied de page. Ajoute la ligne
suivante en-dessous :

```
session required pam_mkhomedir.so skel=/etc/skel umask=0077
```

Après avoir sauvegarder et quitter, relance SSSD

```
sudo systemctl restart sssd
```

Etape 6 : Ajoute ton Administrateur domaine à ton groupe de domaine local :

```
sudo adduser administrateur sudo
```

Test de te connecter avec le compte Administrateur du domaine :

```
su -l administrateur
```

Etape 7 : Redémarre l’ordinateur

Dès lors on peut observer sur notre Windows Server que notre Ubuntu fait partis des ordinateurs
reconnus par notre AD.
