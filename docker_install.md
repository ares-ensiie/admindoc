# Installation de docker sur une machine

## Docker Compose
Docker compose permet de définir une infrastructure et de la lancer simplement.

Pour installer docker compose il suffit de lancer les commandes suivantes :
```shell
sudo sh -c "curl -L https://github.com/docker/compose/releases/download/1.3.3/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose"
sudo chmod +x /usr/local/bin/docker-compose
sudo sh -c "curl -L https://raw.githubusercontent.com/docker/compose/1.3.3/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose"
```
