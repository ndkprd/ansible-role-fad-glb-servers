# ansible-role-fortiadc-glb-servers

## Description

Ansible role to create/update Fortinet's FortiADC GLB Servers and its members using REST API. 

Since the GLB Servers need the Data Centers to be exist first, it depends on my other role: [ndkprd.fad-glb_data_center](https://github.com/ndkprd/ansible-role-fad-glb-data-center).

## Usage

### Install Role

```
ansible-galaxy install ndkprd.fad_glb_servers
```

### Hosts Example

```
fad1.ndkprd.com fad_apitoken=mysupersecrettoken fad_vdom=root
```

### Playbook Example

```
---
# ./playbook.yaml

- name: Update/create FortiADC GLB Data Centers resources.
  hosts: all
  become: false
  gather_facts: false
  connection: local
  vars:
     # for testing-purpose only, to delete the created resources after. change to 'false' if you want the resource to stay.
    do_cleanup: true
    # global-load-balance servers entries
    fad_glb_data_centers:
    - name: dc1.ndkprd.com
        location: ID
    - name: dc2.ndkprd.com
        location: ID
    - name: dc3.ndkprd.com
        location: ID
    fad_glb_servers:
    - name: "dmz.dc1.ndkprd.com"
        data_center: "dc1.ndkprd.com"
        health_check_ctrl: enable
        health_check_list: "LB_HLTHCK_ICMP "
        health_check_relationship: AND
        server_type: Generic-Host
        auth_type: none
        address_type: ipv4 # FAD address type
        auto_sync: disable
        fad_ipv4: "0.0.0.0"
        fad_ipv6: "::"
        fad_pass: ""
        fad_port: "5858"
        server_members:
        - name: public-waf-1.dmz.dc1.ndkprd.com
            ipv4: 10.10.1.2
            ipv6: "::"
            address_type: ipv4
            gateway: ""
            health_check_ctrl: disable
            health_check_inherit: enable
            health_check_list: ""
            health_check_relationship: "OR"

  roles:
    - ndkprd.fad_glb_servers


```

### About Tags

Each task is tagged with their task file name, `fad_glb_servers` and `fad_glb_servers_member`. 

There's also a some kind of pre-task and post-task that run before and after create/update tasks to compare the value of before and after create/update tasks, tagged with the above tag and an additional `debug` tag.

## Limitation

Developed and tested against FortiADC 7.0.

## License

MIT, use at your own risk.
