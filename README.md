#### Ansible Playbooks  
* Cisco and Palo Alto playbooks  
* Jinga2 to create playbook with_items  

**Jinga2**  
Python based templating engine.   Used to build Ansible Playbooks.  
Playbook with static and dynamic variables.  
Hostname of a device as a static, per interface router address as a loop variable (pulled in from spreadsheet for example)  
```{# jinga2 file for main.yml to build network config #}
static switch config here
!
{# call variables from main.yml #}
hostname {{static_objects.hostname}}
!
{#interface loop#}
{% for x in loop_objects %}
interface {{x.interface}}
 ip vrf forwarding {{x.vrf}}
 ip address {{x.ip}}
 no shutdown
 !
{% endfor %}
```
**Ansible loop using parents/with_items**    
```
tasks:
  - name: loop standard config
    ios_config:
      lines:
        - no shutdown
        - mtu 9202
      parents: "{{ item }}"
    loop:
      #with_items:
        - interface vlan12
        - interface vlan22
        - interface vlan32
```
**static playbook with no read_lines variables**  
```
tasks:
  - name: int description
    ios_config:
      lines:
        - vlan 13
        - name A1-13
        - vlan 23
        - name A2
        # VRF-A1 config
        - interface vlan13
        - vrf member A1
        - ip address 100.123.x.x 255.255.255.248
        # VRF-A2 config
        - interface vlan23
        - vrf member A2
        - ip address 100.123.x.x 255.255.255.248
```
**Playbooks can also be *stacked ~built to call other playbooks.**  
```
- import_playbook: metric_before.yml
- import_playbook: te_config.yml
- import_playbook: metric_after.yml
```

#### Ansible server access and playbook execution

SSH to Ansible server:
```
`ssh -i "keypair.pem" ec2-user@x.x.x.x`
```

Executine a playbook:
```
ansible-playbook ./te_chg_books.yml --extra-vars "sso=user pass=pass"
```
