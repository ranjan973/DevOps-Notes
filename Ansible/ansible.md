### Cofiguration Management with Ansible.

## Problem Statement: 
In early days, sysadmin used to manage the infrastructure/server configs, patching, software installations, upgrades manually or with shell scripting. THis was fine if the number of servers were less but became a combursome task with the adoption of cloud and micro-service architecture where the number of servers grew 10X.

The manual config management became slow, non-repeatable and error prone. The scripts were hard to maintain. Congif management tools like Ansible, Cheff, Puppet solved this issue by automating infrastructure management by defining system configurations as code.

## Why Ansible amongst other config Management Tools?

- Puppet is a pull mechanism model whereas Ansible works on pull mechanism.
	  In Push mechanism, the control server sends (pushes) configuration to target machines or managed nodes using Anisble playbook. In pull mechanism, the client (agents) regularly checks (pulls) configuration from the server.
	
- Ansible is agentless whereas other tools like Puppet is agent based and needs agents to be installed on control nodes and clients. 
	Ansible connects to Linux servers using SSH protocol with paswordless authentication. The client names/IPs are stored in the inventory.ini file.
	Scalig up and down becomes easier since it is agentless. Just need to add remove node names from teh inventory file. Dynamic inventory concept in Ansible can auto detect new servers to be managed.
	Connects to Windows using WinRM protocol.

- Ansible supports both windows and Linux distros.

- Ansible is easy to lear using global language i.e YAML manifests whereas Puppet is using its own Puppet language.

- Ansible is written in Python and we can write our own Ansible modules. Modules can be shared using Ansible Galaxy.

## Issues with Ansible

- Support of Windows OS is not seamless and has scope of improvement. Linux support is better.
- Debugging is not so good.
- Performance of Ansible degrades when dealing of huge numbe of servers or large parallel executions.

## Passwordless Authentication

To set up passwordless SSH key authentication, we need to generate the public/private key pair using ssh-keygen and then copy the public key contents under ~/.ssh/id_rsa.pub to the ~/.ssh/authorized_keys file on the other server to which we want to login using passwordless auth.

`ssh-copy-id` command automatically copies the client's public key to the server's authorized_keys.

--> Client's public key is copied over to server's ~/.ssh/authorized_keys
--> Client says ssh@server_IP
-->Server has a way to verify that the client possesses the corresponding private key by sending a challenge to teh client and the client saigns that challenge using its private key. The server verifies it using the client's public key.

## Ansible Adhoc Commands
- To perform simple tasks we don't need to write playbooks and can be achieved by using ansible adhoc commands. It's similar to shell commands that we run like ls or cd etc. We write playbooks to perform series of actions just like we do with shell scripts.

EX:1 `ansible -i inventory_file 172.31.22.25 -m "shell" -a "touch devops"`
 -m = ansible Module & -a = Argument. This command creates a file name "devops" on teh target server. The server IP or name must be listed in teh inventory file.
 
 EX:2 - `ansible -i inventory 172.31.22.25 -m copy -a "src=/etc/hosts dest=/tmp/hosts"`
 
 We can group servers in Ansible inventory file using syntax [Server_Type]. Ex below
 
 [Webservers]
 172.31.82.84
 172.31.81.100
 
 `ansible -i inventory_file Webservers -m "shell" -a "touch devops"` --> This will create devops folder in the homedirectory on both the servers under Webservers group.
 
 The default ansible inventory file location is /etc/ansible/hosts. If we put this file under a custom dir, we need to specify it in the adhoc command using the switch -i
 
 ## What is Ansible role
 
 Ansible role is a way to organize complex playbooks into reusable structured components for better readability and efficiency. So instead of writing everything in one playbook. Ansible role seggregates them into smaller pieces like Tasks, Variables,Files, Templates,handlers which are all grouped under a single unit/folder.
 
 We run command `ansible-galaxy role init kuberntes` . It will create a folder named kubernetes and place other subfolders like Tasks, Variables etc under that we can write parts of playbook under these different units. Teh parent playbook file is written and placed under the parent folder.
