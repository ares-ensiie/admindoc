# Sympa

## Installation

Sympa est installé sur leely. Une machine tournant sur Arme.

Pour installer sympa, nous avons suivi la procédure décrite [ici](https://wiki.debian.org/Sympa).
De plus la liaison avec le LDAP est disponible [ici](http://www.sympa.org/documentation/manual/doc-5.2.3/html/node13.html).

Cependant, afin de rendre Sympa compatible avec note système, nous avons modifié le code d'authentification de sympa.
Le code modifié est dans le fichier `/usr/share/sympa/lib` (l239) :

Le champ base valait à l'origine :
```perl
$ldap->{'suffix'}
```

Il à été modifié en :

```perl
'uid='.$auth.','.$ldap->{'suffix'}
```
> NDLR: Je soupçonne un problème de doits sur le ldap. Mais pas réussi a trouver la cause exacte. 1 Panini à celui qui trouve (john).

## Infra

Les mailing lists hebergent le serveur postfix d'ares.

Il doit être géré pour relayer mailhost.u-strasbg.fr ([Documentation](https://services-numeriques.unistra.fr/documentations/outils-collaboratifs/messagerie-electronique/relayage-de-messagerie.html))


## Vrac

Synchro les aliases :
```shell
postalias /etc/mail/sympa/aliases
```

Un utilisateur LDPAP à été créé pour gérer le compte listmaster : `sympa`
