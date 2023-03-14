## Title
Ansible Training

## What Is Ansible
Ansible is a configuration management tool. It can be used to perform a task (like installing web servers) on multiple servers at a go without the need to manually install them one after the other or individually. It can also be used for infrastructure As Code (IaC). Ansible shines very well when it comes to configuration management. It is `Agentless` this mean you don;t have to install ansible on the machines you won't to install this wenservers on them all you need is to install ansible on one particular machine (the machine to run the ansible command on).

**Note:**
* Playbook is a collection of one or more play or tasks.
* A task or play is the actual code to run on the remote servers.

### How it works.
* Install ansible on master machine i.e the machine to run ansible commands on.
* Generate ssh-key on master machine. `ssh-keygen`
* Copy the public ssh key on the machines to install packages on. `ssh-copy-id host_machine_ip`
* Create ansible.cfg file and place the following code snippert in it.

```
[defaults]
inventory = hosts
remote_user = vm

[privilege_escalation]
become=True
become_user=root
become_method=sudo
become_ask_pass=False
```

1. Where `hosts` is the file containing the ip address of the remote machine to connect to
2. Where `vm` is the name of the user on the remote machine. This wil help to use that particular user to log into the remote server when runing the ansible script.
Leave the remaining as the default values.

* Create hosts file and place the following code snippert in them

```
[backup]
172.173.152.44

[vm1]
20.168.242.95
```

Where:
* backup and vm1 is a way to group multiple remote machines together.

### Basic Example Of How Ansible Play / Task Looks Like
```yaml
---
- name: Install apache2 and nginx
  hosts: all
  tasks:
    - name: Install apache2
      apt:
        name: apache2
        state: latest
    - name: Install nginx
      apt:
        name: nginx
        state: latest
```

### Some Basics Ansible commands
* `ansible all --list`
* `ansible all -m ping`
* `ansible-playbook main.yml -C`
* `ansible-playbook main.yml -C -vvvv`
* `ansible-playbook main.yml`
* `ansible-playbook main.yml --tags=dev,stage`
* `ansible-playbook main.yml --list-tasks`
* `ansible-playbook main.yml --list-tags`
* `ansible-playbook main.yml --lists-hosts`

### Ansible Ad-Hoc Commands
This isa way of executing commands to remote machine whithout the use of playbooks.

**Shell Module**
* Using the shell module looks like this:
`ansible all -m shell -a 'echo $TERM'`
`ansible all -m shell -a "echo hello world"`
`ansible 13.10.1.67 -m shell -a "mkdir demo"`

**File Module**
* The file module allows changing ownership and permissions on files. These same options can be passed directly to the copy module as well:
`ansible webservers -m file -a "dest=/var/tmp/a.txt mode=600"`
`ansible webservers -m file -a "dest=/var/tmp/a.txt mode=600 owner=mdehaan group=mdehaan"`

* The file module can also create directories, similar to mkdir -p:
`ansible webservers -m file -a "dest=/var/tmp/testing mode=755 owner=mdehaan group=mdehaan state=directory"`

* As well as delete directories (recursively) and delete files:
`ansible webservers -m file -a "dest=/var/tmp/testing state=absent"`

**Managing Packages Module**
* Ensure a package is installed, but don’t update it
`ansible webservers -m apt -a "name=acme state=present"`
* Ensure a package is installed to a specific version
`ansible webservers -m apt -a "name=httpd-2.4.6-9* state=present"`
* Ensure a package is at the latest version:
`ansible webservers -m apt -a "name=httpd state=latest"` 
* Ensure a package is not installed:
`ansible webservers -m apt -a "name=httpd state=absent"`

**Users Group**
* The ‘user’ module allows easy creation and manipulation of existing user accounts, as well as removal of user accounts that may exist:
`ansible all -m user -a "name=foo password=KEnTeaStIFY"`
`ansible all -m user -a "name=foo state=absent"`

**Managing Services**
* Ensure a service is started on all webservers:
`ansible webservers -m service -a "name=httpd state=started"`

* Alternatively, restart a service on all webservers:
`ansible webservers -m service -a "name=httpd state=restarted"`

* Ensure a service is stopped:
`ansible webservers -m service -a "name=httpd state=stopped"`

**Without ssh key authentication**
What if you do not have SSH key-based authentication, then enter the user name and password while invoking the command as shown below.
`ansible multi -m ping  --user=vagrant --ask-pass`

**Remote machines uptime**
* `ansible all -m command -a uptime`
* `ansible all -m shell -a uptime` 
* `ansible all -a uptime`

**Check memory Usage**
* ansible ad hoc command to check memory usage
`ansible all -a "free -m"`

**Get physical memory allocated**
* ansible ad hoc command to get physical memory allocated to the host
`ansible all -m shell -a "cat /proc/meminfo|head -2"`

**Create users and groups**
* Create group
`ansible all -m group -a "name=weblogic state=present"`
* Create user
`ansible all -m user -a "name=weblogic group=weblogic createhome=yes" -b`

**Create directory**
* ansible ad hoc command to Create a Directory with 755 permission 
`ansible all -m file -a "path=/opt/oracle state=directory mode=0755" -b`

**Daling with files**
* ansible ad hoc command to Create a file with 755 permission
`ansible all -m file -a "path=/tmp/testfile state=touch mode=0755"`

* ad hoc command to Change ownership of a file
`ansible all -m file -a "path=/opt/oracle group=weblogic owner=weblogic" -i ansible_hosts -b`

**Disk space of host machine**
* ansible ad hoc command to  check free disk space of hosts
`ansible all -a "df -h"`

**Managing Cron Job and Scheduling with Ansible ad hoc**

* Run the job every 15 minutes
`ansible all -m cron -a "name='daily-cron-all-servers' minute=*/15 job='/path/to/minute-script.sh'"`

* Run the job every four hours
`ansible all -m cron -a "name='daily-cron-all-servers' hour=4 job='/path/to/hour-script.sh'"`

* Enabling a Job to run at system reboot
`ansible all -m cron -a "name='daily-cron-all-servers' special_time=reboot job='/path/to/startup-script.sh'"`

* Scheduling a Daily job
`ansible all -m cron -a "name='daily-cron-all-servers' special_time=daily job='/path/to/daily-script.sh'"`

* Scheduling a Weekly job
`ansible all -m cron -a "name='daily-cron-all-servers' special_time=weekly job='/path/to/daily-script.sh'"`