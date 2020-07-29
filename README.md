# Mastering Ansible - Just Notes ;)

## Installing on Mac OS X

Installing the VirtualEnv
```bash
sudo pip3 install virtualenv pyyaml
```

Creating the Virtual Env
```bash
virtualenv venv
```

Activating the Virtual Env
```bash
source venv/bin/activate
```

Installing the Ansible Dev Version
```bash
pip3 install git+https://github.com/ansible/ansible
```

Installing the Ansible Stable Version
```bash
pip3 install ansible
```

Testing the installation
```bash
ansible all -i localhost, -c local -m ping
[WARNING]: Platform darwin on host localhost is using the discovered Python interpreter at /usr/bin/python, but
future installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

## Validating Ansible Installation

Ansible Configuration File Precedence
- ANSIBLE_CONFIG
- ./ansible.cfg
- ~/.ansible.cfg
- /etc/ansible/ansible.cfg

```bash
ansible --version
ansible 2.9.11
  config file = None
  configured module search path = ['/Users/douglas/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /Volumes/Data/git/MasteringAnsible/venv_stable/lib/python3.8/site-packages/ansible
  executable location = /Volumes/Data/git/MasteringAnsible/venv_stable/bin/ansible
  python version = 3.8.5 (default, Jul 21 2020, 10:48:26) [Clang 11.0.3 (clang-1103.0.32.62)]
```

Supressing the Warning about Python interpreter
```bash
vim ansible.cfg
[defaults]
interpreter_python = auto_silent
```

Checking the ping again
```bash
ansible all -i localhost, -c local -m ping
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

Configuring the hosts, this entry must be in dns or in /etc/hosts
```bash
vim hosts
[all]
ubuntu01
```

Now we need to configure the ansible.cfg to see the hosts file
```bash
vim ansible.cfg
[defaults]
# Supress the warning
interpreter_python = auto_silent
# Inventory file
inventory = hosts
# Do not check the keys
host_key_checking = False
```

Try to connect into ubuntu01 without the host_key_checking configuration
```bash
ansible all -m ping
The authenticity of host 'ubuntu01 (10.0.0.21)' can't be established.
ECDSA key fingerprint is SHA256:ppfL4lqzavcYfK4iywzJmDW//R+610GFawVkYHDR0cE.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
ubuntu01 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'ubuntu01' (ECDSA) to the list of known hosts.\r\ndouglas@ubuntu01: Permission denied (publickey,password).",
    "unreachable": true
}
```

if we try again
```
 ansible all -m ping
ubuntu01 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: douglas@ubuntu01: Permission denied (publickey,password).",
    "unreachable": true
}
```

Now we can add the keys into the host in the .ssh/authorized_keys and execute the command again, use ssh-copy-id or when create the authorized_keys on CentOS 8 set the permission to 600
```bash
ansible all -m ping
ubuntu01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Passing the name of the target via command line
```bash
ansible all -i ubuntu01, -m ping
ubuntu01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

First look at the debug module
```
ansible all -m debug
ubuntu01 | SUCCESS => {
    "msg": "Hello world!"
}
```

Checking for documentation about the module
```bash
ansible all -m debug
ubuntu01 | SUCCESS => {
    "msg": "Hello world!"
}
```

Executing the debug module with debug message
```bash
ansible all -m debug --args='msg="This is a custom debug message"'
ubuntu01 | SUCCESS => {
    "msg": "This is a custom debug message"
}
```

Executing the debug module with debug message and verbosity
```bash
ansible all -m debug --args='msg="This is a custom debug message" verbosity=3'
ubuntu01 | SKIPPED
```

Executing the debug module with verbosity
```bash
ansible all -vvv -m debug --args='msg="This is a custom debug message" verbosity=3'
ansible 2.9.11
  config file = /Volumes/Data/git/MasteringAnsible/ansible.cfg
  configured module search path = ['/Users/douglas/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /Volumes/Data/git/MasteringAnsible/venv_stable/lib/python3.8/site-packages/ansible
  executable location = /Volumes/Data/git/MasteringAnsible/venv_stable/bin/ansible
  python version = 3.8.5 (default, Jul 21 2020, 10:48:26) [Clang 11.0.3 (clang-1103.0.32.62)]
Using /Volumes/Data/git/MasteringAnsible/ansible.cfg as config file
host_list declined parsing /Volumes/Data/git/MasteringAnsible/hosts as it did not pass its verify_file() method
script declined parsing /Volumes/Data/git/MasteringAnsible/hosts as it did not pass its verify_file() method
auto declined parsing /Volumes/Data/git/MasteringAnsible/hosts as it did not pass its verify_file() method
Parsed /Volumes/Data/git/MasteringAnsible/hosts inventory source with ini plugin
META: ran handlers
ubuntu01 | SUCCESS => {
    "msg": "This is a custom debug message"
}
META: ran handlers
META: ran handlers
```

Listing hosts from the group centos
```bash
ansible centos --list-hosts
  hosts (1):
    centos01
```

Listing host from the all group
```bash
ansible all --list-hosts
  hosts (2):
    ubuntu01
    centos01
```

## Ansible Architecture and Design

## Ansible Inventories
- Ansible inventories
  - Provide Ansible connectivity to our centos by means of root
  - Provide Ansible connectivity to our Ubuntu hosts by means of sudo
  - Inventory host variables
  - Simplification of the inventory file with ranges
  - Inventory group variables
  - Inventory children groups
- Ansible modules
- YAML
- Ansible playbooks, breakdown of sections
- Ansible playbooks, variables
- Ansible playbooks, facts
- Templating with Jinja2
- Ansible playbooks, creating and executing


## Mastering Ansible examples Repository
- Available on GitHub from the following url:
  - https://github.com/spurin/masteringansible
- Clone to control host with the command 
  - git clone https://github.com/spuring/masteringansible.git


Testing the connection
```bash
ansible linux -i hosts.yaml -m ping -o
zabbix-01 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"},"changed": false,"ping": "pong"}
zabbix-02 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
```

Testing overring the ssh port
```bash
ansible linux -m ping -e 'ansible_port=22' -o
zabbix-02 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"},"changed": false,"ping": "pong"}
zabbix-01 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"},"changed": false,"ping": "pong"}
```

## Ansible Modules

- Ansible Modules
- The setup module
- The file module
- Color notation used during Ansible execution
- Idempotence
- The copy module
- The command module
- Ansible-doc

## Setup Module
- Used for gathering facts when executing playbooks
- This module is automatically called by playbooks to gather useful variables about remote hosts that can be used in playbooks
- it can also be executed directly by /usr/bin/ansible to check what variables are available to a host.
- Ansible provides many 'facts' about the system, automatically. this module is also supported for Windows targets.

Let's check the setup of centos01
```json
ansible centos01 -m setup
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.0.22"
        ],
        "ansible_all_ipv6_addresses": [
            "2804:4dcc:e010:bf:ab0:272f:fe36:6ff0",
            "fe80::a00:27ff:fe36:6ff0"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        [...]
```

## File Module
- Used for file, symlinks and directory manipulation
- Sets attributes of files, symlinks and directories, or removes files/symlinks/directories
- Many other modules support the same options as the 'file' module - including [copy], [template] and [assemble].
- For Windows targets use the [win_file] module instead

Testing the module
```json
ansible linux -m file -a 'path=/tmp/test state=touch'
ubuntu01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "dest": "/tmp/test",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "size": 0,
    "state": "file",
    "uid": 0
}
centos01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": true,
    "dest": "/tmp/test",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:user_tmp_t:s0",
    "size": 0,
    "state": "file",
    "uid": 0
}
```

## Ansible Colours
- Green - Success
- Yellow - Success with changes
- Red - Failure

## Idempotency
- Idempotency - An operation is idempotent if the result of performing it once is exactly the same as the result of performing it repeatedly without any intervening actions.

Testing the file module with idempotency
```json
ansible linux -m file -a 'path=/tmp/test state=file mode=600'
ubuntu01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "gid": 0,
    "group": "root",
    "mode": "0600",
    "owner": "root",
    "path": "/tmp/test",
    "size": 0,
    "state": "file",
    "uid": 0
}
centos01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "gid": 0,
    "group": "root",
    "mode": "0600",
    "owner": "root",
    "path": "/tmp/test",
    "secontext": "unconfined_u:object_r:user_tmp_t:s0",
    "size": 0,
    "state": "file",
    "uid": 0
}
```

Now if we change the file mode and execute the module again only the host with the change mode differente from the ansible will get the changes
```json
ansible linux -m file -a 'path=/tmp/test state=file mode=600'
ubuntu01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "gid": 0,
    "group": "root",
    "mode": "0600",
    "owner": "root",
    "path": "/tmp/test",
    "size": 0,
    "state": "file",
    "uid": 0
}
centos01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "gid": 0,
    "group": "root",
    "mode": "0600",
    "owner": "root",
    "path": "/tmp/test",
    "secontext": "unconfined_u:object_r:user_tmp_t:s0",
    "size": 0,
    "state": "file",
    "uid": 0
}
```

## Copy Module
- Used for copying files, from the local or remote, to a location on the remote
- The 'copy' module copies a file from the local or remote machine to a location on teh remote machine. Use the [fetch] module to copy files from remote locations to the local box. If you need variable interpolation in the copied files, use the [template] module. For Windows targets, use the [win_copy] module instead.

Testing the copy module
```json
ansible linux -m copy -a 'src=hosts.yaml dest=/tmp/hosts.yaml'
ubuntu01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "checksum": "2547d35b6ccb778656d5d61ce2d4a9d77274962b",
    "dest": "/tmp/hosts.yaml",
    "gid": 0,
    "group": "root",
    "md5sum": "deb579a5c71feaa37dfe3a58cd85a232",
    "mode": "0644",
    "owner": "root",
    "size": 340,
    "src": "/home/douglas/.ansible/tmp/ansible-tmp-1595862716.471367-35783-48811134662319/source",
    "state": "file",
    "uid": 0
}
centos01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": true,
    "checksum": "2547d35b6ccb778656d5d61ce2d4a9d77274962b",
    "dest": "/tmp/hosts.yaml",
    "gid": 0,
    "group": "root",
    "md5sum": "deb579a5c71feaa37dfe3a58cd85a232",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 340,
    "src": "/root/.ansible/tmp/ansible-tmp-1595862716.5049548-35781-120565643474192/source",
    "state": "file",
    "uid": 0
}
```

Executing the copy on the remote host to the remote host
```json
ansible linux -m copy -a 'remote_src=yes src=/tmp/hosts.yaml dest=/tmp/hosts-02.yaml'
ubuntu01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "checksum": "2547d35b6ccb778656d5d61ce2d4a9d77274962b",
    "dest": "/tmp/hosts-02.yaml",
    "gid": 0,
    "group": "root",
    "md5sum": "deb579a5c71feaa37dfe3a58cd85a232",
    "mode": "0644",
    "owner": "root",
    "size": 340,
    "src": "/tmp/hosts.yaml",
    "state": "file",
    "uid": 0
}
centos01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": true,
    "checksum": "2547d35b6ccb778656d5d61ce2d4a9d77274962b",
    "dest": "/tmp/hosts-02.yaml",
    "gid": 0,
    "group": "root",
    "md5sum": "deb579a5c71feaa37dfe3a58cd85a232",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 340,
    "src": "/tmp/hosts.yaml",
    "state": "file",
    "uid": 0
}
```

## Command Module
- Used for executing remote commands
- The 'command' module takes the command name followed by a list of space-delimited arguments. The given command will be executed on all selected nodes. It will not be processed through the shell, so variables like '$HOME' and operations like "<", ">", "|", ";" and "&" will not work (use the [shell] module if you need these features). For Windows targets, use the [win_command] module instead.

Testing the command module
```json
ansible linux -m command -a 'hostname' -o
ubuntu01 | CHANGED | rc=0 | (stdout) puppetserver
centos01 | CHANGED | rc=0 | (stdout) centos8-base.dqs.local
```

If we not set the module ansible will use the command by default
```json
ansible linux -a 'hostname' -o
ubuntu01 | CHANGED | rc=0 | (stdout) puppetserver
centos01 | CHANGED | rc=0 | (stdout) centos8-base.dqs.local
```

Checking the id of the user
```json
ansible linux -a 'id'
ubuntu01 | CHANGED | rc=0 >>
uid=0(root) gid=0(root) groups=0(root)
centos01 | CHANGED | rc=0 >>
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

Executing the touch command but checking if the file exist before create
```json
ansible linux -a 'touch /tmp/test_copy_module creates=/tmp/test_copy_module'
[WARNING]: Consider using the file module with state=touch rather than running 'touch'.  If you need to use command because file is insufficient you can add 'warn:
false' to this command task or set 'command_warnings=False' in ansible.cfg to get rid of this message.
ubuntu01 | CHANGED | rc=0 >>

centos01 | CHANGED | rc=0 >>
```

Now executing again to double check
```json
ansible linux -a 'touch /tmp/test_copy_module creates=/tmp/test_copy_module'
ubuntu01 | SUCCESS | rc=0 >>
skipped, since /tmp/test_copy_module exists
centos01 | SUCCESS | rc=0 >>
skipped, since /tmp/test_copy_module exists
```

Removing the files only if it exists
```json
ansible linux -a 'rm /tmp/test_copy_module removes=/tmp/test_copy_module'
[WARNING]: Consider using the file module with state=absent rather than running 'rm'.  If you need to use command because file is insufficient you can add 'warn:
false' to this command task or set 'command_warnings=False' in ansible.cfg to get rid of this message.
ubuntu01 | CHANGED | rc=0 >>

centos01 | CHANGED | rc=0 >>
```

The same as the above command but throught the Ansible file module
```json
ansible linux -m file -a 'path=/tmp/test_copy_module state=absent'
ubuntu01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "path": "/tmp/test_copy_module",
    "state": "absent"
}
centos01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "path": "/tmp/test_copy_module",
    "state": "absent"
}
```

Creating a file and setting the permission
```json
ansible centos01 -m file -a 'path=/tmp/test_modules.txt state=touch mode=600'
centos01 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": true,
    "dest": "/tmp/test_modules.txt",
    "gid": 0,
    "group": "root",
    "mode": "0600",
    "owner": "root",
    "secontext": "unconfined_u:object_r:user_tmp_t:s0",
    "size": 0,
    "state": "file",
    "uid": 0
}
```

Fetching a file from the remote host
```json
ansible centos01 -m fetch -a 'src=/tmp/test_modules.txt dest=/tmp/test_modules.txt'
centos01 | CHANGED => {
    "changed": true,
    "checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709",
    "dest": "/tmp/test_modules.txt/centos01/tmp/test_modules.txt",
    "md5sum": "d41d8cd98f00b204e9800998ecf8427e",
    "remote_checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709",
    "remote_md5sum": null
}
```

Checking the file that we copy
```json
ls -l /tmp/test_modules.txt/centos01/tmp
total 0
-rw-------  1 douglas  wheel  0 Jul 27 14:05 test_modules.txt
```

Checking the documentation about the file module with ansible-doc
```bash
ansible-doc file
```

## YAML
- YAML - yet another markup language
- Structure of YAML files
- Identation
- Quotes
- Multiline values
- True/false
- Lists and dictionaries
- Online YAML to Python tool
  - http://yaml-online-parser.appspot.com

```bash
python -c 'import yaml,pprint;pprint.pprint(yaml.load(open("test.yml").read(), Loader=yaml.FullLoader))
```

## Ansible Playbooks, Breakdown of Sections
- The different sections in the Ansible playbooks
- How to use the target section
- How to use the tasks section
- How to use the vars section
- How to use handlers

Execute the playbook inside the motd
```json
ansible-playbook motd_playbook.yml

PLAY [centos] *********************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************
ok: [centos01]

TASK [Configure a MOTD (message of the day)] **************************************************************************************************************************
changed: [centos01]

PLAY RECAP ************************************************************************************************************************************************************
centos01                   : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## Example target Parameters
- sudo
- user
- sudo_user
- connection
- gather_facts

Checking the differente from the local file with the remote host
```bash
diff motd <(ssh centos01 'cat /etc/motd')
```

Executing a playbook and changing the variables in the command line
```json
ansible-playbook motd_playbook.yml -e 'motd="Testing the motd playbook\n"'
```

Executing a playbook and changing the variables in the command line
```json
ansible-playbook motd_playbook.yml --extra-vars='motd="Testing the motd playbook\n"'
```

Executing the playbook with handler
```json
 ansible-playbook motd_playbook.yml

PLAY [centos,ubuntu] **********************************************************************************************************************************************************************************

TASK [Configure a MOTD (message of the day)] **********************************************************************************************************************************************************
changed: [ubuntu01]
ok: [centos01]

RUNNING HANDLER [MOTD changed] ************************************************************************************************************************************************************************
ok: [ubuntu01] => {
    "msg": "The MOTD was changed"
}

PLAY RECAP ********************************************************************************************************************************************************************************************
centos01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu01                   : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Checking the inventory of the hosts
```json
ansible linux -m setup | grep ansible_distribution
        "ansible_distribution": "Ubuntu",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/os-release",
        "ansible_distribution_file_variety": "Debian",
        "ansible_distribution_major_version": "18",
        "ansible_distribution_release": "bionic",
        "ansible_distribution_version": "18.04",
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "8",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "8.2",
```

## Ansible Playbooks, Variables
- The different ways in which variables can be assigned
- Examples of lists and dictionaries
- The use of dot and python notation as an accessor
- Hostvars and Groupvars
- External variables
- Variable prompts
- Providing variables on the command line by means of the INI, JSON and YAML format
- Providing variables as external files in JSON and YAML format


## Ansible Playbooks, Facts
- Facts
- The setup module and how this relates to fact gathering
- Filtering for specific facts
- The creation of custom facts
- The execution of custom facts
- How custom facts can be used in environments without super user access

Gathering the network information
```json
ansible centos01 -m setup -a 'gather_subset=network'
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.0.22"
        ],
[...]
```

Filtering the output
```json
ansible centos01 -m setup -a 'gather_subset=network,!all,!min'
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.0.22"
        ],
```

Filtering the output with filter
```json
ansible centos01 -m setup -a 'filter=ansible_memfree_mb'
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_memfree_mb": 376,
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false
}
```

filtering with wildcard
```json

ansible centos01 -m setup -a 'filter=ansible_mem*'
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_memfree_mb": 375,
        "ansible_memory_mb": {
            "nocache": {
                "free": 590,
                "used": 228
            },
            "real": {
                "free": 375,
                "total": 818,
                "used": 443
            },
            "swap": {
                "cached": 0,
                "free": 2215,
                "total": 2215,
                "used": 0
            }
        },
        "ansible_memtotal_mb": 818,
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false
}
```

Getting the ipv4
```json
ansible centos01 -m setup -a 'filter=ansible_default_ipv4'
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_default_ipv4": {
            "address": "10.0.0.22",
            "alias": "enp0s3",
            "broadcast": "10.0.0.255",
            "gateway": "10.0.0.1",
            "interface": "enp0s3",
            "macaddress": "08:00:27:36:6f:f0",
            "mtu": 1500,
            "netmask": "255.255.255.0",
            "network": "10.0.0.0",
            "type": "ether"
        },
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false
}
```


## Custom Facts
- Can be written in any language
- Returns a JSON structure
- Returns an INI structure
- /etc/ansible/facts.d

executing the playbook targeting the host
```json
ansible-playbook facts_playbook.yaml -l ubuntu01
```

Removing directory
```json
ansible linux -m file -a 'path=/home/user/ansible/facts.d state=absent' -o
```

## Templating with Jinja2
- The Jinja2 templating language
- if/elif/else statements
- for loops
- break and continue
- ranges
- Jinja2 filters

## Ansible Playbooks, Creating, and Executing
- A use case example of using Ansible playbooks
- Installing the Nginx web server on CentOS and Ubuntu
- Using Ansible to standardize the configuration across both of the platforms
- Using Ansible to fix any issues that arise owing to differences in the different operating systems
- Templating a website
- Using Ansible facts to customize our template page

Getting the distribution from the hosts
```json
ansible all -i ubuntu01,centos01, -m setup -a 'filter=ansible_distribution*'
ubuntu01 | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "Ubuntu",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/os-release",
        "ansible_distribution_file_variety": "Debian",
        "ansible_distribution_major_version": "18",
        "ansible_distribution_release": "bionic",
        "ansible_distribution_version": "18.04",
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false
}
centos01 | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "8",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "8.2",
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false
}
```



## TODO 
- [ ] Create files fo each sysctl configuration.
- [ ] Create file for Zabbix Agent
- [ ] Create file for Vim
- [ ] Create file for Node Exporter
- [ ] Create file for Motd
- [ ] Create file for bashrc
- [ ] Check the Monit



## Some VMs info

- Account information:
  - packt user, password = 'password'
  - root user, password = 'password'
  - centos virtual machines, root access permitted
  - ubuntu virtual machines, root access by means of sudo from packt user


## References
- https://www.shellhacks.com/python-install-pip-mac-ubuntu-centos/
- https://docs.ansible.com
- https://docs.ansible.com/ansible/latest/index.html
- https://github.com/github/gitignore/tree/master/Global
- https://goo.gl/b68auU
- https://docs.ansible.com/ansible/latest/reference_appendices/config.html
- https://docs.ansible.com/ansible/latest/plugins/connection/ssh.html
- https://docs.ansible.com/ansible/latest/modules/list_of_files_modules.html
- https://docs.ansible.com/ansible/latest/modules/command_module.html#command-module
- https://docs.ansible.com/ansible/latest/modules/list_of_all_modules.html
- https://github.com/yaml/pyyaml/wiki/PyYAML-yaml.load(input)-Deprecation
- http://yaml.org/spec/1.2/spec.html
- https://en.wikipedia.org/wiki/YAML
- https://stackoverflow.com/questions/3790454/in-yaml-how-do-i-break-a-string-over-multiple-lines
- https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html
- https://www.middlewareinventory.com/blog/ansible-find-examples/
- https://devops.stackexchange.com/questions/9209/loop-chmod-with-ansible
- https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html
- https://docs.ansible.com/ansible/latest/modules/find_module.html
- https://www.toptechskills.com/ansible-tutorials-courses/how-to-fix-usr-bin-python-not-found-error-tutorial/
- https://geekflare.com/ansible-ad-hoc-command/
- https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters_ipaddr.html
- https://www.howtoforge.com/ansible-guide-ad-hoc-command/
- https://sites.google.com/site/mrxpalmeiras/ansible/ansible-cheat-sheet
- https://www.middlewareinventory.com/blog/ansible-get-ip-address/
- https://docs.ansible.com/ansible/latest/user_guide/playbooks_templating.html
- https://docs.ansible.com/ansible/latest/modules/systemd_module.html
- https://docs.ansible.com/ansible/latest/modules/sysctl_module.html#sysctl-module
- https://docs.ansible.com/ansible/latest/reference_appendices/config.html#ansible-configuration-settings-locations
- https://docs.ansible.com/ansible/latest/modules/wait_for_module.html
- https://blog.zabbix.com/zabbix-agent-active-vs-passive/9207/#:~:text=Active%20checks,-Changing%20the%20active&text=This%20is%20the%20list%20of,minutes%20to%20request%20the%20configuration.&text=In%20the%20same%20zabbix_agentd.,also%20a%20parameter%20called%20Hostname.