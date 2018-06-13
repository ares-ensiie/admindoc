# Docker
## Installation de docker Compose
Docker compose permet de définir une infrastructure et de la lancer simplement.

Pour installer docker compose il suffit de lancer les commandes suivantes :
```shell
sudo sh -c "curl -L https://github.com/docker/compose/releases/download/1.3.3/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose"
sudo chmod +x /usr/local/bin/docker-compose
sudo sh -c "curl -L https://raw.githubusercontent.com/docker/compose/1.3.3/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose"
```


## Misc

### Problème de ports

Il se peux que docker se mélange les pinceaux avec les ports et par conséquent certains ports deviennent innaccèssible ou reliés au mauvais container.

Pour y remedier : une solution simple (si le serveur n'a pas de configuration iptables particulière).

/!\ ATTENTION NE JAMAIS LANCER CE SCRIPT SUR CHOUCROUTE !!!!

```shell
cd ares/
docker-compose stop
sudo service docker stop
sudo ../scripts/iptables-reset.sh
sudo service docker start
docker-compose up -d
```
