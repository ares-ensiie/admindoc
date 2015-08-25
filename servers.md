# Les serveur ares

## Choucroute

IP:
- eth0: 10.0.0.1
- eth1: 130.79.243.34
- IPMI: 130.79.243.34

Serveur frontal. Il gère:
- Le DHCP pour le WIFI et les prises réseau des salles 1601 et 1604
- Le firewall
- Le serveur DNS

Comptes disponibles:
- root

## Arme
IP :
- eth0: 10.0.0.4
- IPMI: 10.0.0.204
Comptes disponibles:
- ares

Il gére :
  - Les VM's openstack

## Biere
IP:
- eth0: 10.0.0.2
- IPMI: 10.0.0.202

Comptes disponibles:
- ares

Il gère :
  - L'infrastructure Docker

## Ronflex
IP:
- eth0: 10.0.0.3
- IPMI: 10.0.0.203

Serveur de stockage.
Il gère:
- Le serveur NFS

Comptes disponibles:
- ares

## Leely
Machine virtuelle lancée sur arme
IP:
- eth0: 10.0.5.4
Comptes disponibles:
- ares
Il gère:
- Les mailing lists avec sympa

## Perso
Machine virtuelle lancée sur arme
IP:
- eth0: 10.0.5.2
Il gère:
- Les comptes ssh perso
- Les accès apache2 pour les comptes perso
