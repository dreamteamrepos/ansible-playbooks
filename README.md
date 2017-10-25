ansible-playbooks
=================

Installs all the roles needed for use with the ansible-playbooks repository

Requirements
------------

1. Ansible 2.4

Dependencies
------------

none

Example Playbook
----------------

* A use example:
*	ansible-galaxy install -r requirements.yml -p roles/
*	ansible-playbook -i ansible-inventory/prod/artifactory ansible-playbooks/base.yml -u root

Tested OSes
------------------
7. RHEL7/CentOS7 ( Working )

License
-------

MIT

Author Information
------------------

Development Range Engineering, Architecture, and Modernization (DREAM) Team
