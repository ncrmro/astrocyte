# Install project dependencies

```bash
pipenv install
```

```bash
ansible-galaxy install ncrmro.kvm_host --force --roles-path=roles/galaxy
```

### Ansible Vault
Create a password file

```bash
mkdir ~/.ansible ; \
echo "mypassword" >> ~/.ansible/.vault_pass.txt && \
chmod 0444 ~/.ansible/.vault_pass.txt
```

## Initial commands

ansible -i hosts -m ping all

ansible -i hosts -m shell -a 'uptime' all

### Maitnence

ansible-playbook -i hosts init.yml
