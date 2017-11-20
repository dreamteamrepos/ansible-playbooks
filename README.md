# Ansible Playbooks

Purpose
----

This repository stores playbooks and variables associated with DREAM Team roles and inventories.


Structure
---------

The structure of the repository is similar to the following:

```
.
├── docs
│   └── artifacts.md
├── group_vars
│   ├── artifacts
│   ├── badger
│   ├── cephadmin
│   └── director
├── host_vars
│   ├── artifacts-01.prod.badger.net
│   ├── artifacts-01.prod.clan.net
│   ├── cephadmin-01.prod.badger.net
│   └── director-01.prod.badger.net
├── inventory -> ../inventory
├── roles -> ../roles
├── vault
│   └── certs
│       └── BADGER.NET.yml
├── artifacts.yml
├── cephadmin.yml
├── common.yml
├── director.yml
├── postgres.yml
└── README.md

```

The playbooks are stored in a separate repository from inventories and roles, to reduce merge conflicts; however, you will notice that there are symlinks in place to connect the repository to it's sister repos. Since playbooks must be in the same directory as the `roles/` directory, to use any roles inside of it, that has been symlinked in.

You'll notice inside of some of these playbooks, that the `common.yml` playbook is called via `import_playbook:` in order to create a chain. For example:

```
---
- import_playbook: common.yml

- name: Ensuring the configuration of the director host(s)
  hosts: director
  become: true
  become_method: sudo
  become_user: root
  gather_facts: true

  roles:
    - director
...

```

The hosts inside of inventory files are separated by network, environment tier, and role. This method creates some safety by ensuring that a single play can not be run across multiple projects, networks, and/or environment tiers. However, you will notice that each playbook also has it's associated group name hard-coded into the playbook. In order to prevent the `common.yml` from taking action on nodes that it should not, tasks common/not-common to only a single network are marked with a conditional `when:` statement. This elimates the need for more than two levels of nested playbooks:

```
---
- name: Executing tasks common to all DREAM infrastructure hosts
  hosts: all
  become: true
  become_method: sudo
  become_user: root
  gather_facts: true

  roles:
    - host_rename
    - repositories
    - global_base
    - bash
    - motd

  tasks:
  - block:
      - name: Including the vars_file containing the {{ realm }} CA certificate
        include_vars:
          file: vault/certs/{{ realm }}.yml
      - name: Including the authentication role
        include_role:
          name: authentication
      - name: Including the authorization role
        include_role:
          name: authorization
    when: net_name != 'clan'
...

```

Calling a Playbook
------------------
You can call a playbook like this:

```
ansible-playbook -i inventory/badger/prod/director playbooks/director.yml
```

Documentation
-------------
Playbook documentation is stored in the `docs/` directory.

