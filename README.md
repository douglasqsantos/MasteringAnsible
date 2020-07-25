# Mastering Ansible - Just Notes ;)

## Installing on Mac OS X

Installing the VirtualEnv
```bash
sudo pip3 install virtualenv
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



## Some VMs info

- Account information:
  - packt user, password = 'password'
  - root user, password = 'password'
  - centos virtual machines, root access permitted
  - ubuntu virtual machines, root access by means of sudo from packt user


## References
- https://docs.ansible.com
- https://docs.ansible.com/ansible/latest/index.html
- https://github.com/github/gitignore/tree/master/Global
- https://goo.gl/b68auU
- https://docs.ansible.com/ansible/latest/reference_appendices/config.html