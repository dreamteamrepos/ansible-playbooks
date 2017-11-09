C-LAN
=====

The `infra-clan-prod-artifactory.yml` playbook includes some hard-coded tasks that do not live in a role. The playbook's purpose is to install the following services, in addition to what the other artifactory playbooks install:

* NFS
* libvirt

The playbook also ensures that a graphical environment is running on the server to make it easy to administer the system. Lastly, the post-deploy manual steps below should be followed in order to create a VM capable of syncing from the Red Hat CDN. Artifactory does not include the native capability to import Red Hat CDN-hosted content, due to it's inability to pass credentials (like `subscription-manager`). The work-around for this is a RHEL VM hosted on the server that pulls down the repos via `reposync` and then passes them to Artifactory via across a virtual bridge device. The NFS share lives on the host server, and is therefore viewable inside the Artifactory interface as a local-repo. The metadata of the packages is collectable by Artifactory and they are then served via it's web interface.

Requirements
------------

The geerlingguy.nfs role needs to be available to create the NFS share on the VM.
This Ansible Galaxy role has been forked from the Github repo into AFCYBER-DREAM as `ansible-role-nfs`.
It is already available for use if you are using the `dev-ranger` to manage your AFCYBER-DREAM ansible materials.
After running the playbook against the target system, the manual steps below will need to be completed.

Post-deploy manual steps
------------------------

* Copy the reposync.qcow2 file to `/var/lib/libvirt/images` on the host server
* Run the following command to import the VM:

```bash
cd /var/lib/libvirt/images
virt-install --name reposync \
--memory 2048 --vcpus 2\
--disk /var/lib/libvirt/images/reposync.qcow2 \
--noautoconsole --vnc \
--import
```
* If not already completed, pass the activation key to `subscription-manager` and double-check connectivity to RH CDN 

Tested OSes
------------------
* RHEL7/CentOS7 ( Working )
