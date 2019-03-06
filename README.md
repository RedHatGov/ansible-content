idm
=========

This role installs and configures RHEL Local Content Server (Repos, Registry, NFS).

Requirements
------------

- Expects a base RHEL system to target
- Red Hat Network account with a RHEL subscriptions available (for all content to sync)

Role Variables
--------------

| Variable        | Required | Default  | Description                                                                                                                                                                                                                                     |
| --------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `versions` | :x:      | ```see defaults/main.yml``` | Dictionary of Red Hat product versions |
| `disconnected` | :x:      | false | Is this running in a disconnected environment |
| `domain` | :x:      | ```hattrick.lab``` | The domain for the environment |
| `dns_server_public` | :x:      | ```1.1.1.1``` | The default upstream DNS server to use |
| `content_hostname` | :heavy_check_mark:      |  | The short hostname |
| `content_ssh_user` | :x:      | ```root``` | The default user to use for SSH access |
| `content_ssh_pwd` | :x:      | ```p@ssw0rd``` | The default password to use for SSH access. Obviously you'd change this :) |
| `content_public_ip` | :heavy_check_mark:      |  | The reachable public IP |
| `content_base_img` | :heavy_check_mark:      |  | Name of the base image located in /var/lib/libvirt/images on the KVM hypervisor |
| `content_vcpus` | :x:      | ```1``` | Number of vCPUS required |
| `content_ram` | :x:      | ```4096``` | Amount of ram required in megabytes |
| `content_os_disk_name` | :x:      | ```{{ idm_hostname }}``` | Name of the OS disk in /var/lib/libvirt/images |
| `content_os_disk_size` | :x:      | ```45G``` | Size of OS disk |
| `content_nics` | :heavy_check_mark:      | ```see example playbook``` | Dictionary of NICs to create |
| `content_ht_git_repo` | :x:      | ```https://github.com/redhat-kejones/ht.git``` | Location of Project Hat Trick repository |
| `content_sync_repos` | :x:      | true | Boolean to sync Red Hat repos |
| `content_repos` | :x:      | ```see defaults/main.yml``` | Dictionary of Repos to enable |
| `content_packages` | :x:      | ```see defaults/main.yml``` | Dictionary of Packages |
| `content_sync_satellite` | :x:      | false | Boolean to sync repos for Satellite (requires Satellite subscription) |
| `content_sync_registry` | :x:      | true | Boolean to sync container images to local registry |
| `content_ocp_tag` | :x:      | ```v3.11.43``` | Tag for versions of OCP images to grab |
| `content_rh_registry_username` | :heavy_check_mark:      |  | Service account username from https://access.redhat.com/terms-based-registry |
| `content_rh_registry_token` | :heavy_check_mark:      |  | Service account token from https://access.redhat.com/terms-based-registry |
| `content_ocp_images` | :x:      | ```see defaults/main.yml``` | Dictionary OCP container images to sync |

Dependencies
------------

- RedHatGov.rhsm

Example Playbook
----------------

```yaml
---
- hosts: content
  tags: install
  vars:
    domain: "example.com"
    dns_server_public: 1.1.1.1
    content_hostname: content #Short hostname
    content_ssh_user: root
    content_ssh_pwd: p@ssw0rd
    content_public_ip: "192.168.122.8"
    content_base_img: rhel-guest-image-7.qcow2
    content_vcpus: 1
    content_ram: 1024
    content_os_disk_name: "{{ content_hostname }}"
    content_os_disk_size: 300G
    content_nics:
      - name: eth0
        bootproto: static
        onboot: yes
        ip: "{{ content_public_ip }}"
        prefix: "24"
        gateway: "192.168.122.1"
        dns_server: "{{ dns_server_public }}"
        config: "--type network --source default --model virtio"
    #NOTE: Create/Get username/token from https://access.redhat.com/terms-based-registry/
    content_rh_registry_username: "1234567|rhnserviceaccount"
    content_rh_registry_token: "..."
  tasks:
    - name: Install content server
      include_role:
        name: content
      tags: install
```

License
-------

GPLv3

Author Information
------------------

[Red Hat North American Public Sector Solution Architects](https://redhatgov.io)
