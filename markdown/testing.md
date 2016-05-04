<!-- .slide: data-state="section-break" id="testing" data-menu-title="Testing" -->
# Testing


<!-- .slide: data-state="normal" id="create-vm" -->
## Create a VM

Let's create a VM to check evacuation/resurrection works

```sh
source .openrc
openstack image list
openstack flavor list
neutron net-list
nova boot --image imageID --flavor flavorID --nic net-id=netID testvm
```

Let's get it a floating IP

```sh
neutron floatingip-create floatingnetID
nova list # get vmIP
neutron port-list | grep vmIP # get portID
neutron floatingip-associate floatingipID portID
```

The VM uses the default security group. Make sure it has ICMP.


<!-- .slide: data-state="normal" id="test-vm" -->
## Test VM

We should be able to ping/SSH that VM
Check in which host the VM is running
```
nova list --fields host,name
```

