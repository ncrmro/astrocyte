# astrocyte

## Project Scope & Methidologies

* Baremetal
* KVM Host
* KVM Guest Provisioning

### Plays
1. Set up baremetal eg disks and network interfaces.
1. Initialize KVM hosts and guests, as well as run docker and security tasks.
1. Initialize network storage


## Setup
`brew install python pipenv`

Create the virtualenv
`virtualenv ~/.venv/astrocyte`

Activate the virtualenv
`source ~/.venv/astrocyte/bin/activate`

Install project dependencies
`pipenv install`

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

Editing the vault file
```
export EDITOR=nano && \
export VAULT_PASSFILE=~/.ansible/.vault_pass.txt && \
ansible-vault edit --vault-password-file $VAULT_PASSFILE vars/vault.yml
```

```
ansible-playbook -i hosts init.yml --vault-password-file ~/.ansible/.vault_pass.txt
```

### KVM Guest Networking

dig +short @192.168.122.1 TXT CHAOS version.bind
systemd-resolve --status

bastion needs to be configured to listen on both bridge and the default .kvm network

### Naming
leviathian
earthdiver


ocean


https://github.com/stuvusIT/zfs-storage