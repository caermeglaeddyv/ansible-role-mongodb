Ansible role: MongoDB
=========

This role is used to install mongodb replica set.

For now, it does the following:
- generates MongoDB root CA and server certificates if needed
- if separate disk path if defined, creates lvm volumes from it
- does all necessary OS preparations
- enables access control and replication in config, initializes replica set
- creates databases, users and rights if needed


Requirements
------------

This is not strict requirements and it may not work with other versions than tested ones.
Anyway. feel free to test by yourself, suggest addition of new functionality and contribute.

Role is tested with:
- Ansible version >= 2.8.6
- CentOS version >= 7.6 (1803)

Currently supports installation of MongoDB versions >= 4.2.

cfssl and cfssljson must be installed on localhost if mongodb_cfssl variable is set to True (default).


Role Variables
--------------

Variables and their descriptions copied from defaults/main.yml

```bash

# MongoDB is designed to work better with Transparent Huge Pages turned off -
# https://docs.mongodb.com/manual/tutorial/transparent-huge-pages/, it's better
# not to change this setting regardless of your RAM size, includes no_thp role as dependency:
mongodb_no_thp: true

# Use CFSSL to create MongoDB ReplicaSet Root CA and self-signed certificates:
mongodb_cfssl: true

# Variable which is common for most projects, used in
# configuration files or file/directory names.
# By default used as reference for etcd_project_dir variable:
mongodb_project_name: test

# Variable which is common for most projects, used as
# project working directory on the localhost for the role.
# Currently is used to store created certificates and keyfile:
mongodb_project_dir: "{{ mongodb_project_name }}"

# Name of separate device in /dev/disk/by-path/ for MongoDB storage:
mongodb_disk_path: ""          # pci-0000:13:00.0-scsi-0:0:0:0

# Version of MongoDB package which will be installed:
mongodb_version: 4.2

# Revision of MongoDB package which will be installed:
mongodb_revision: 1

# MongoDB admin user's password on replica set members:
mongodb_admin_pass: adminpass12345

# Hostname, DNS name or IP address to bind MongoDB server on,
# will bind to default ipv4 address if empty. It's recommended
# to use logical DNS hostname when possible:
mongodb_bind_ip: ""

# Name of MongoDB replica set which will be created:
mongodb_replicaset_name: myreplicaset

# Specifies whether a certificate is required from the other side
# of the connection, and whether it will be validated if provided:
mongodb_replicaset_ssl_cert_reqs: CERT_NONE

# List of MongoDB replica set members if you want to use ones
# different from "ansible_play_batch" names for hostname verification:
mongodb_replicaset_members: []
# - abc.xyz
# - def.xyz

# Database, users and rights to create after initializing MongoDBreplicaset:
mongodb_init_dbs: []
# - database: test
#   username: test_user
#   password: Test_p@ssw0rd!
#   roles:
#   - db: test
#     role: dbOwner

```


Dependencies
------------

If mongodb_no_thp variable set to True (default), installs no_thp role to permanently disable Transparent Huge Pages, which is strongly [recommended](https://docs.mongodb.com/manual/tutorial/transparent-huge-pages/).

cfssl and cfssljson must be installed on localhost if mongodb_cfssl variable is set to True (default).


Example Playbook
----------------

```bash
---
- hosts: localhost
  gather_facts: false
  become: no
  tasks:
  - name: Check ansible version >=2.8.6
    assert:
      msg: Ansible must be v2.8.6 or higher
      that:
      - ansible_version.string is version("2.8.6", ">=")
    tags:
    - check
  vars:
    ansible_connection: local

- hosts: all
  become: yes
  tasks:
  - import_role:
      name: mongodb

```

More detailed examples ( inventories, playbooks etc. ) of this and other roles can be found [here](https://github.com/caermeglaeddyv/examples/tree/dev/ansible).

It's highly recommended to start you test deploys from there, especially if you use Google Cloud Platform or VMware vCenter as your infrastructure, for now that [repository](https://github.com/caermeglaeddyv/examples) contains [Packer](https://github.com/caermeglaeddyv/examples/tree/dev/packer) and [Terraform](https://github.com/caermeglaeddyv/examples/tree/dev/terraform) examples to build templates and deploy machines on this platforms.


License
-------

[Apache 2.0](https://github.com/caermeglaeddyv/ansible-role-rear/blob/dev/LICENSE)


Author Information
------------------

Copyright 2020 [caermeglaeddyv](https://github.com/caermeglaeddyv)
