# Reminec Ansible Role Proxmox

 - Install proxmox on a fresh debian jessie (Debian 8) 
 - reboot machine
 - Create storages from configuration
 - Create containers from configuration
 
By default, disable proxmox pve-enterprise sources.list because we works with no subscription    

## Requirements
- Should work with Ansible 1.7.2+ | 2.0 otherwise

## Role Variables

### Configuration example
```yaml
# General
proxmox_restrict_webadmin: true
proxmox_pveproxy_allow_from: '127.0.0.1' # Can be '10.0.0.0/24'


# ProxMox - Storage
proxmox_storages:
  - id: backup
    type: dir
    path: /var/lib/vz/dump
    maxFiles: 1
    content: backup # images,iso,vztmpl,backup
    nodes: myProxmoxHostname # hostname
    
# Specify a container
proxmox_containers:
  - vmid: 112
      api_host: '{{ proxmox_api_host }}'
      api_user: root@pam
      api_password: '{{ proxmox_api_password }}'
      node: 'proxmox2'
      hostname: 'test.example.me'
      netif: '{"net0":"name=eth0,bridge=vmbr2,gw=10.0.0.1,ip=10.0.0.13/16"}'
      memory: '2048'
      swap: '512'
      ostemplate: "local:vztmpl/debian-8.0-standard_8.0-1_amd64.tar.gz"
      onboot: 'yes'
      disk: '10'
      password: 'YourRootPasswordHere'
      cpus: 2
      cpuunits: 3000
      state: 'present'
```

### Optional vars

```yaml

proxmox_base_packages: # Install with apt manager
  - proxmox-ve
  - ntp
  - ssh
  - postfix
  - ksm-control-daemon
  - open-iscsi
  - systemd-sysv
  
# Network
proxmox_interfaces_template: my_template_path/interfaces.new.j2 # Modify /etc/network/interfaces.new use by proxmox on next reboot
```

*/etc/network/interfaces.new* - Example with an OVH dedicated server:
```
auto lo
iface lo inet loopback

auto vmbr0
iface vmbr0 inet static
    address w.x.y.z # PUBLIC IP
    netmask 255.255.255.0
    network w.x.y.0 
    broadcast w.x.y.255
    gateway w.x.y.1
    bridge_ports eth0
    bridge_stp off
    bridge_fd 0

auto vmbr2
iface vmbr2 inet static
    address 10.0.0.1
    netmask 255.255.0.0
    bridge_ports none
    bridge_stp off
    bridge_fd 0
    post-up echo 1 > /proc/sys/net/ipv4/ip_forward

# If you use OpenVPN on the host
auto openvpnbr0
iface openvpnbr0 inet static
        address 10.1.0.1
        netmask 255.255.0.0
        network 10.1.0.0
        broadcast 10.1.255.255
        bridge_ports dummy1
        bridge_stp off
        bridge_fd 0
        post-up route add -net 224.0.0.0 netmask 240.0.0.0 dev openvpnbr0
```

## Example Playbook

    - hosts: proxmox
      roles:
         - reminec.proxmox

## License
MIT - [See LICENSE](LICENSE)

## Credits
tasks/storage.yml copied from https://github.com/ElaoInfra/ansible-role-openvz

