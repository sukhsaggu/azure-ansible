Using Ansible with Azure
========================

Basic, no frills, single file playbooks to provision, run tasks and terminate a vm.

Ansible control machine is running CentOS 7.6.1810

ansible --version  = 2.9.2

[1]: https://docs.ansible.com/ansible/latest/scenario_guides/guide_azure.html
[2]: https://github.com/jborean93/ansible-win-demos/blob/master/azure-windows/provision.yml

Prerequisites
-------------

* Necessary libraries have been installed on Ansible control machine. [Link][1]
* Select an authentication method. In these cases i'll be using a service principal whose profile details have been stored in the ~/.azure/credentials file. [Link][1]
* Azure SSH Keypair has been created locally. In this case i'm using 'azure-user' and have an associated ssh keypair. (Keypair name is referenced in the playbook). This is used for ssh to the new vm.
* An Azure Resource group has already been setup. The name of which is referenced in the playbooks
* A Virtual network with an address space has already been setup in the resource group

Notes
-----

* Provisioning a Linux virtual machine in azure will open up public access to port 22. For Windows this will be port 5986/3389
* For Linux the ssh public key is referenced in the playbook and added to the authorized_keys for the new vm. This is because the public key is not stored in azure.
* For windows, WinRM has to be configured. Great resource here on how this was done. [Link][2]
* The play at the end of the playbook will remove any storage accounts, public ip address, network interface, network security group associated with the vm.

Provision a basic VM (Red Hat Enterprise Linux)
-----------------------------------------------

Playbook run-time : ~ 9 mins

This playbook will:

1. Launch an azure rhel virtual machine
2. Connect via ssh to the **external IP** of the new vm
3. Gather facts on the vm
4. Reboot the vm
5. Terminate the vm

* `<azure_profile_name>` = name of the Azure Profile used to create the VM
* `<ansible_ssh_public_key_file>` = path to public key file. e.g ~/.ssh/my_key.pub
* `<ansible_ssh_private_key_file>` = path to private key file. e.g ~/.ssh/my_key
* `<vm_name>` = name of the VM. e.g 'test2019'

>ansible-playbook launch_azure_compute_rhel.yml --extra-vars '{"azure_profile_name":"***<azure_profile_name>***", "vm_name":"***<vm_name>***","ansible_ssh_public_key_file":"***<ansible_ssh_public_key_file>***","ansible_ssh_private_key_file":"***<ansible_ssh_private_key_file>***" }'

Provision a basic VM (Windows Server 2016)
-----------------------------------------------

Playbook run-time : ~ 14 mins

This playbook will:

1. Launch an azure windows 2016 datacenter virtual machine
2. Configures winrm for ansible
3. Connect via winrm to the **external IP** of the new vm
4. Gather facts on the vm
5. Reboot the vm
6. Terminate the vm

* `<azure_profile_name>` = Name of the Azure Profile used to create the VM
* `<vm_name>` = Name of the VM. e.g 'test2019'
* `<vm_admin_username>` = username of the administrator. Same account will be used in ansible_user
* `<vm_admin_password>` = password of the administrator. Also used in ansible_password

>ansible-playbook launch_azure_compute_win.yml --extra-vars '{"azure_profile_name":"***<azure_profile_name>***","vm_name":"***<vm_name>***","vm_admin_username":"***<vm_admin_username>***","vm_admin_password":"***<vm_admin_password>***"}'
