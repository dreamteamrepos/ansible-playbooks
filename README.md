# Ansible Playbooks

Usage
----

This repository stores playbooks and variables associated with DREAM Team roles and inventories.
The structure of the repository is similar to the following:

```
.
├── all.yml
├── group_vars
│   └── all
│       ├── infra
│       │   ├── infra-badger
│       │   │   ├── infra-badger-prod
│       │   │   │   ├── infra-badger-prod-artifacts
│       │   │   │   │   └── main.yml
│       │   │   │   ├── infra-badger-prod-xray
│       │   │   │   │   └── main.yml
│       │   │   │   └── main.yml
│       │   │   ├── infra-badger-stag
│       │   │   │   ├── infra-badger-stag-artifacts
│       │   │   │   │   └── main.yml
│       │   │   │   ├── infra-badger-stag-xray
│       │   │   │   │   └── main.yml
│       │   │   │   └── main.yml
│       │   │   └── main.yml
│       │   ├── infra-clan
│       │   │   ├── infra-clan-prod
│       │   │   │   ├── infra-clan-prod-artifacts
│       │   │   │   │   └── main.yml
│       │   │   │   ├── infra-clan-prod-xray
│       │   │   │   │   └── main.yml
│       │   │   │   └── main.yml
│       │   │   └── main.yml
│       │   └── main.yml
│       └── main.yml
├── host_vars
│   ├── artifacts-01.prod.badger.net
│   └── artifacts-01.prod.clan.net
├── infra-badger-prod-artifactory.yml
├── infra-badger-prod.yml
├── infra-badger.yml
├── infra-clan-prod-artifactory.yml
├── infra-clan-prod.yml
├── infra-clan.yml
├── infra.yml
├── inventory -> ../inventory
├── postgres.yml
├── README.md
├── roles -> ../roles/
└── vault
    ├── certs
    │   └── BADGER.NET.yml
    └── keys

```

The playbooks are stored in a separate repository from inventories and roles, to reduce merge conflicts; however, you will notice that there are symlinks in place to connect the repository to it's sister repos. Since playbooks must be in the same directory as the `roles/` directory, to use any roles inside of it, that has been symlinked in.

You'll notice inside of some of these playbooks, that other playbooks are called via `import_playbook:` in order to create a chain. For example:

```
---
- import_playbook: infra-badger-prod.yml

- name: Set up Artifactory in the infra project, badger network, and prod tier
  hosts: infra-badger-prod-artifactory
  become: true
  become_method: sudo
  become_user: root
  gather_facts: true

  roles:
    - teaming
    - nginx
    - postgresql
    - artifactory
...
```

This method creates some safety by ensuring that a single play can not be run across multiple projects, networks, and/or environment tiers. However, you will notice that each playbook also has it's associated group name hard-coded into the playbook. In order to prevent the `all.yml` from acting on everything in the called inventory file, it is wise to use the `--limit` flag on the command line when you call a playbook. For example:


```
ansible-playbook -i ./inventory/infra-badger-prod/ infra-badger-prod-artifactory.yml --limit "infra-badger-prod-artifactory"
OR
group="infra-badger-prod-artifactory"; ansible-playbook -i ./inventory/${group%-*}/ ${group}.yml --limit "${group}"
```

