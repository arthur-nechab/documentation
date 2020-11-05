On souhaite une synchronisation d’utilisateurs entre notre AD et notre serveur Ubuntu.

https://www.datasunrise.com/blog/professional-info/integrating-a-linux-machine-into-windows-active-directory-domain/

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
default_realm = M2L.FR
rdns = no
dns_lookup_kdc = true
dns_lookup_realm = true
[realms]
M2L.FR = {
kdc = s1-intra.m2l.fr
admin_server = s1-intra.m2l.fr
}
```


Etape 3 : Initialise Kerberos et génère un fichier de clés

```
kinit administrator
klist
msktutil -N -c -b 'CN=COMPUTERS' -s S5-MAIL /s5-mail.bts.lan -k my-keytab.keytab --computername UBUNTU-DESKTOP --upn UBUNTU $ --server s1-intra.m2l.fr --user-creds-only
msktutil -N -c -b 'CN=COMPUTERS' -s S5-MAIL /s5-mail -k my-keytab.keytab --computer-name
S5-MAIL --upn S5-MAIL $ --server s1-intra.m2l.fr --user-creds-only
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
domains = m2l.fr
[nss]
entry_negative_timeout = 0
#debug_level = 5
[pam]
#debug_level = 5
[domain/m2l.fr]
#debug_level = 10
enumerate = false
id_provider = ad
auth_provider = ad
chpass_provider = ad
access_provider = ad
dyndns_update = false
ad_hostname = s5-mail.m2l.fr
ad_server = s1-intra.m2l.fr
ad_domain = m2l.fr
ldap_schema = ad
ldap_id_mapping = true
fallback_homedir = /home/%u
default_shell = /bin/bash
ldap_sasl_mech = gssapi
ldap_sasl_authid = S5-MAIL$
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
