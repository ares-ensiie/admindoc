Ares utilise trois réseaux:
Le réseau interne les serveurs, il est utilisé pour la communication entre les différents serveurs.
- Addresse du réseau: `10.0.0.0`
- Masque du réseau: `255.255.0.0`
- Passerelle: `10.0.0.1`

Le réseau externe, en provenance directe d'osiris, nous n'avons que deux IP dessus : `130.79.243.34` et `130.79.243.38`. Seul l'IP `130.79.243.34` est utilisée par l'interface `eth0` de Choucroute.

Le réseau publique, c'est le réseau utilisé par les étudiants, l'admin, etc.
- Addresse du réseau: `10.3.0.0`
- Masque du réseau: `255.255.254.0`
- Passerelle: `10.3.0.1`
