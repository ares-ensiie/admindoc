# Chef

Chef permet la gestion de la configuration des serveurs physiques et des VMS.
Le contôleur chef est installé sur Arme. L'interface web et le serveur chef répondent sur l'url `chef.ares-ensiie.eu`. On peut se connecter sur le manager grâce au login `ares`.

## Ajout d'une node

Pour ajouter une node, connectez vous sur arme avec le compte `chef` et placez vous dans le dossier `/home/chef/chef-repo/`

Puis lancez la commande suivante:
```shell
knife bootstrap {{node_address}} --ssh-user {{user}} --ssh-password '{{password}}' --sudo --use-sudo-password --node-name {{node_name}}
```

Ex pour arme:
```shell
knife bootstrap 10.0.0.4 --ssh-user ares --ssh-password 'REDACTED' --sudo --use-sudo-password --node-name arme
```

## CookBook

Le cookbook ares est stocké sur arme : `/home/chef/chef-repo/cookbook`

## La runlist

Chaque node à une runlist qui définit les actions à appliquer sur une node.
La runlist est manageable par l'interface en ligne ou par la commande `knife` (Module node).

## Modifier les valeurs par défaut des recipes
Les recipes viennent généralement avec des valeurs par défaut. Cependant il est parfois nécessaire de les modifier en fonction de la node sur laquelle nous travaillons.

Par exemple pour la recipe `interfaces::interfaces`, la valeur par défaut pour l'addresse ip est `10.0.0.2`. Cependant nous pouvons la modifier en ajoutant aux attributs de la node le code suivant:

```json
"network": {
  "eth0": {
    "address": "10.0.0.3"
  },
}
```

## Recipes
Liste des recipes:
- [interfaces](chef/recipes/interfaces.md)
- [filesystems](chef/recipes/filesystems.md)
