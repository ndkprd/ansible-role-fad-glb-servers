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
fad_vdom: root

fad_glb_data_centers:
  - name: dc1.ndkprd.com # Data Center name
    location: ID # 2-letter country ID
    glb_servers:
      - name: "dmz.dc1.ndkprd.com" # GLB servers mkey
        health_check_ctrl: enable # "enable" or "disable"
        health_check_list: "LB_HLTHCK_ICMP " # must exists first, also, the FAD default one HAVE a single whitespace after the name, probably to make space to other HLTHCK
        health_check_relationship: AND # either "AND" or "OR"
        server_type: Generic-Host # "Generic-Host" or "FortiADC-SLB"
        auth_type: none # check FortiADC docs
        auto_sync: disable # "enable" only when server_type: FortiADC-SLB
        fad_ip: 0.0.0.0 # FortiADC IP if using server_type: FortiADC-SLB
        fad_pass: "" # FortiADC admin pass if using server_type: FortiADC-SLB
        fad_port: "5858" # FortiADC sync port if using server_type: FortiADC-SLB, use quote since it will be taken as string
        server_members:
          - name: waf1.dmz.dc1.ndkprd.com # GLB servers child mkey
            ip: 10.10.10.10 # GLB servers IP address
            health_check_inherit: enable # I just enable inherit to reduce headache
  - name: dc2.ndkprd.com
    location: ID # 2-letter country ID
    glb_servers:
      - name: "dmz.dc2.ndkprd.com"
        health_check_ctrl: enable
        health_check_list: LB_HLTHCK_ICMP
        health_check_relationship: AND
        server_type: Generic-Host
        auth_type: none
        auto_sync: disable
        fad_ip: 0.0.0.0
        fad_pass: ""
        fad_port: 5858
        server_members:
          - name: waf1.dmz.dc2.ndkprd.com
            ip: 10.20.10.10
            health_check_inherit: enable

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

## Limitation

Developed and tested against FortiADC 7.0.

## License

MIT, use at your own risk.