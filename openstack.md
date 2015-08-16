# OpenStack

## Installation

Pour l'instant l'installation d'OpenStack ne ce fait pas par chef.

### Paquets
Les paquets d'ubuntu 14.04 ne supportant pas la version Juno il nous faut passer par ceux de la 15.04:

```shell
sudo apt-get install ubuntu-cloud-keyring
sudo echo "deb http://ubuntu-cloud.archive.canonical.com/ubuntu trusty-updates/kilo main" > /etc/apt/sources.list.d/cloudarchive-kilo.list
sudo apt-get update
sudo apt-get upgrade
```
### Rabbitmq
La première chose à installer est RabbitMQ. RabbitMQ est un service permettant l'échange de messages. C'est ce qu'utilise openStack pour faire communiquer les différentes nodes.

```shell
sudo apt-get install -y rabbitmq-server
```

Editez la configuration de rabbitmq (`/etc/rabbitmq/rabbitmq-env.conf`) et ajoutez les lignes suivante:
```shell
NODENAME=openstack
NODE_PORT=5673
```

Puis créez le fichier de configuration (`/etc/rabbitmq/rabbitmq.config`) et ajoutez la ligne suivante:
```
[{rabbit, [{loopback_users, []}]}].
```

Puis redemmarrez le service avec:
```shell
service rabbitmq-server restart
```

### MySQL
Pour le stockage, OpenStack utilise le SGBD MySQL.

```shell
sudo apt-get install -y mysql-server python-mysqldb
```

Modifiez le fichier de configuration de mysql (`/etc/mysql/my.cnf`) tel que :
```ini
[mysqld]
...
bind-address = 0.0.0.0
...
default-storage-engine = innodb
innodb_file_per_table
collation-server = utf8_general_ci
init-connect = 'SET NAMES utf8'
character-set-server = utf8
```

Puis relancez mysql :
```shell
sudo service mysql restart
```

### Utilitaires réseau
Afin de gérer la configuration openStack dépend d'autres outils :
 - ntp : Pour la gestion de l'heure par le réseau
 - vlan : Utilisation des vlans
 - bridge-utils : Pour la gestion des bridge.

```shell
sudo apt-get install -y ntp vlan bridge-utils
```

Enfin editez les lignes suivantes du fichier `/etc/sysctl.conf`:

```shell
net.ipv4.ip_forward=1
net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.default.rp_filter=0
```

Puis appliquez les paramêtres :
```shell
sudo sysctl -p
```

### Keystone

#### Installation

Keystone est le gestionnaire d'identitée d'openstack, il est résponsable (entre autre) de l'authentification des utilisateurs.

```shell
sudo apt-get install keystone
```

Puis créez la BDD pour keystone :

```shell
mysql -u root -p
mysql> CREATE DATABASE keystone;
mysql> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY '<PASSWORD>';
mysql> quit
```

Puis modifiez le fichier de configurationde Keystone pour qu'il utilise mysql a la place de sqlite.
Dans le fichier `/etc/keystone/keystone.conf` remplacez :
```
connection = sqlite:////var/lib/keystone/keystone.db
```
par
```
connection = mysql://keystone:<PASSWORD>@10.0.0.4/keystone
```

Enfin redémarrez keystone :
```shell
sudo service keystone restart
```

Puis synchronisez la BDD :
```shell
sudo keystone-manage db_sync
```

#### Création des comptes de base et de l'endpoint pour l'indentification.

```shell
sudo -s

export OS_SERVICE_TOKEN=ADMIN
export OS_SERVICE_ENDPOINT=http://10.0.0.4:35357/v2.0

keystone tenant-create --name=admin --description="Admin Tenant"
keystone tenant-create --name=service --description="Service Tenant"
keystone user-create --name=admin --pass=<PASSWORD> --email=ares@ares-ensiie.eu
keystone role-create --name=admin
keystone user-role-add --user=admin --tenant=admin --role=admin

keystone service-create --name=keystone --type=identity --description="Keystone Identity Service"
keystone endpoint-create --service=keystone --publicurl=http://10.0.0.4:5000/v2.0 --internalurl=http://10.0.0.4:5000/v2.0 --adminurl=http://10.0.0.4:35357/v2.0

unset OS_SERVICE_TOKEN
unset OS_SERVICE_ENDPOINT
exit
```

On peut maintenant se faire un fichier avec nos identifiants pour pouvoir se connecter:
```shell
echo "export OS_USERNAME=admin" >> ~/.keystone_creds
echo "export OS_PASSWORD=b11ere5" >> ~/.keystone_creds
echo "export OS_TENANT_NAME=admin" >> ~/.keystone_creds
echo "export OS_AUTH_URL=http://10.0.0.4:35357/v2.0" >> ~/.keystone_creds

chmod 600 ~/.keystone_creds
```

Aisni pour nous connecter ils nous suffira de faire :
```shell
source ~/.keystone_creds
```

Pour tester vous pouvez lancer:
```shell
source ~/.keystone_creds
keystone token-get
keystone user-list
```

## Glance

Glance est le service utilisé pour stocker les images.

```shell
sudo apt-get install glance
```

Il lui faut également une BDD:

```shell
mysql -u root -p
mysql> CREATE DATABASE glance;
mysql> GRANT ALL ON glance.* TO 'glance'@'%' IDENTIFIED BY '<PASSWORD>';
mysql> quit
```
Et un compte keystone (+endpoint):

```shell
source ~/.keystone_creds
keystone user-create --name=glance --pass=<PASSWORD> --email=ares@ares-ensiie.eu
keystone user-role-add --user=glance --tenant=service --role=admin
keystone service-create --name=glance --type=image --description="Glance Image Service"
keystone endpoint-create --service=glance --publicurl=http://10.0.0.4:9292 --internalurl=http://10.0.0.4:9292 --adminurl=http://10.0.0.4:9292
```

Il y a quelques modifications a apporter à la configuration de glance.

Editez le fichier `/etc/glance/glance-api.conf`
et commentez la ligne :
```
sqlite_db = /var/lib/glance/glance.sqlite
```

Puis rajoutez :
```
connection = mysql://glance:<PASSWORD>@10.0.0.4/glance
```

Puis Modifiez la partie keystone :
```
[keystone_authtoken]
identity_uri = http://10.0.0.4:35357
admin_tenant_name = service
admin_user = glance
admin_password = <PASSWORD>

[paste_deploy]
flavor = keystone
```

Enfin modifiez le chemin de stockage des images pour utiliser le nfs :
```
filesystem_store_datadir=/openstack/glance/images/
```

Enfin modifiez le fichier `/etc/glance/glance-registry.conf`.
et commentez la ligne :
```
sqlite_db = /var/lib/glance/glance.sqlite
```

Puis rajoutez :
```
connection = mysql://glance:<PASSWORD>@10.0.0.4/glance
```

Puis Modifiez la partie keystone :
```
[keystone_authtoken]
identity_uri = http://10.0.0.4:35357
admin_tenant_name = service
admin_user = glance
admin_password = <PASSWORD>

[paste_deploy]
flavor = keystone
```

Enfin, on relance glance, et synchronise la db :
```
sudo service glance-api restart
sudo service glance-registry restart
sudo glance-manage db_sync
```

Pour tester:
```
source ~/.keystone_creds
glance image-create --name Cirros --is-public true --container-format bare --disk-format qcow2 --location https://launchpad.net/cirros/trunk/0.3.0/+download/cirros-0.3.0-x86_64-disk.img
glance image-list
```

### Nova

Nova est le cerveau d'openStack, c'est lui qui va faire tourner les différentes applis et gérer les différents serveurs.

#### Installation

```shell
sudo apt-get install -y nova-api nova-cert nova-conductor nova-consoleauth nova-novncproxy nova-scheduler python-novaclient nova-compute nova-console
```

Il lui faut également une BDD:

```shell
mysql -u root -p
mysql> CREATE DATABASE nova;
mysql> GRANT ALL ON nova.* TO 'nova'@'%' IDENTIFIED BY '<PASSWORD>';
mysql> quit
```

Et un utilisateur + endpoint keystone:

```shell
source ~/.keystone_creds
keystone user-create --name=nova --pass=b11ere5 --email=ares@ares-ensiie.eu
keystone user-role-add --user=nova --tenant=service --role=admin
keystone service-create --name=nova --type=compute --description="OpenStack Compute"
keystone endpoint-create --service=nova --publicurl=http://10.0.0.4:8774/v2/%\(tenant_id\)s --internalurl=http://10.0.0.4:8774/v2/%\(tenant_id\)s --adminurl=http://10.0.0.4:8774/v2/%\(tenant_id\)s
```

#### Configuration

Ajoutez les lignes suivantes à la fin du fichier `/etc/nova/nova.conf`:

```
rpc_backend = rabbit
auth_strategy = keystone
my_ip = 10.0.0.4
vnc_enabled = True
vncserver_listen = 10.0.0.4
vncserver_proxyclient_address = 10.0.0.4
novncproxy_base_url = http://10.0.0.4:6080/vnc_auto.html

network_api_class = nova.network.neutronv2.api.API
security_group_api = neutron
linuxnet_interface_driver = nova.network.linux_net.LinuxOVSInterfaceDriver
firewall_driver = nova.virt.firewall.NoopFirewallDriver

scheduler_default_filters=AllHostsFilter

[database]
connection = mysql://nova:<PASSWORD>@10.0.0.4/nova

[oslo_messaging_rabbit]
rabbit_host = 10.0.0.4
rabbit_port = 5673
rabbit_userid = guest

[keystone_authtoken]
identity_uri = http://10.0.0.4:35357
admin_tenant_name = service
admin_user = nova
admin_password = <PASSWORD>

[glance]
host = 10.0.0.4

[oslo_concurrency]
lock_path = /var/lock/nova

[neutron]
service_metadata_proxy = True
metadata_proxy_shared_secret = openstack
url = http://10.0.0.4:9696
auth_strategy = keystone
admin_auth_url = http://10.0.0.4:35357/v2.0
admin_tenant_name = service
admin_username = neutron
admin_password = <PASSWORD>
```

Puis synchroniser la bdd nova :

```shell
sudo nova-manage db sync
```

Enfin redémarrer nova:
```shell
sudo service nova-api restart
sudo service nova-cert restart
sudo service nova-consoleauth restart
sudo service nova-scheduler restart
sudo service nova-conductor restart
sudo service nova-novncproxy restart
sudo service nova-compute restart
sudo service nova-console restart
```

### Neutron

Neutron est le service openstack qui permet de gérer tout ce qui est lié au réseau.


#### Installation

```shell
sudo apt-get install -y neutron-server neutron-plugin-openvswitch neutron-plugin-openvswitch-agent neutron-common neutron-dhcp-agent neutron-l3-agent neutron-metadata-agent openvswitch-switch
```

La bdd:
```shell
mysql -u root -p
mysql> CREATE DATABASE neutron;
mysql> GRANT ALL ON neutron.* TO 'neutron'@'%' IDENTIFIED BY 'b11ere5';
mysql> quit
```

Et l'accès keystone:
```shell
source ~/.keystone_creds
keystone user-create --name=neutron --pass=<PASSWORD> --email=ares@ares-ensiie.eu
keystone service-create --name=neutron --type=network --description="OpenStack Networking"
keystone user-role-add --user=neutron --tenant=service --role=admin
keystone endpoint-create --service=neutron --publicurl http://10.0.0.4:9696 --adminurl http://10.0.0.4:9696  --internalurl http://10.0.0.4:9696
```

#### Configuration

Editez le fichier `/etc/neutron/neutron.conf` et faites correspondre la configuration suivante:

```
[DEFAULT]
...
service_plugins = router
...
auth_strategy = keystone
...
allow_overlapping_ips = True
..
notify_nova_on_port_status_changes = True
notify_nova_on_port_data_changes = True
nova_url = http://10.0.0.4:8774/v2
nova_admin_username = nova
nova_admin_tenant_id = <TENANT_ID>
nova_admin_tenant_name = service
nova_admin_password = <PASSWORD>
nova_admin_auth_url = http://10.0.0.4:35357/v2.0
...
notification_driver=neutron.openstack.common.notifier.rpc_notifier
rpc_backend=rabbit
...
[keystone_authtoken]
identity_uri = http://10.0.0.4:35357
admin_tenant_name = service
admin_user = neutron
admin_password = <PASSWORD>

[database]
connection = mysql://neutron:<DB_PASS>@10.0.0.4/neutron
...
[nova]
auth_url = http://10.0.0.5:35357
auth_plugin = password
project_name = service
username = nova
password = <PASSWORD>

[oslo_concurrency]
lock_path = /var/lock/neutron/

[oslo_messaging_rabbit]
rabbit_host = 10.0.0.4
rabbit_port = 5673
rabbit_userid = guest
```

Pour récuperer le TENANT_ID:
```shell
source ~/.keystone_creds
keystone tenant-get service
```

Puis le plugin ml2 :

Editez le fichier `/etc/neutron/plugins/ml2/ml2_conf.ini` pour le faire correspondre à :
