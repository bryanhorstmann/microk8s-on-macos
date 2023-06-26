# MicroK8s on MacOS
A useful guide for getting MicroK8s running on MacOS and allowing access to the services from your network

<!-- toc -->

- [Installation](#installation)
  * [References](#references)
- [Multipass](#multipass)
- [Networking](#networking)
  * [References](#references-1)
- [Storage](#storage)
  * [References](#references-2)

<!-- tocstop -->

## Installation

### References
- https://microk8s.io/docs/install-macos


## Multipass
```
multipass set client.primary-name=microk8s-vm
multipass list
multipass shell
```

## Networking
```
→ multipass shell
```
```
ubuntu@microk8s-vm:~$ sudo -i
root@microk8s-vm:~# cd /etc/netplan/
root@microk8s-vm:/etc/netplan# vim enp0s2.yaml
root@microk8s-vm:~# cat /etc/netplan/enp0s2.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s2:
      dhcp4: true
```
```
microk8s stop
microk8s start
```
```
→ multipass list
Name                    State             IPv4             Image
microk8s-vm             Running           192.168.64.4     Ubuntu 22.04 LTS
                                          192.168.86.108
                                          10.1.254.64
```

### References
- https://github.com/canonical/multipass/issues/2476#issuecomment-1063259237
- https://github.com/canonical/multipass/issues/2476#issuecomment-1437334269
- https://github.com/canonical/netplan/blob/main/examples/dhcp.yaml
- https://miniwebtool.com/mac-address-generator/


## Storage
### References
- https://multipass.run/docs/mount-command
- https://multipass.run/docs/improve-mount-performance#heading--nfs-mounts
- https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-volumes-example-nfs-persistent-volume.html
- https://microk8s.io/docs/nfs
