Examen de open stack, tengo que ir a lo que será mi carpeta del proyecto y tengo que hacer lo siguiente, para que funcione:

- `source Proyecto\ de\ amorgon204-openrc.sh `

Luego creo el fichero cloud que nos pide , el cual voy a llamar **cloud-init-skadi.yaml**, el cual es el siguiente:

```
#cloud-config
hostname: skadi
fqdn: skadi.andy.gonzalonazareno.org
manage_etc_hosts: true

users:
  - name: profesor
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCmjoVIoZCx4QFXvljqozXGqxxlSvO7V2aizqyPgMfGqnyl0J9YXo6zrcWYwyWMnMdRdwYZgHqfiiFCUn2QDm6ZuzC4Lcx0K3ZwO2lgL4XaATykVLneHR1ib6RNroFcClN69cxWsdwQW6dpjpiBDXf8m6/qxVP3EHwUTsP8XaOV7WkcCAqfYAMvpWLISqYme6e+6ZGJUIPkDTxavu5JTagDLwY+py1WB53eoDWsG99gmvyit2O1Eo+jRWN+mgRHIxJTrFtLS6o4iWeshPZ6LvCZ/Pum12Oj4B4bjGSHzrKjHZgTwhVJ/LDq3v71/PP4zaI3gVB9ZalemSxqomgbTlnT jose@debian
    sudo: ['ALL=(ALL) NOPASSWD:ALL']

  - name: andy
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC00lFf6Jyj2o41CRmIrIvguHHYKfaByakRVvyoVjfTyZDxDA5PIsAW0JHAKW9V2+MMREjAeY74LiDMJlyc1XHSkEFmzrKXLQ9d8M81WV9vYh2aOB3yHWZq3/CwxpHgcgatlTRKaP310y4mfsbkdbuDAcgE7jwO0k6KlibpUSIet2bUXZ3zagrMNhmDrHErYh+ARW37cm2+OqQdv1l+GeOBlFCTC6yAsOdpbJ5VQ0fWwvR6bl5DUpGBr0RDWE7HQLHGt3WE2nNKCii+myzGc17kU1ERfi7C2aI80VWdQMPqwLUpam/TdNahvw1tZ3lkrjSHVS330Ll7T+uT8WXU4dHEgtlgSNEHszxrQoQcAv+KYzkzi7oMi42xEpdW378WeKcEHgcaKK+gIqR1UHBI8cfOYiduMbnqcySaSEs3YIAk3MaBtSBn4GkkMFWaYNzUK2Ic5MQIOZ/ZkQyzDYLfpVcf6i42bHTLkJvR1mNSDYiL6M3xAp76ws/3ETx0OcdiAf0= madandy@toyota-hilux
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
package_update: true
package_upgrade: true

runcmd:
  - echo "nameserver 8.8.8.8" >> /etc/resolv.conf

```

Una vez hecho esto ceo la instancia llamada **skadi** en Openstack, por lo que uso el comando:

```
openstack server create skadi \
  --flavor vol.medium \
  --image "Ubuntu 22.04" \
  --network "Red DMZ de andy" \
  --user-data cloud-init-skadi.yaml \
  --boot-from-volume 15

```

Asigno la ip estatica a skadi, por lo que primero creo el puerto, y luego lo asigno:

- Creación del puerto.
```
openstack port create --network "Red DMZ de andy" --fixed-ip ip-address=172.16.0.100 skadi-port
```
- Asignación del puerto

```openstack server add port skadi skadi-port```


Ahora creo el contenedor como se llame en Odin 

1. Creo el contenedor "Como se llame" en Odin : `lxc launch ubuntu:22.04 balder`
2. Configuro la red del contenedor:  ```lxc config device add balder eth0 nic name=eth0 nictype=bridged parent=br0 ipv4.address=192.168.0.4```

3. Configuro el contenedor:
```
lxc config set balder boot.autostart true
lxc config set balder raw.lxc "lxc.net.0.mtu = 1450"
```
4. Accedo al contenedor y configuro el usuarioy los permisos
```
lxc exec balder -- bash

adduser profesor
echo "profesor ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/profesor

adduser andy
echo "andy ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/andy


exit
```

