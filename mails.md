# Envoyer des mails depuis un serveur ares

## Paramètrage de sendmail

Pour envoyer des mails depuis l'infra ares, il faut obligatoirement passer par le serveur postfix installé sur leely (10.0.5.4).

Pour cela nous utilisons ssmtp sur nos serveurs :

```shell
sudo apt-get install ssmtp mailutils
```

Puis configurez le serveur ssmtp en editant le fichier `/etc/ssmtp/ssmtp.conf` et faites correspondre les paramètres suivants:

```
mailhub=10.0.5.4
FromLineOverride=YES
rewriteDomain=ares-ensiie.eu
```

## Test

Si tout c'est bien passé, la commande sendmail doit passer par ssmtp:
```shell
$ sendmail -V
sSMTP 2.64 (Not sendmail at all)
```

## Envoie d'un email

Pour envoyer un email créez un fichier mail.txt avec le contenu suivant:
```
Subject:test
To: mon_email@domain.tld

Mon Super Message
Love ares
```

Puis :
```shell
sendmail -f ares mon_email@domain.tld < mail.txt
```
