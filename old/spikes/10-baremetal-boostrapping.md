# Baremetal Bootstrapping


## Install
* Provisioning Tools
* KVM

# Bootstrapping
Boostrapping is done is such a way that the hosts are confirmed to be available or 
not based on SSH accesability
* Baremetal Hosts
  * NIC
  * Storage  
* KVM Base Guest
* KVM Guest

# Secure
** ssh-ed25519

# Backups
** ssh-ed25519

docker-machine create \
    --driver generic \
    --generic-ip-address=lemnos.nodes.jtco.services \
    --generic-ssh-user ncrmro \
    --generic-ssh-key ~/.ssh/id_ed25519 \
    --generic-ssh-port 23761 \
    --generic-engine-port 23760 \
    lemnos