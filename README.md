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

This allows us spin up new pre configured guests for experimentation etc.

The main prequsite of Astrocyte is that you have a body of storage available. [Ocean](https://gitlab.com/jtco-io/ops/ocean)
is a project that utilizes ZFS to provide networked attached storage (NAS). This allows us to have as many hosts virtual
or baremetal that can all write to a single storage pool.

All hosts should refer to this pool of data on a network file share. This means all virtual machine images are available
to any astrocyte host. The iso directory can be allowed to be written for from a [Transmission](https://transmissionbt.com/)
vm. Further work to get iSCI shares working are in order. 

This separation of concerns means we use standard OS Ubuntu with a minimal package set. Data backups and integreity are
handled by the Ocean hardware and repos respectivly. Althought Ocean does maintain the ability to have Astrocyte
deployed to it.

Furthermore Astrocyte provisions the baremetal hosts and their respective VM guests with various tools to aid in security
and monitoring. These typically include Prometheus [Node exporter](https://github.com/prometheus/node_exporter) and
Graylog [Sidecar](https://docs.graylog.org/en/3.0/pages/sidecar.html).

---

An additional goal is to provide cloud provider agnostic Virtual Machines
for things like off site backups, archives, kubernetes workers/ingress nodes.

For instance all AWS servers should be secure and waiting for any domain specific ansible plays once astrocyte is done with them.

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

### Maintenance

ansible-playbook -i hosts init.yml
k
