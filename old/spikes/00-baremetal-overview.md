# Baremetal Requirements


## Dev machine
The first baremetal host we're working with would be your main laptop/desktop of course.

It's a good idea to be working from a static hardline NIC configured with a .local search domain.

### Setup
`brew install python pipenv`

Create the virtualenv
`virtualenv ~/.venv/astrocyte`

Activate the virtualenv
`source ~/.venv/astrocyte/bin/activate`

Install project dependencies
`pipenv install`


`sudo fdisk -l`

### Ansible Vault

Create a password file
```
mkdir ~/.ansible ; \
echo "changeme2018" >> ~/.ansible/.vault_pass.txt && \
chmod 0444 ~/.ansible/.vault_pass.txt
```
To generate the secret file, run `ansible-vault create vars/secret` from the project root.

The first prompt will create your vault password.

The second prompt will send you to a vim session, in that vim sesson input or copy vault.example.yml from the vars folder

```
ansible_sudo_pass: your-sudo-password
```

Using nano and a password file this is nice in dev and maybe bad practice in prod.
```
export EDITOR=nano && \
export VAULT_PASSFILE=~/.ansible/.vault_pass.txt && \
ansible-vault create --vault-password-file $VAULT_PASSFILE vars/vault.yml
```

```
export EDITOR=nano && \
export VAULT_PASSFILE=~/.ansible/.vault_pass.txt && \
ansible-vault create --vault-password-file $VAULT_PASSFILE vars/aws.vault.yml
```

Editing the vault file
```
export EDITOR=nano && \
export VAULT_PASSFILE=~/.ansible/.vault_pass.txt && \
ansible-vault edit --vault-password-file $VAULT_PASSFILE vars/vault/main.yml

export EDITOR=nano && \
export VAULT_PASSFILE=~/.ansible/.vault_pass.txt && \
ansible-vault edit --vault-password-file $VAULT_PASSFILE vars/aws.vault.yml

export EDITOR=nano && \
export VAULT_PASSFILE=~/.ansible/.vault_pass.txt && \
ansible-vault edit --vault-password-file $VAULT_PASSFILE vars/vault.routing.yml
```

```
ansible-playbook -i hosts init.yml --vault-password-file ~/.ansible/.vault_pass.txt
```

### NAS - Earthdiver
The network attached storage is the backbone for our infrastructures storage. It should have an industrial grade SSD as a ZFS SLOG cache. 
More and more it seems it should not itself be a KVM host both to reduce software coverage and focus exclusively on delivering storage and backups.
Eg we're going for appliance level's here.
 
* NFS
* AFP
* Samba
* PXE
* Docker
  * Deliver services with complex dependencies
  * Act as a docker registrar
* ISO storage
* Remote ZFS backups.

### KVM Host - Leviathian
KVM host should have all data be consumed and and stored over NFS from the NAS.

### Guest Networking

dig +short @192.168.122.1 TXT CHAOS version.bind
systemd-resolve --status

bastion needs to be configured to listen on both bridge and the default .kvm network

### AWS Host - Leviathian
KVM host should have all data be consumed and and stored over NFS from the NAS.

### Network
Take a look at your infrastructure. Can you set your router up. Make sure to have all devices plugged in at assigned static IPs if not already configured.
We will also set the OS to use a static IP so if your not able to assign static IPS thats fine.



Hostnames used laters as well need to have - not underscore _


https://forums.freenas.org/index.php?threads/some-insights-into-slog-zil-with-zfs-on-freenas.13633/


https://en.wikipedia.org/wiki/OSI_model

Converting old hosts to KVM
http://manpages.ubuntu.com/manpages/bionic/man1/virt-p2v.1.html