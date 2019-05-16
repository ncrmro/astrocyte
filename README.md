# Astrocyte

> ...\[are] cells in the brain and spinal cord. They perform many functions, 
including ...supporitng the cell that form the blood–brain barrier, 
provision of nutrients to the nervous tissue, maintenance of extracellular
ion balance, and a role in the repair and scarring process of the brain 
and spinal cord following traumatic injuries.
>
> -- <cite>Wikipedia</cite>

The main goal of Astrocyte in this context is to take baremetal hosts and
turn them into KVM hosts.

Then provision any KVM base guests with admin credentials, snapshot and 
then clone the base guest.

Then we can quickly spin up new pre configured guests for experimentation etc.

---

An additional goal is to provide cloud provider agnostic Virtual Machines
for things like off site backups, archives, kubernetes workers/ingress nodes.


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
