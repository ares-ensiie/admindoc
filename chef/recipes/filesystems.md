# Chef recipes : filesystems

Cette recipe permet le montage de système de fichiers sur une machine.

## NFS

Pour monter un fichier NFS sur la node il suffit de définir la variable suivante:

```json
"fs":{
  "nfs":{
    "local_mountpoint":"distant_endpoint"
  }
}
```

Ex:

```json
"fs":{
  "nfs":{
    "/openstack":"10.0.0.1:/var/openstack"
  }
}
```
