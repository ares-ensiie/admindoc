# Certificats

Ares utilise un certificat ssl autogénéré.

Il est stocké sur biere dans le dossier `/home/ares/certificates`

Pour le generer :

```shell
openssl req -new -x509 -keyout cert.pem -out cert.pem -days 365 -nodes
Country Name (2 letter code) [AU]:FR
State or Province Name (full name) [Some-State]:France
Locality Name (eg, city) []:Illkirch
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ENSIIE
Organizational Unit Name (eg, section) []:Ares
Common Name (eg, YOUR name) []:*.ares-ensiie.eu
Email Address []:ares@ares-ensiie.eu
```
Le certificat doit être re-généré tous les ans.
Date de dernière génération : `22/08/2015`
