Role Name
=========

This role install MongoDB client, MongoDB server or MongoDB cluster v3.0.x or v3.2.x or v3.4.x

Requirements
------------

- Ubuntu Trusty

Role Variables
--------------

     #
     # variables
     #
     mongodb_major_version: "3.0" or "3.2" or "3.4"
     mongodb_minor_version: latest or 3.0.4 or 3.2.0 or 3.4.0
     mongodb_port: 27017
     mongodb_bind_ip: 127.0.0.1, 0.0.0.0 for clustering
     # LVM specific
     mongodb_use_lvm: "true"
     mongodb_vg_name: vg_vroot, vg_root for physical machine
     mongodb_lv_name: lv_mongo
     mongodb_lv_size: 10g (min 4go )
     #
     mongodb_datadir: /var/lib/mongodb
     mongodb_conf_file: /etc/mongod.conf
     mongodb_engine: mmapv1 or wiredTiger
     mongodb_physical_disk: sda, only for physical machine
     # Cluster specific
     mongodb_cluster: "false"
     mongodb_member_priority: 1
     mongodb_member_votes: 1
     mongodb_member_arbiterOnly: false
     mongodb_member_hidden: false

     #
     # variables in secret.yml
     #
     mongodb_admin_password: <admin user pass>
     mongodb_backup_password: <backup user pass>
     # Cluster specific (Min 6 characters)
     mongodb_cluster_key: <cluster key>

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Notes:
- The first node must have the biggest priority when the cluster is created
- If the "mongodb_member_hostname" variable is not set, the variable is equal to the inventory node name
- The host group named "MongoDB_Cluster" in inventory file can be overridden by the "mongodb_inventory_cluster_name" variable

Inventory file "hosts" example:

     [MongoDB_Cluster]
     node1.network.local mongodb_member_hostname=node1.prod.local mongodb_member_priority=2 mongodb_member_priority=2
     node2.network.local mongodb_member_priority=2
     node3.network.local mongodb_member_priority=0 mongodb_member_votes=1 mongodb_member_arbiterOnly=true mongodb_member_hidden=true

Playbook example:

     - name: deploy MongoDB
       hosts: node1
       #remote_user: <your username>
       become: yes
       vars_files:
       - secret.yml
       vars:
         mongodb_engine: "wiredTiger"
       roles:
         - ../roles/ansible-mongodb

Playbook example to install a v3.0 cluster:

     - name: deploy MongoDB Cluster
       hosts: MongoDB_Cluster
       #remote_user: <your username>
       become: yes
       vars_files:
       - secret.yml
       vars:
         mongodb_engine: "wiredTiger"
         mongodb_cluster: "true"
         mongodb_bind_ip: 0.0.0.0
         mongodb_cluster_name: "MonNomdeCluster"

       roles:
         - ../roles/ansible-mongodb

 Playbook example to install a v3.2 cluster:

      - name: deploy MongoDB Cluster
        hosts: MongoDB_Cluster
        #remote_user: <your username>
        become: yes
        vars_files:
        - secret.yml
        vars:
          mongodb_major_version: "3.2"
          mongodb_minor_version: "3.2.0"
          mongodb_engine: "wiredTiger"
          mongodb_cluster: "true"
          mongodb_bind_ip: 0.0.0.0
          mongodb_cluster_name: "MonNomdeCluster"

        roles:
          - ../roles/ansible-mongodb

Playbook launch
---------------

      ANSIBLE_HOST_KEY_CHECKING=False; ansible-playbook -i hosts playbook.yml --ask-vault-pass --ask-pass --ask-become-pass --tags installation

Playbook launch for client installation only with version 3.0.6
---------------------------------------------------------------

      ANSIBLE_HOST_KEY_CHECKING=False; ansible-playbook -i hosts playbook.yml --ask-vault-pass --ask-pass --ask-become-pass --extra-vars="mongodb_minor_version=3.0.6"--tags installation-client

Tags
----

- [installation-client] : MongoDB client installation
- [installation] : MongoDB server or cluster installation
- [rollback] : go back to a previous MongoDB version
- [testing] : Unit testing for a server

License
-------

BSD

Author Information
------------------

BSO ISL
