# GateHub-sysops-candidates-task

## [part 1]

These are comprehensive steps for preparing the Fedora Linux server environment to execute the necessary Ansible playbook for our upcoming tasks. The instructions will ensure that the server is fully equipped and configured for efficient and effective Ansible playbook execution.

### Enable sshd service:
sudo systemctl enable sshd

### Edit Configuration file and remove "#" from #Port 22 If you would like to change sshd port In my case I use port "2244":
nano /etc/ssh/sshd_config

### Add port in firewall:
sudo firewall-cmd --add-port=2244/tcp --permanent

### Restart the sshd service:
sudo service sshd restart

### Add two new users: superuser and user:
adduser superuser
adduser user

### Change users passwords:
passwd superuser
passwd user

### Add user "superuser" in sudoers file
sudo usermod -aG wheel superuser

### Install Ansible:
sudo dnf install -y ansible

Output:

Installed:
  ansible-7.1.0-1.fc37.noarch          ansible-core-2.14.1-1.fc37.noarch
  python3-jinja2-3.0.3-5.fc37.noarch   python3-resolvelib-0.5.5-6.fc37.noarch

_____________________________________________________________________________________________________________________________________________________

## [part 2]

### [1] 	Create Key-Pair by each user, so login with a common user on SSH Server Host and work like follows.

# create key-pair

[superuser@developer ~]$ ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/home/superuser/.ssh/id_rsa): # Enter or input changes if you want
Created directory '/home/superuser/.ssh'.
Enter passphrase (empty for no passphrase): # set passphrase (if set no passphrase, Enter with empty)
Enter same passphrase again:
Your identification has been saved in /home/superuser/.ssh/id_rsa.
Your public key has been saved in /home/superuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:N64p0kXAaqN/dFCdNz7IDnqm0vOTSDihz+yr2kXjyKQ fedora@192.168.1.147
The key's randomart image is:
.....
.....

[superuser@developer ~]$ ll ~/.ssh

total 8
-rw-------. 1 superuser superuser 2655 Jan 30 18:38 id_rsa
-rw-r--r--. 1 superuser superuser  574 Jan 30 18:38 id_rsa.pub

[superuser@developer ~]$ mv ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys 


### [2] 	Transfer the private key created on the Server to a Client, then it's possbile to login with Key-Pair authentication.

[superuser@target ~]$ mkdir ~/.ssh

[superuser@target ~]$ chmod 700 ~/.ssh
### transfer the private key to the local ssh directory

[superuser@target ~]$ scp superuser@192.168.1.148:/home/superuser/.ssh/id_rsa ~/.ssh/

superuser@192.168.1.148's password:
id_rsa                                        100% 1876   193.2KB/s   00:00

[superuser@target ~]$ ssh superuser@192.168.1.147

Enter passphrase for key '/home/superuser/.ssh/id_rsa':   # passphrase if you set
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Mon Jan 30 18:38:51 2023

[superuser@developer ~]$   # logined

# [3] If you set [PasswordAuthentication no], it's more secure.

[root@developer ~]# vi /etc/ssh/sshd_config
### line 73: change to [no]

PasswordAuthentication no
[root@developer ~]# systemctl restart sshd 

_____________________________________________________________________________________________________________________________________________________
## [part 3]

### This part is about Ansible hosts file configuration data. Add hosts and ip's of developer and target machines into the hosts file in "/etc/ansible/hosts"
In my case my "developer" machine has 192.168.1.147 local ip addres and my "target" machine 192.168.1.148
    
    [target]
    192.168.1.148
    [target:vars]
    ansible_connection=ssh
    ansible_user=superuser
    [developer]
    192.168.1.147
    
    [developer:vars]
    ansible_connection=ssh
    ansible_user=superuser

## [part 4]

This is an Ansible playbook that is used to configure and install various software packages on Fedora Linux systems.
The playbook consists of two plays, one for the "target" hosts and one for the "developer" hosts.

### For the "target" hosts, the playbook performs the following tasks:

    Update all packages to the latest version.
    Add the Slack repository to the system's yum repository list.
    Create the Slack repository file and add its configuration details.
    Install the Slack package.
    Add the Google Chrome repository to the system's yum repository list.
    Add the MySQL Community repository to the system's yum repository list.
    Add the Visual Studio Code Microsoft repository to the system's yum repository list.
    Install the Gnome-tweaks package.
    Install the Htop package.
    Install the KeepassXC package.
    Install the Zsh package.

### For the "developer" hosts, the playbook performs the following tasks:

    Install the Zsh package.
    Install the Development Tools package group.
    Install the C Development Tools and Libraries package group.
    Install the Ansible package.
    Install the Visual Studio Code package.
    Install the Fd-find package.
    Install the Fzf package.
    Install the Fish package.
    Install the Vim package.
    
### For docker role the playbook performs the following tasks:

    Install the docker
    Install the docker-compose-plugin
    Install the docker-compose
    Enable docker at startup
    Add all users to docker group
    
### For Ansible role for configuring SSH server the playbook performs the following tasks:

    Disable password logins
    Disable root login
    Disallow forwarding
    Install fail2ban and punish offensive ssh clients
    
### CA & internal self signed certificates:
    Create certificate authority
    Issue a wildcard certificate with newly created CA
    ??? Use sysops.jobs domain for example
    Create ansible role for trusting all certificates issued by our CA
    
### Ansible role for installing nix - https://nixos.org/ (developers only)
    Install as plain user
    
## Bonus task    
    
### Prepare docker deployment of ghost blog:
    Install Ghost blog
    Install MariaDB backend
### Expose previous deployment with any reverse proxy (bonus task):
    Use sysops.jobs and www.sysops.jobs domain
    Expose with SSL certificates created in previous task

## [part 4]
    
    ### This is my playbook for Ansible: - https://github.com/mcsaban/GateHub-sysops-candidates-task/blob/main/ 
    
### to run this playbook with Ansible run this command:
    
    ansible-playbook tasks.yml --ask-become-pass
      
_________________________________________________________________________

used websites for help
- https://docs.ansible.com/
- https://github.com/
- https://git.drinkingtea.net/
- https://code.visualstudio.com/docs/setup/linux
- https://docs.docker.com/engine/install/fedora/
- https://stackoverflow.com/
- https://www.clockworknet.com/blog/2020/03/10/managing-firewalld-with-ansible/
- https://codebeautify.org/yaml-validator
    
    
