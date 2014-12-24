Wotol Ansible Role Proxmox
=========

Install proxmox on a fresh debian 7 , reboot it, and create new VM if it provided

By default, disable proxmox pve-enterprise sources.list because we have no subscription

Requirements
------------

library/openvz/openvz
library/openvz/pvectl

Role Variables
--------------

Optional:

```yaml
proxmox_eth0_ipv4:10.0.0.1 # (ip which set in /etc/hosts for current hostname) default value = ansible_default_ipv4.address

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
      sha256sum: '1d114375beb93940ad296e64d3c1ddfc14d35fb00dfdf40c8eb4f4b70496b3c5' # If no sha256sum, ansible download file each time :( }

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
    password: 'test314'
    cpus: 4
    cpuunits: 2000

```

Dependencies
------------

wotol_common

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: proxmox
      roles:
         - { role: wotol_proxmox, proxmox_eth0_ipv4 = 10.0.0.1 }

References
----------
http://openvz.org/Setting_up_an_iptables_firewall#Setting_up_a_firewall_that_allows_per-container_configuration

License
-------

...

Author Information
------------------

...

