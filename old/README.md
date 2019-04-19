# Astrocyte
A set of [Ansible](https://ansible.com) plays and tasks to provision your own private intranets.

This article `Explain how to think about ansible and how to use it` found 
[here](https://gist.github.com/MrNice/89a3bbe44e218c9d2309), can help you understand whats happening

### Host Overview
* NAS - Durable Storage
* VM Providers - Baremetal Hypervisor
    * KVM
    * Cloud - AWS, etc
* Router - Routing and Name Resolution

## Project Scope, Philosophy & Methodologies

The idea being, while cloud providers such as the one below are available.
Running your own hybrid internal infrastructure alongside cloud providers can be easy, cost-effective and highly preformat. 
 
* [AWS](https://aws.amazon.com)
* [Azure](https://azure.microsoft.com)
* [Google Cloud](https://cloud.google.com)
* [Digital Ocean](https://digitalocean.com)
* [Vultr](https://vultr.com)

This means functional parity with the above on the following
 
* dynamic allocation of resources
* durable storage
* routing

This boilerplate should get each host and service to an appliance like standard, having only the nessary packages needed,static IP's set and ports firewalled.
Eg The nas should 

### Baremetal Provisioning
Setup and secure various Baremetal, VM guests and Docker hosts. 

* Provision Hosts
    * KVM Baremetal Hypervisor Hosts
        * VM Guests
    * Cloud VM's
* Networking
    * Setup Services Domain & Zones
    * WAN & LAN Routing (Should Resolve too LAN when available)
* Package Updates & Upgrades
* Storage
    * Networked File Systems NFS/iSCSI/Samba/AFP
    * Backups
    * Data Scrubs
* Remote Access & Security
* Install and Configure Docker
    
###  Service Provisioning 
Set up services dependent services such as proxies and databases, then start ops services. 

* [Traefik](https://traefik.io) Edge Router & Proxy
* Databases & Stateful backends 
    * [Prometheus](https://prometheus.io)
    * [Postgres](https://postgresql.org)
    * [Mongo](https://mongodb.com)
    * [Elasticsearch](https://elastic.co)
* Ops
    * [Grafana](https://grafana.com) - Data visualization & Monitoring
    * [Graylog](https://graylog.org) - Log Management

### Plays
1. Set up baremetal eg disks and network interfaces.
1. Initialize KVM hosts and guests, as well as run docker and security tasks.
1. Initialize network storage

## Origins of Name - [Astrocytes](https://en.wikipedia.org/wiki/Astrocyte)

_...are cells in the brain and spinal cord... They perform many functions,
including support of cells that form the blood-brain barrier,
provision of nutrients tissue, maintenance of extracellular ion balance,
and a role in the repair and scarring process of the brain and spinal cord following traumatic injuries._

### Unique Naming Scheme
Giving various entities in your infrastructure unique names is a good idea as they give you quick identification of what system your working with.

This idea can further be expanded by wrapping your ideas in human allegory to give deeper meaning and subconscious indication of what each service does and how it's structured.
  
The names should be able to be shortened `ssh dive` or `ssh levi` for example.

Ansible inventories should be structured such that hostnames can easily be refactored to anything you like.

### [Botlzmann Brain](https://en.wikipedia.org/wiki/Boltzmann_brain) - Router
_a self-aware entity that arises due to extremely rare random fluctuations out of a state of thermodynamic equilibrium. 
For example, in a homogeneous Newtonian soup, theoretically by sheer chance all the atoms could bounce off and stick to 
one another in such a way as to assemble a functioning human brain (though this would, 
on average, take vastly longer than the current lifetime of the Universe).._

### [Earthdiver](https://en.wikipedia.org/wiki/Creation_myth#Earth-diver) - NAS
_Earth diver creation in which a diver, usually a bird or amphibian sent by a creator,
 plunges to the seabed through a primordial ocean to bring up sand or mud which develops into a terrestrial world...
 is among the first of them to awaken and 
 lay the necessary groundwork by building suitable lands where the coming creation will be able to live._

#### [Cosmic Ocean](https://en.wikipedia.org/wiki/Cosmic_ocean) - ZFS Raid Pool - Durable Storage
_A mythological motif found in the mythology of many cultures and civilizations, representing the world or cosmos as enveloped by primordial waters._

### [Leviathian](https://en.wikipedia.org/wiki/Leviathan) - KVM High performance Compute Host
_A primeval...dragon and world serpent...
with a literal meaning of "wreathed, twisted in folds"...
The word Leviathan has come to refer to any sea monster._

### [Hakata](https://en.wikipedia.org/wiki/Battle_of_Bun%27ei) - Public Infrastructure Access Point
_First Battle of Hakata Bay, was the first attempt... by the Mongols to invade Japan... Kublai Khan's fleet... landed at Hakata Bay,
Despite the superior weapons and tactics of the Mongols, the Yuan forces that disembarked at Hakata Bay were grossly outnumbered by the samurai force; 
A typhoon that night, said to be divinely conjured wind, threatened their ships, persuading them to return to Korea. Many of the returning ships sank that night due to the storm._