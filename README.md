Role Name
=========

Provision compute instance(s) on GCP with production grade requirements.

- [x] instance(s)
- [x] dedicated boot-disk
- [x] dedicated and mounted data and log disk
- [x] a static IP

Requirements
------------

It requires: 
- ansible client
- service account and google account
- gcp module in ansible.cfg

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
    service_account_file: ~/.ssh/ansible-sandbox-236618-5fd34807ceb2.json
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

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

[tansudasli](http://github.com/tansudasli)
