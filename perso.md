# Perso

Le serveur perso est un serveur accèssible par tous les étudiants. La conenction se fait via le compte LDAP de l'utilisateur et les utilisateurs peuvent stocker des fichiers perso dans leur dossier public_html.

## Configuration

### LDAP
Afin de se connecter en LDAP la procédure de configuration suivie fut celle [décrite ici](http://doc.ubuntu-fr.org/ldap_client).
Tous les utilisateurs sont automatiquement ajouté dans le groupe `students` (git 1111).

### Motd
Comme sur tous les serveurs unix le MOTD est personnalisable dans le dossier `/etc/update-motd.d`.

### SKEL
Les homes de chaques utilisateurs sont créé en suivant un squelette disponible dans le dossier `/etc/skel`.

Dans ce dossier nous trouvons plusieurs choses.
Tout d'abord nous mettons en place un dossier `public_html/` pour qu'apache2 puisse servire ces fichiers.
Ensuite nous créons le dossier .irssi/ pour la connection a l'irc ares.

### Apache2
Sur se serveur apache est concu pour servire les fichiers des homes.
Pour cela tous les fichiers disponibles dans le dossier `/areshome/<USER>/public_html` sera disponible à l'addresse : https://perso.ares-ensiie.eu/~<USER>/

### Divers
Le script screen_irssi est disponible dans le dossier `/usr/local/bin`
