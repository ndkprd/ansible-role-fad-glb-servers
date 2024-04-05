# ansible-role-fortiadc-glb-servers

## Description

Ansible role to create/update Fortinet's FortiADC GLB Servers and its members using REST API. 

Since the GLB Servers need the Data Centers to be exist first, it depends on my other role: [ndkprd.fortiadc-glb-data-center](https://github.com/ndkprd/ansible-role-fortiadc-glb-data-center).

## Usage

### Install Role

#### From Galaxy

```
ansible-galaxy install ndkprd.fortiadc-glb-servers
```

#### From Github

##### Create Requirements File

```
---
# ./requirements.yaml

- name: ndkprd.fortiadc-glb-servers
  scm: git
  src: https://github.com/ndkprd/ansible-role-fortiadc-glb-servers
  version: main
```

```
ansible-galaxy install -r requirements.yaml
```

### Hosts Example

```
fad1.ndkprd.com fad_apitoken=mysupersecrettoken
fad2.ndkprd.com fad_apitoken=mysupersecrettoken
```

### Vars Example

```
---
fad_vdom: root

fad_glb_data_centers:
  - name: hq.ndkprd.com  # Data Center name/mkey
    location: ID  # 2 letters country ID
  - name: dc1.ndkprd.com    
    location: ID     
  - name: dc2.ndkprd.com
    location: ID

fad_glb_servers:
  - name: "dmz.hq.ndkprd.com" # GLB Server name/mkey
    data_center: "hq.ndkprd.com" # Data Center name, must exists first
    health_check_ctrl: enable
    health_check_list: "LB_HLTHCK_ICMP " # the whitespace is a must
    health_check_relationship: AND      
    server_type: Generic-Host # "Generic-Host" or "FortiADC-SLB"
    auth_type: none # Only for FortiADC-SLB
    address_type: ipv4 # FAD address type
    auto_sync: disable # Only for FortiADC-SLB
    fad_ipv4: "0.0.0.0" # Only for FortiADC-SLB
    fad_ipv6: "::" # Only for FortiADC-SLB
    fad_pass: "" # Only for FortiADC-SLB
    fad_port: "5858" # Only for FortiADC-SLB
    server_members: []

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
        ipv4: 36.92.152.196
        ipv6: "::"
        address_type: ipv4
        gateway: ""
        health_check_ctrl: disable # default value
        health_check_inherit: enable
        health_check_list: ""
        health_check_relationship: OR # default value
      - name: public-waf-2.dmz.dc1.ndkprd.com
        ipv4: 114.5.127.186
        ipv6: "::"
        address_type: ipv4
        gateway: ""
        health_check_ctrl: disable # default value
        health_check_inherit: enable
        health_check_list: ""
        health_check_relationship: OR # default value

  - name: "dmz.dc2.ndkprd.com"
    data_center: "dc2.ndkprd.com"
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
      - name: public-waf-1.dmz.dc2.ndkprd.com
        ipv4: 114.5.119.176
        ipv6: "::"
        address_type: ipv4
        gateway: ""
        health_check_ctrl: disable # default value
        health_check_inherit: enable
        health_check_list: ""
        health_check_relationship: OR # default value

```

### Playbook Example

```
---
# ./playbook.yaml

- name: Setup GLB Data Center entry in FortiADC.
  hosts: fortiadc
  become: false
  gather_facts: no
  vars_files:
    - ./fad-glb-servers-vars.yaml

  roles:
    - ndkprd.fortiadc-glb-servers

```

### About Tags

I added quiet lots of debug task, mainly to check if the variable I set is correct. These tags basically just print out the var that the previous task set/register. You can skip them altogether by skipping tasks with `debug` tags.

For example, if you're using CLI, you can just go `ansible-playbook playbook.yaml --skip-tags debug`.

If you don't need to run the Data Center role, you can also skip tasks tagged with `fad_glb_dc`.

## Limitation

Developed and tested against FortiADC 7.0.

## License

MIT, use at your own risk.