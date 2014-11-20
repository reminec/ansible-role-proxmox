Role Name
=========

Install proxmox on a fresh debian 7 

Requirements
------------

...

Role Variables
--------------

Required:

Optional:

```
proxmox_eth0_ipv4 # (ip which set in /etc/hosts for current hostname) default value = ansible_default_ipv4.address

proxmox_base_packages: # Install with apt manager
  - ntp
  - lvm2
  - postfix 
  - ksm-control-daemon 
  - vzprocps 
  - open-iscsi 
  - bootlogd

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: proxmox
      roles:
         - { role: wotol_proxmox, proxmox_eth0_ipv4 = 10.0.0.1 }

License
-------

...

Author Information
------------------

...
