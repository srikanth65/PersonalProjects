**Install Ansible on RHEL9**

Launch Two instances: 1 Contoller and 1 Managed Node

Do the following on Contoroller Node and Managed node: 

useradd ansibleuser

passwd ansibleuser

usermod -aG wheel ansibleuser

vi /etc/ssh/sshd_config 
Password_Authentiation Yes
# Password_Authentication No

systemctl restart sshd


**Install Ansible**

Check the os-release of your system by running below command

# cat /etc/os-release

Open your terminal and run the below command to install Ansible.

# dnf install ansible-core

Once Ansible is installed check the version of Ansible by running the below command

# ansible --version


**To test ansible installation, we will be using one remote linux system apart from ansible control node (RHEL 9)**

Ansible Control Node – RHEL 9 – 192.168.1.213

Ansible Managed Node – RHEL 9  – 192.168.1.156

Generate SSH keys for the user (in my case it is ansibleuser) and share ssh key to managed node.

$ ssh-keygen

Share the ssh keys with managed node using following ssh-copy-id command,

$ ssh-copy-id linuxtechi@192.168.1.167

On the managed node, create the following file

$ echo "ansibleuser ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansibleuser

**Head back to Ansible control node and create ansible.cfg file with beneath content**

$ mkdir ~/automation && cd ~/automation/

$ vi ansible.cfg

[defaults]

inventory = ./inventory

host_key_checking = false

remote_user = ansibleuser

ask_pass = False

[privilege_escalation]

become=true

become_method=sudo

become_user=root

become_ask_pass=False

save & exit the file.

**Create the inventory file with following content**

$ vi ~/automation/inventory

[dev]

192.168.1.156

Save & close the file

**Run following ansible command to perform the ping pong connectivity from control node to managed node**

$ cd ~/automation/

$ ansible all -m ping

**ansible playbook to install nginx on managed node**

$ vi nginx-deploy.yaml
---
- name: Playbook to Install and Start Nginx

  hosts: dev

  tasks:

  - name: Install nginx
  
    package:

      name: nginx

      state: present

  - name: Start nginx Service

    service:

      name: nginx

      state: started

Save and exit the file.

**Run the above created playbook using following command**

$ ansible-playbook nginx-deploy.yaml

ansible ad-hoc commands,

$ ansible dev -i inventory -m shell -a 'apt list --installed|grep nginx'

$ ansible dev -i inventory -m shell -a 'systemctl status nginx'













