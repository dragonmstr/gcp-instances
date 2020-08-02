Role Name
=========

Provision compute instance(s) on GCP with production grade requirements.

- [x] instance(s)
- [x] dedicated boot-disk
- [x] dedicated data and log disk
- [x] a static IP

Requirements
------------
 
- _ansible_ client
- _ansible.cfg_ file and enable _gcp_compute_ plugin
- service account and gcp account

Role Variables
--------------

Role, on default, supports *standalone* provisioning. If you want *distributed* provisioning, you can provide more nodes, with appropriate naming convention, as list in _your_ running yaml file.

defaults/main.yml, 
```
# GCP project
general:
    project: sandbox-236618       #gcp project ID 
    region: europe-west4          #gcp region
    auth_kind: serviceaccount     
    service_account_file: ~/.ssh/ansible.json
scopes:
    - https://www.googleapis.com/auth/compute

# design your infrastructure architecture
nodes:
  - name: standalone-node    #node-name such as standalone-node/name-node/secondary-name-node/data-node1/data-node2 ..
    zone: europe-west4-a     #choose close regions, distribute to other nodes
    machine_type: f1-micro   #increase when needed
    ips:
        - nic: 
            name: "ip-01"
    tags: hadoop             #network tags used by FW rules! Use the tool name like hadoop, elasticsearch ...
    labels:                  #filtering! Use the tool name as type + master/worker as name
        type: hdfs
        name: master
    boot_disk:
        - disk:
            name: "boot-disk-01"
            size: 100
            image: "projects/ubuntu-os-cloud/global/images/family/ubuntu-2004-lts"
            auto_delete: true
            boot: true
    additional_disks:
        - disk: 
            name: "data-disk-01"
            size: 300
            auto_delete: true     #false is better for production usecases
            boot: false
        - disk: 
            name: "log-disk-01"
            size: 300
            auto_delete: true     #false is better for production usecases
            boot: false
```

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

[Click](https://github.com/ansible-injection/test-gcp-iaas-roles) to test and see example playbooks.

for provision.yaml file:

```
- name: Create Compute Instance(s)
  hosts: localhost
  gather_facts: no

   vars:
     general:
         project: sandbox-236618
         region: europe-west4
         auth_kind: serviceaccount
         service_account_file: ~/.ssh/ansible.json
     scopes:
         - https://www.googleapis.com/auth/compute
          
  tasks:

  roles:
    #run w/ default var values
    - role: tansudasli.gcp_instances  
```

License
-------

Apache-2.0

Author Information
------------------

[tansudasli](http://github.com/tansudasli)
