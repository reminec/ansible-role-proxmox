# Reminec Ansible Role Proxmox

Install proxmox on a fresh debian Weezie (Debian 7) , reboot it, and :
 - Ability to create a simple 2nodes cluster
 - Create storages from configuration
 - Create containers from configuration
 
By default, disable proxmox pve-enterprise sources.list because we works with no subscription

## Requirements

library/pvectl/pvectl [https://github.com/reminec/ansible-library-pvectl]
library/xml/xml [https://github.com/cmprescott/ansible-xml]

## Role Variables

### Configuration example - 1 Proxmox instance
```yaml
# General
proxmox_restrict_webadmin: true
proxmox_pveproxy_allow_from: '127.0.0.1'

# ProxMox - Storage
proxmox_storages:
  - id: backup
    type: dir
    path: /var/lib/vz/dump
    maxFiles: 1
    content: backup # images,iso,vztmpl,backup
    nodes: myProxmoxHostname # hostname
    
# Specify a container
pve_containers:
  - ctid: 101
    hostname: 'vztest'
    ip_address: '10.10.10.2'
    memory: '8192'
    swap: '512'
    ostemplate: "{{ proxmox_template_path }}/debian-7.0-standard_7.0-2_amd64.tar.gz"
    # nameserver: '10.10.10.1'
    # searchdomain:
    onboot: 'yes'
    disk: '50'
    password: 'changeMe'
    cpus: 4
    cpuunits: 2000
```
### Configuration example - 2 nodes cluster (Work in Progress)
This role provide some task to create cluster from your current host, and add automatically nodes to the cluster by delegate task   
Actually only tested for create a simple 2nodes cluster
```yaml
# ... Copy here configuration example from 1 proxmox instance

# Cluster
proxmox_cluster: true # Enable cluster tasks
proxmox_cluster_master: true # The cluster will be created from the current host
proxmox_cluster_master_fqdn: proxmox.example.com # Specify the reverse hostname for external ipv4
proxmox_cluster_name: myCluster 
proxmox_cluster_internal_ipv4: 10.1.1.1 # Private address used by cluster

# Configure /etc/pve/cluster.conf - Only works for a 2nodes cluster
proxmox_cluster_conf:
  config_version: 4
  two_node: 1
  expected_votes: 1

# Other proxmox instances will be added to the cluster
proxmox_cluster_nodes:
  - hostname: proxmox2 # Short hostname
    ipv4: 10.1.1.2 # Private address used by cluster
    external_ipv4: 1.2.3.4 
    fqdn: proxmox2.example.com # Specify the reverse hostname for external ipv4
    
# You need to restric pveproy for your private network
proxmox_restrict_webadmin: true
proxmox_pveproxy_allow_from: '10.1.1.0/24'
```

### Optional vars

```yaml

proxmox_base_packages: # Install with apt manager
  - ntp
  - lvm2
  - postfix 
  - ksm-control-daemon 
  - vzprocps 
  - open-iscsi 
  - bootlogd

proxmox_template_path: "/var/lib/vz/template/cache" # Where proxmox store templates

proxmox_templates: # Define templates which you want download and store on proxmox
  - { filename: 'debian-7.0-standard_7.0-2_amd64.tar.gz', 
      url: 'ftp://download.proxmox.com/appliances/system/debian-7.0-standard_7.0-2_amd64.tar.gz',
      sha256sum: '1d114375beb93940ad296e64d3c1ddfc14d35fb00dfdf40c8eb4f4b70496b3c5' }

```

## Example Playbook

    - hosts: proxmox
      roles:
         - reminec.proxmox

## References
- http://openvz.org/Setting_up_an_iptables_firewall#Setting_up_a_firewall_that_allows_per-container_configuration
- https://blog.elao.com/fr/infra/creer-un-cluster-2-nodes-proxmox/
- http://www.nedproductions.biz/wiki/configuring-a-proxmox-ve-2.x-cluster-running-over-an-openvpn-intranet

## License
MIT - [See LICENSE](LICENSE)

## Credits
tasks/storage.yml copied from https://github.com/ElaoInfra/ansible-role-openvz

