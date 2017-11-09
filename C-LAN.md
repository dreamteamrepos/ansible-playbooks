C-LAN
=====

The clan.yml playbook is only needed for the commercial lan server. It's purpose install the following services:

* NFS
* libvirt

It will also ensure that a graphical environment is running on the server to make it easy to administer the system.

Requirements
------------

The geerlingguy.nfs role needs to be available.

Installing the nfs role
----------------

```bash
ansible-galaxy install -r geerlingguy.nfs -p roles/
```

Post deploy steps
-----------------

* Copy the reposync.qcow2 file to `/var/lib/libvirt/images` on the host server.
* Run the following command to import the vm

```bash
cd /var/lib/libvirt/images
virt-install --name reposync \
--memory 2048 --vcpus 2\
--disk /var/lib/libvirt/images/reposync.qcow2 \
--noautoconsole --vnc \
--import
```

Tested OSes
------------------
* RHEL7/CentOS7 ( Working )
