## Setup local machine
`brew install python pipenv`

Create the virtualenv
`virtualenv .venv`

Activate the virtualenv
`source ~/.venvbin/activate`

Install project dependencies
`pipenv install`

ansible-galaxy install ncrmro.kvm_host --force


### Ansible Vault
Create a password file

```
mkdir ~/.ansible ; \
echo "mypassword" >> ~/.ansible/.vault_pass.txt && \
chmod 0444 ~/.ansible/.vault_pass.txt
```

## Initial commands

ansible -i hosts -m ping all

ansible -i hosts -m shell -a 'uptime' all

### Maitnence

ansible-playbook -i hosts init.yml
ansible-playbook -i hosts nas.yml --limit @nas.retry


sudo zfs send -i rpool/ROOT/ubuntu@install rpool/ROOT/ubuntu@2019-03-06_H20 | sudo zfs recv ocean/backup/ROOT/ubuntu

ansible-playbook -i hosts init.yml --vault-password-file ~/.ansible/.vault_pass.txt

