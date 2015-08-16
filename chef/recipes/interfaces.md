# Chef recipes : Interfaces

La recipe interfaces permet la configuration de base des interfaces.

Elle ne dispose que d'une action : `interfaces`.

Cette recipe génère le fichier `/etc/network/interfaces`.
Puis redémarre l'interface `eth0` en executant la commande suivante:
```shell
sudo ifdown eth0 && sudo ifup eth0
```

Par défault elle applique les valeurs suivantes:
```ruby
default["network"]["eth0"] = {
  "static"=>true, # Utilise une ip static si true ou passe par le DHCP
  "address"=>"10.0.0.4" # Addresse ip de la machine
  "network"=>"10.0.0.0" # Addresse du réseau
  "netmask"=>"255.255.0.0" # Masque de sous réseau
  "gateway"=>"10.0.0.1" # Addresse de la passerelle
  "dns-nameservers"=>["8.8.8.8", "8.8.4.4"] # Addresse des serveurs dns a utiliser
}
```
