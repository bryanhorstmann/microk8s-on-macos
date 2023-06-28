# MicroK8s on MacOS
A useful guide for getting MicroK8s running on MacOS and allowing access to the services from your network

<!-- toc -->

- [Installation](#installation)
  * [References](#references)
- [Multipass](#multipass)
- [Networking](#networking)
  * [Add additional NIC for local network access](#add-additional-nic-for-local-network-access)
  * [Configure new interface](#configure-new-interface)
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
### Add additional NIC for local network access
* Stop multipass VM and multipass
```
# stop the instance
$ multipass stop microk8s-vm

# stop multipass
$ sudo launchctl unload /Library/LaunchDaemons/com.canonical.multipassd.plist

# confirm multipass is stopped
$ multipass list
list failed: cannot connect to the multipass socket
```
* Add an extra interface in `/var/root/Library/Application\ Support/multipassd/qemu/multipassd-vm-instances.json`
```
        "extra_interfaces": [
            {
                "auto_mode": true,
                "id": "en0",
                "mac_address": "79:14:44:9d:89:2c"
            }
        ],
```
__NB:__ Make sure the MAC address is unique. Use [mac-address-generator](https://miniwebtool.com/mac-address-generator/) to generate one.
* Start multipass and multipass VM
```
# start multipass
$ sudo launchctl load /Library/LaunchDaemons/com.canonical.multipassd.plist

# confirm multipass is started
$ multipass list
Name                    State             IPv4             Image
microk8s-vm             Stopped           --               Ubuntu 22.04 LTS

# start the instance
$ multipass start microk8s-vm
```
### Configure new interface
* Enable DHCP on new interface
```
# Connect to instance shell
$ multipass shell

# Use ip command to find interface id. Mostly likely enp0s2
ubuntu@microk8s-vm:~$ ip addr

# Create netplan config for device
ubuntu@microk8s-vm:~$ sudo vim /etc/netplan/enp0s2.yaml
ubuntu@microk8s-vm:~$ cat /etc/netplan/enp0s2.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s2:
      dhcp4: true

# Apply the config
ubuntu@microk8s-vm:~$ sudo netplan apply
```
* Confirm new IP Address.
```
# multipass list
Name                    State             IPv4             Image
microk8s-vm             Running           192.168.64.2     Ubuntu 22.04 LTS
                                          <internal IP>
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
