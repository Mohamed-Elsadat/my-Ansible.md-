# Ansible

1- First thing in Ansible is make a Key-Gen in a master and slave machines

```bash
ssh-keygen
```

now you create ssh in your machine with public and private key with path (/home/mohamedelsadat/.ssh/id_rsa)

```bash
ls /home/mohamedelsadat/.ssh/
```

output

```bash
id_rsa  id_rsa.pub
```

the regional method for SSH is 

```bash
ssh -i [private key] [username][Hostname/ip-add]
```

if you does not use private key this mean that you have it   

if you does not write username this mean your machine that you need to connect with it have the same name of you machine 

you do not need to write the IP you can make host name by it

```bash
sudo vim /etc/hosts
```

 file

```bash
192.168.1.8 node01 node01.example.com #take care the IP is change every run a machine
192.168.1.9 node02 node02.example.com #take care the IP is change every run a machine
192.168.1.7 control control.example.com  #take care the IP is change every run a machine

```

if i create 3 node in my account aws one is master and two is slave node and i will :

Now we need to make Hosts to my IP's that i create it 

install Ansible by PiP and apt or yum the best practice is pip

```bash
ansible --version
```

output   ansible 2.10.8

```bash
sudo apt install python3 python3-pip -y
sudo apt remove ansible   #This removes the Ansible package but leaves configuration files behind.
sudo apt purge ansible	#This removes any remaining configuration files associated with Ansible, 
pip3 install ansible
```



------

Best practice to make name off user small to easy use 

in Ubuntu  

```bash
sudo hostname control 	 #mohamedelsadat@control:~$
```

we will try to make user that have the same name of user in two users that we will do in them   	ansible

```bash
sudo useradd -m ansible  # -m to make ansble in home dir
sudo passwd ansible
```

and i will great public and private key  

```bash
ssh-keygen
```

i will use public to copy it to 2 user ansible in node 1 and node 2



when i create ansible user in master node make it shell terminal and i need to make it Bash 

```bash
 sudo chsh -s /bin/bash ansible
```

Now if i make any install in machine there are permission denied we need to give it all permission 

we can not make any changes in    /etc/sudoers      we can make file to give it permission in    /etc/sudoers.d/ansible

```bash
sudo vim /etc/sudoers.d/ansible
ansible ALL=(ALL) NOPASSWD: ALL
```

------

i need to connect to node by  (ssh node01) 	not  	ssh -i [private key] [username] [hostname/Ip-add]

we need to create file config

```bash
sudo vim /home/mohamedelsadat/.ssh/config
```

this file will contain

```bash
HOST control
  HostName 192.168.1.7
  User ubuntu
  StrictHostKeyChecking no

HOST node1
  HostName 192.168.1.4
  User vagrant
  StrictHostKeyChecking no

 HOST node2
  HostName 192.168.1.2
  User vagrant
  StrictHostKeyChecking no

if there are file pem from AWS you can added it with 
IdentityFile /home/mohamedelsadat/mohame.pem
```

in a two Nodes we need to make short name with hostname 

```bash
sudo hostname node1
sudo hostname node2
```

and add user ansible for them and make password and change mode form sh to bash

when you make useradd ansible in Ubuntu there are not home directory that we will need it 

thus, do not use "sudo adduser ansible" 

we used here dedicated user ansible and this is not will be available in all time but will make process more easy 

```bash
sudo adduser -m ansible 				#pwd /home/ansible
sudo passwd ansible #Ubuntu
echo pass | sudo passwd --stdin ansible   #passwd for fedora
sudo chsh -s /bin/bash ansible
```

logout from ansible and add this code to node1 and 2 to add all permission to this users to i can make administration tasks 

```bash
logout
    sudo vim /etc/sudoers.d/ansible
ansible ALL=(ALL) NOPASSWD: ALL
```

  OK already we have a public Key in ansible master node that we need to copy it to node 1 and node 2 

we can use usb flash and scp command But it is not Best practice we need to use "ssh-copy-id" command to copy just Public Key

when i need copy system need to know user name and ip and name of public key 

but i make user have the same name in master and slaves node .thus, we do not need to add user it will know

and i do not need to add the name of public key because it is formal and i did not change it and need it like it's

and i do not need to add IP or Host name i make a else name of machine in file "/etc/hosts" name node01 and node02

```bash
ssh-copy-id node01 
```

There are permission denied because " gssapi " 

there are service we use it to ssh named "sshd" we will change it is configuration 

```bash
sudo vim /etc/ssh/sshd_config
```

we will search about "PasswdAuthentication" make it  "Yes" and if commanded remove cammand

and we will search about "GSSAPI" make it  as a command

after you do that in master user not ansible reload the service sshd

OUTPUT

```bash
ansible@Ubuntu:~$ ssh-copy-id node01
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/ansible/.ssh/id_rsa.pub"
The authenticity of host 'node01 (192.168.1.5)' can't be established.
ED25519 key fingerprint is SHA256:qJaCfSwxl7frVIQhEo5LFpXxPlOVn21aMNd2Nwk2ibk.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
ansible@node01's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'node01'"
and check to make sure that only the key(s) you wanted were added.

#####################################
ansible@node1:~/.ssh$ ls
authorized_keys
```

no you can ssh in you ansible user that be in control node 

```bash
 sudo vim /etc/hosts
```

after edit my ip 

```cmd
192.168.1.2 node01 node01.example.com
192.168.1.5 node02 node02.example.com
192.168.1.7 control control.example.com

```

there are file in every .ssh file after there are change in ip there are change in saved machine in ssh and will show message if you need to ssh to it tell you there are modifiy in ip and need for you make a new "KNOW FILE" in ssh to add the machine again

```bash
ansible@Ubuntu:~/iti$ ssh node01
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:ZIh8zOLKNwQqllmdd/4mvG6M/S+LdI1RgtkHEaYPHZA.
Please contact your system administrator.
Add correct host key in /home/ansible/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in /home/ansible/.ssh/known_hosts:2
  remove with:
  ssh-keygen -f "/home/ansible/.ssh/known_hosts" -R "node01"
Host key for node01 has changed and you have requested strict checking.
Host key verification failed.

```



```bash
ssh-keygen -f "/home/ansible/.ssh/known_hosts" -R "node01"
```

this make the old known_hosts  known_hosts.old and make new one to take yes permission again and just need passwd

```bash
ansible@Ubuntu:~$ ssh node01
ansible@node01's password:
Welcome to Ubuntu 14.04.6 LTS (GNU/Linux 3.13.0-170-generic x86_64)

ansible@Ubuntu:~$ ssh node02
ansible@node02's password:
Welcome to Ubuntu 14.04.6 LTS (GNU/Linux 3.13.0-170-generic x86_64)

```

```bash
ssh node01 -t bash
```



```bash
ansible@Ubuntu:~/iti$ ssh node01 -t bash
ansible@node01's password:
ansible@node:~$
```

SSH is DONE

------

invantory file

```ini
ungrouped.example.com

[nodes]
node01.example.com
node02.example.com
intersection.example.com
node[03:10]

[dbservers]
dbserver.example.com
intersection.example.com
dbserver[a:d].example.com

```

if you to check the hosts 

```bash
ansible@Ubuntu:~/iti$ ansible -i inventory nodes --list-hosts
  hosts (2):
    node01.example.com
    node02.example.com
    
ansible@Ubuntu:~/iti$ ansible -i inventory dbservers --list-hosts
  hosts (1):
    dbserver.example.com

ansible@Ubuntu:~/iti$ ansible -i inventory all --list-hosts
  hosts (3):

    node01.example.com
    node02.example.com
    dbserver.example.com

ansible@Ubuntu:~/iti$ ansible -i inventory ungrouped --list-hosts
  hosts (1):
    ungrouped.example.com

ansible@Ubuntu:~/iti$ ansible -i inventory 'all:!nodes' --list-hosts
  hosts (2):
    ungrouped.example.com
    dbserver.example.com
   
ansible@Ubuntu:~/iti$ ansible -i inventory 'nodes,dbservers' --list-hosts
  hosts (3):
    node01.example.com
    node02.example.com
    dbserver.example.com

```

inventory file with intersection 

inventory file support Rang with number and alpha

```ini
ungrouped.example.com

[nodes]
node01.example.com
node02.example.com
intersection.example.com
node[03:10]

[dbservers]
dbserver.example.com
intersection.example.com
dbserver[a:d].example.com
```

```bash
ansible@Ubuntu:~/iti$ ansible -i inventory 'nodes:&dbservers' --list-hosts
  hosts (1):
    intersection.example.com

ansible@Ubuntu:~/iti$ ansible -i inventory 'nodes' --list-hosts
  hosts (11):
    node01.example.com
    node02.example.com
    intersection.example.com
    node03
    node04
    node05
    node06
    node07
    node08
    node09
    node10


ansible@Ubuntu:~/iti$ ansible -i inventory 'dbservers' --list-hosts
  hosts (6):
    dbserver.example.com
    intersection.example.com
    dbservera.example.com
    dbserverb.example.com
    dbserverc.example.com
    dbserverd.example.com

```

there are file config for ansible default be in /etc/ansible/ansible.cfg this file have all permissition that ansible need it

```bash
in this order system serch about this file 

you dir home/ansible/iti/ansible.cfg
/home/ansible/.ansible.cfg
/etc/ansible/ansible.cfg
```

you can know it by ansible --version

```bash
ansible@Ubuntu:~/iti$ ansible --version
ansible 2.10.8
  config file = /home/ansible/iti/ansible.cfg
```

in this file we will write 

```ini
[defaults]
inventory = inventory
remote_user = ansible
host_key_checking = no

[privilege_escalation]
become = yes
become_user = root
become_method = sudo
become_ask_pass = no
```

AD-HOC command is a command that use for a sample model that you can run it without playbook file

```bash
 ansible -i inventory nodes -m ping
```

```bash
[nodes]
172.20.10.4 ansible_user=vagrant ansible_password=123
172.20.10.5 ansible_user=vagrant ansible_password=123
```

there are error in my machine because permission and i install this service

```bash
ansible@Ubuntu:~/iti$ ansible -i inventory nodes -m ping
172.20.10.4 | FAILED! => {
    "msg": "to use the 'ssh' connection type with passwords, you must install the sshpass program                               "
}
172.20.10.5 | FAILED! => {
    "msg": "to use the 'ssh' connection type with passwords, you must install the sshpass program                               "
}
ansible@Ubuntu:~/iti$ sudo apt-get install sshpass
```



```bash
ansible@Ubuntu:~/iti$ ansible -i inventory nodes -m ping

172.20.10.5 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
172.20.10.4 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}

```

this code use to send ping to your host or ip in this nodes group in inventory and return "PONG" but i do not have internet now HHH

```bash
ansible@Ubuntu:~/iti$ ansible nodes -m debug -a 'msg="Hello from ITI"'
192.168.1.2 | SUCCESS => {
    "msg": "Hello from ITI"
}
192.168.1.5 | SUCCESS => {
    "msg": "Hello from ITI"
}
```

if you write this code again i do not need to know it -i inventory i write it in ansible.cfg as a default  " inventory = inventory" and this code used to print message  in screen and check connection also

```bash
ansible@Ubuntu:~/iti$ ansible nodes -m apt -a "name=apache2 state=present"
172.20.10.5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "cache_update_time": 1721131070,
    "cache_updated": false,
    "changed": true,
```

this code use a apt install httpd and you can specific you node if there are not ubuntu 

```bash
ansible@Ubuntu:~/iti$ ansible node01.example.com -m yum -a "name=httpd state=present"
```

in RHCL you need to enable service after you install it

```bash
ansible@Ubuntu:~/iti$ ansible node01.example.com -m service -a "name=httpd state=started"
```

------

Playbook

```yaml
---
- name: Ansible debug module example
  hosts: nodes
  tasks:
    - name: print dummy message
      debug:
        msg: hello world
```

if you need to check syntax of you yml file use this command if true will return the name of yml file 

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml --syntax-check
playbook: site.yml
ansible@Ubuntu:~/iti$ ansible-playbook site.yml 
```

```cmd
PLAY [Ansible debug module example] ********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [172.20.10.4]
ok: [172.20.10.5]

TASK [print dummy message] *****************************************************************************************************
ok: [172.20.10.4] => {
    "msg": "hello world"
}
ok: [172.20.10.5] => {
    "msg": "hello world"
}

PLAY RECAP *********************************************************************************************************************
172.20.10.4                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
172.20.10.5                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


```

```yaml
---
- name: Ansible debug module example
  hosts: nodes
  tasks:
    - name: print dummy message
      command: echo Hello World
      #    debug:
      # msg: hello world
```

output

```cmd
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] ********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [172.20.10.5]
ok: [172.20.10.4]

TASK [print dummy message] *****************************************************************************************************
changed: [172.20.10.5]
changed: [172.20.10.4]

PLAY RECAP *********************************************************************************************************************
172.20.10.4                : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
172.20.10.5                : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

variable in playbook yml file

first varible in inventory

1-Host var

```ini
#invantory file
[nodes]
172.20.10.4 ansible_user=vagrant ansible_password=123 scope=host_inventory_scope
172.20.10.5 ansible_user=vagrant ansible_password=123 scope=host_inventory_scope
```

```yaml
#playbook file
---
- name: Ansible debug module example
  hosts: nodes
  tasks:
    - name: print dummy message
      debug:
        var: scope

```



```cmd
#RUN
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] *************************************************************

TASK [Gathering Facts] **************************************************************************
ok: [172.20.10.5]
ok: [172.20.10.4]

TASK [print dummy message] **********************************************************************
ok: [172.20.10.4] => {
    "scope": "host_inventory_scope"
}
ok: [172.20.10.5] => {
    "scope": "host_inventory_scope"
}

PLAY RECAP **************************************************************************************
172.20.10.4                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
172.20.10.5                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

2-Group var

if we change the var to group the the system will look to host first not group

```ini
#invantory file
[nodes]
192.168.1.3 ansible_user=vagrant ansible_password=123 scope=host_inventory_scope

[nodes:vars]
scope=group_inventory_scope

```

still will see the 'host_inventory_scope' 

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] ********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [192.168.1.3]

TASK [print dummy message] *****************************************************************************************************
ok: [192.168.1.3] => {
    "scope": "host_inventory_scope"
}

PLAY RECAP *********************************************************************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

but if remove   'host_inventory_scope' 

```ini
#invantory file
[nodes]
192.168.1.3 ansible_user=vagrant ansible_password=123 

[nodes:vars]
scope=group_inventory_scope
```

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] ********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [192.168.1.3]

TASK [print dummy message] *****************************************************************************************************
ok: [192.168.1.3] => {
    "scope": "group_inventory_scope"
}

PLAY RECAP *********************************************************************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

Best practice do not use host and group var in invantory file

if you need to make dedicated process in you host Ip or group Ip

you can make file named is host_vars and group_vars

put in it [ group + .yml ]

put in it [ IP + .yml ]

```bash
mkdir group_vars
cd group_vars/
touch nodes.yml

---
scope: group_vars_scope
```

```ini
---
- name: Ansible debug module example
  hosts: nodes
  tasks:
    - name: print dummy message
      debug:
        var: scope
```

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] *****************************************************

TASK [Gathering Facts] ******************************************************************
ok: [192.168.1.3]

TASK [print dummy message] **************************************************************
ok: [192.168.1.3] => {
    "scope": "group_vars_scope"
}

PLAY RECAP ******************************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

<img src="C:\Users\moham\AppData\Roaming\Typora\typora-user-images\image-20240723190832826.png" style="zoom:150%;" />

```bash
mkdir host_vars
cd host_vars/
touch 192.168.1.3.yml

---
scope: host_vars_scope
```

```ini
# playbook file site.yml

---
- name: Ansible debug module example
  hosts: nodes
  tasks:
    - name: print dummy message
      debug:
        var: scope
```

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] *****************************************************************

TASK [Gathering Facts] ******************************************************************************
ok: [192.168.1.3]

TASK [print dummy message] **************************************************************************
ok: [192.168.1.3] => {
    "scope": "host_vars_scope"
}

PLAY RECAP ******************************************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

<img src="C:\Users\moham\AppData\Roaming\Typora\typora-user-images\image-20240723184242090.png" alt="image-20240723184242090" style="zoom:150%;" />

there are command use to till you the scope of host or group for the IP

```bash
ansible-inventory --list | jq
```

```json
{
    "_meta": {
        "hostvars": {
            "192.168.1.3": {
                "ansible_password": 123,
                "ansible_user": "vagrant",
                "scope": "group_vars_scope"
            }
        }
    },
    "all": {
        "children": [
            "nodes",
            "ungrouped"
        ]
    },
    "nodes": {
        "hosts": [
            "192.168.1.3"
        ]
    }
}

```

var in playbook

```ini
#invantory file
[nodes]
192.168.1.3 ansible_user=vagrant ansible_password=123 scope=host_inventory_scope

[nodes:vars]
scope=group_inventory_scope
```

```ini
#playbook file
---
- name: Ansible debug module example
  hosts: nodes
  vars:
    - scope: play_scope
  tasks:
    - name: print dummy message
      debug:
        var: scope

```

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] ********************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.1.3]

TASK [print dummy message] *****************************************************
ok: [192.168.1.3] => {
    "scope": "play_scope"
}

PLAY RECAP *********************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

global scope

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml -e "scope=global-scope"

PLAY [Ansible debug module example] ********************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.1.3]

TASK [print dummy message] *****************************************************
ok: [192.168.1.3] => {
    "scope": "global-scope"
}

PLAY RECAP *********************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

the order of var presider

1. global scope
2. play scope 
3. host scope 
4. group scope 

Best practice make file and put in it var.yml

make direct vars have var.yml

<img src="C:\Users\moham\AppData\Roaming\Typora\typora-user-images\image-20240723183643383.png" alt="image-20240723183643383" style="zoom:150%;" />

```bash
mkdir vars
```

```
touch vars.yml
vim vars.yml
```

```ini
---
scope: var_file_scope
```

edit site.yml file

```ini
---
- name: Ansible debug module example
  hosts: nodes
  vars_files:
    - vars/vars.yml
  tasks:
    - name: print dummy message
      debug:
        var: scope
```

```bash
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] ****************************************************

TASK [Gathering Facts] *****************************************************************
ok: [192.168.1.3]

TASK [print dummy message] *************************************************************
ok: [192.168.1.3] => {
    "scope": "var_file_scope"
}

PLAY RECAP *****************************************************************************
192.168.1.3                : ok=2    changed=0    unreachable=0    failed=0    skipped=0                 rescued=0    ignored=0

```

```ini
---
- name: Ansible debug module example
  hosts: nodes
  vars:
    user: mohamedelsadat
  tasks:
    - name: Copy file.j2 to remote node
      ansible.builtin.template:
        src: file.j2
        dest: /tmp/file.j2
        mode: 0644
        owner: ansible
        group: ansible

    - name: cat my_file
      ansible.builtin.command: cat /tmp/file.j2
      register: cmd_output
      changed_when: false

    - name: show cmd_output
      ansible.builtin.debug:
        var: cmd_output.stdout
```

```sh
ansible@Ubuntu:~/iti$ ansible-playbook site.yml

PLAY [Ansible debug module example] ********************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.1.10]

TASK [Copy file.j2 to remote node] *********************************************
changed: [192.168.1.10]

TASK [cat my_file] *************************************************************
ok: [192.168.1.10]

TASK [show cmd_output] *********************************************************
ok: [192.168.1.10] => {
    "cmd_output.stdout": "Hello mohamedelsadat from ITI"
}

PLAY RECAP *********************************************************************
192.168.1.10               : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Loop in playbook

```ini
---
- name: playbook to add a list of users
  hosts: nodes
  vars:
    users_lists:
      - name: alaa
        uid: 4000
      - name: ali
        uid: 4001
      - name: ahmed
        uid: 4002
      - name: mohamed
        uid: 4003
  tasks:
    - name: add users
      user:
        name: "{{ item.name }}"
        uid: "{{ item.uid }}"
      loop: "{{ users_lists }}"
```

```sh
ansible@Ubuntu:~/iti$ ansible-playbook loopuser.yml

PLAY [playbook to add a list of users] *****************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.1.10]

TASK [add users] ***************************************************************
changed: [192.168.1.10] => (item={'name': 'alaa', 'uid': 4000})
changed: [192.168.1.10] => (item={'name': 'ali', 'uid': 4001})
changed: [192.168.1.10] => (item={'name': 'ahmed', 'uid': 4002})
changed: [192.168.1.10] => (item={'name': 'mohamed', 'uid': 4003})

PLAY RECAP *********************************************************************
192.168.1.10               : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

#if i Run code again there are no change and aready users existing 

ansible@Ubuntu:~/iti$ ansible-playbook loopuser.yml

PLAY [playbook to add a list of users] *****************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.1.10]

TASK [add users] ***************************************************************
ok: [192.168.1.10] => (item={'name': 'alaa', 'uid': 4000})
ok: [192.168.1.10] => (item={'name': 'ali', 'uid': 4001})
ok: [192.168.1.10] => (item={'name': 'ahmed', 'uid': 4002})
ok: [192.168.1.10] => (item={'name': 'mohamed', 'uid': 4003})

PLAY RECAP *********************************************************************
192.168.1.10               : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

if you need to remove users

```ini
tasks:
    - name: add users
      user:
        name: "{{ item.name }}"
        uid: "{{ item.uid }}"
        state: absent
        remove: yes
      loop: "{{ users_lists }}"
```

