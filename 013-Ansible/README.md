Ansible
=======

References
==========
1. [SafariBooks - Ansible Fundamental LiveLessons, Sander Van Vught](https://www.safaribooksonline.com/library/view/ansible-fundamentals-livelessons/)
2. [SafariBooks - LEARNING PATH: Ansible: Guide to Master Ansible 2](https://www.safaribooksonline.com/learning-paths/learning-path-ansible/9781789345216/)


Table of Contents
=================

  * [Ansible - Notes](#ansible---notes)
    * [1. Components:](#1-components)
    * [2. What Ansible can and cannot](#2-what-ansible-can-and-cannot)
    * [3. How does Ansible work?](#3-how-does-ansible-work)
    * [4. Use Cases](#4-use-cases)
    * [5. Installation](#5-installation)
    * [6. Inventory](#6-inventory)
    * [7. Usage](#7-usage)
    * [8. Configuration](#8-configuration)
    * [9. Escalating privileges](#9-escalating-privileges)
      * [How to enable privilege escalation?](#how-to-enable-privilege-escalation)
    * [10. Ad-hoc commands](#10-ad-hoc-commands)

## 1. Components:

* **Ansible Core**
	The automation platform

* **Ansible Tower**
	A Web application and REST API for Ansible

* **Ansible Galaxy**
	A website with a large catalogue of community created roles.


## 2. What Ansible can and cannot

* Ansible works by changing the state of a host machine to a pre-defined state.
* Ansible can push configuration changes to the hosts
* Ansible can perform custom operations on the hosts.
* Ansible cannot install machines
* Ansible cannot monitor machines or changes in configurations.
* Ansible can deploy and manipulate the config files on a remote host.
* Ansible can use playbook templates to be applied on multiple machines at once and have them on the same level of configuration.
* Remote task execution is another use-case. Example:
	a. Config software repos.
	b. Install applications
	c. Tune config files
	d. Open firewall ports
	e. Start services
	f. Check if the application process is running.
	etc..


## 3. How does Ansible work?

1. Ansible doesn't have a Client/Server architecture, hence is agentless.
2. The `Control node` acts as the server, where Ansible is installed.
3. The `Managed Hosts` acts as the clients where the operations are executed.
4. Ansible includes several modules exist, for specific purposes.
5. Hostname (Resolvable) or IP address of the `Managed Hosts` are kept in a `host inventory` text file on the `Control Node`.
6. The operations needed to be performed are put in a YAML file called a `Playbook`.
7. An operation is called a `Play`, and the grouping of Plays are done in a `Playbook`.
7. The `Playbook` is executed from the `Controller Node` which then connects to the `Managed Hosts` via passwordless SSH.


## 4. Installation

1. Ansible for RHEL is not currently packaged, and hence needs to be installed from EPEL.
2. Enable EPEL on the Ansible Control node, as well as the `Optional` child channel.
3. Python 2.6 and higher needs to be available on the Ansible Control and Managed hosts. Check the installed python version with:
~~~
# yum list installed python
~~~
4. Install Ansible with:
~~~
# yum install ansible
~~~

## 6. Inventory

1. Inventory is a list of hosts (either hostnames or IP addresses) used by Ansible to act upon.
2. The default inventory file is at /etc/ansible/inventory.
3. Custom inventory files can be created, and passed to Ansible to parse with the `-i` switch. For example:

~~~
# ansible -i <custom-inventory-file>
~~~

## 7. Usage

Let's see how we can use the `ansible` command to parse the Inventory file in various ways.

In this example, we have a created a custom inventory file with the content below, and save it as `inventory_list`. The name can be chosen at will. In this example, we'll use `inventory_list`.

~~~
[lab]
lab1.testing.com
lab2.testing.com

[test]
testnode1.testing.com
testnode2.testing.com

[data_center_first]
labhost1.testing.com
testnode1.testing.com

[data_center_second]
labhost2.testing.com
testnode2.testing.com
192.168.10.10

[datacenter:children]
data_center_first
data_center_second

[new]
192.168.100.100
192.168.100.101
~~~

1. Parse the inventory file for hosts. A few examples are given below.

a. List all the hosts.

~~~
# ansible -i inventory_list --list-hosts all
  hosts (9):
    labhost1.testing.com
    testnode1.testing.com
    labhost2.testing.com
    testnode2.testing.com
    192.168.10.10
    web.testing.com
    10.1.1.254
    192.168.100.100
    192.168.100.101
~~~

b. Hosts can be called bound to specific names. Example, calling hosts starting with `192.X.X.X` IP address.

~~~
# ansible -i inventory_list --list-hosts 192.*
  hosts (3):
    192.168.100.100
    192.168.100.101
    192.168.10.10
~~~

c. Calling hosts under the `datacenter` group.

NOTE: The `datacenter` group has two child groups under it, namely `data_center_first` and `data_center_second`. Hence when the `datacenter` group is called, it prints the whole set of hosts in the two groups.

~~~
# ansible -i inventory_list --list-hosts datacenter
  hosts (4):
    labhost1.testing.com
    testnode1.testing.com
    labhost2.testing.com
    testnode2.testing.com
~~~

d. It's possible to call the hosts in the specific location `data_center_first` with:

~~~
# ansible -i inventory_list --list-hosts data_center_first
  hosts (2):
    labhost1.testing.com
    testnode1.testing.com
~~~

e. Multiple host types/names/regular-expressions can be specified in a single line.

~~~
# ansible -i inventory_list  --list-hosts lab,192.*
  hosts (5):
    labhost1.testing.com
    labhost2.testing.com
    192.168.10.10
    192.168.100.100
    192.168.100.101
~~~

f. All the hosts can be listed with `all` and `*` as well.

~~~
# ansible -i inventory_list --list-hosts '*'
  hosts (9):
    labhost1.testing.com
    testnode1.testing.com
    labhost2.testing.com
    testnode2.testing.com
    192.168.10.10
    web.testing.com
    10.1.1.254
    192.168.100.100
    192.168.100.101
~~~

g. The asterisk `*` can be used as a wildcard.

~~~
# ansible -i inventory_list --list-hosts '*.example.*'
  hosts (5):
    labhost1.testing.com
    testnode1.testing.com
    labhost2.testing.com
    testnode2.testing.com
    web.testing.com
~~~

h. List hosts that are part of either one or more groups.

NOTE: This actually prints the hosts that are part of the groups mentioned.

~~~
# ansible -i inventory_list --list-hosts data_center_first:data_center_second
  hosts (5):
    labhost1.testing.com
    testnode1.testing.com
    labhost2.testing.com
    testnode2.testing.com
    192.168.10.10

[root@ansible1 ~]# ansible -i inventory_list --list-hosts lab:data_center_first
  hosts (3):
    labhost1.testing.com
    labhost2.testing.com
    testnode1.testing.com

[root@ansible1 ~]# ansible -i inventory_list --list-hosts data_center_first,data_center_second
  hosts (5):
    labhost1.testing.com
    testnode1.testing.com
    labhost2.testing.com
    testnode2.testing.com
    192.168.10.10

[root@ansible1 ~]# ansible -i inventory_list --list-hosts lab,data_center_first
  hosts (3):
    labhost1.testing.com
    labhost2.testing.com
    testnode1.testing.com
~~~

i. To list hosts that are in multiple groups, ie.. intersection, use ':&'

~~~
# ansible -i inventory_list --list-hosts 'test:&data_center_second'
  hosts (1):
    testnode2.testing.com

# ansible -i inventory_list --list-hosts 'lab:&data_center_first'
  hosts (1):
    labhost1.testing.com
~~~

j. To list hosts that are in a specific group but not in another group, can be done with:

NOTE: This is exclusion, and can be done between either groups, or groups and hostnames.

~~~
# List the host which is in `test` but not in `data_center_first`.
# ansible -i inventory_list --list-hosts 'test:!data_center_first'
  hosts (1):
    testnode2.testing.com

# List the hosts which are in `test` but not the hosts starting with `testnode2...`.
# ansible -i inventory_list --list-hosts 'test:!testnode2.*'
  hosts (1):
    testnode1.testing.com

# List all the hosts in the inventory, but omit the hosts in the `data_center_first` group
# ansible -i inventory_list --list-hosts 'all:!data_center_first'
  hosts (7):
    labhost2.testing.com
    testnode2.testing.com
    192.168.10.10
    web.testing.com
    10.1.1.254
    192.168.100.100
    192.168.100.101
~~~

**NOTE**: Further complex use-cases are available with regular expressions, and can be seen at http://docs.ansible.com/ansible/intro_patterns.html

## 8. Configuration

Ansible looks for a configuration file in the following order. It stops at the first hit.

* The file name set in the env variable `ANSIBLE_CONFIG` for the user.
* Else, `ansible.cfg` in the location from where `ansible` is executed.
* Else, `~/.ansible.cfg` in the user's home directory.
* Else, /etc/ansible/ansible.cfg

To know the current configuration file being used, try:

~~~
#  ansible --version
ansible 2.1.0.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = Default w/o overrides
~~~

OR add a `-v` to the commands executed:

~~~
# ansible -i inventory_list --list-hosts testnode1* -v
Using /etc/ansible/ansible.cfg as config file
  hosts (1):
    testnode1.testing.com
~~~

## 9. Escalating privileges

While running Ansible commands as a normal user, it is possible to force ansible to ask for SUDO password for additional security.

This is enabled by the `become_*` tags under the [privilege_escalation] section

### 9.1. How to enable privilege escalation?

1. Check the ansible config file

~~~
# ansible --version
ansible 2.1.0.0
  config file = /home/student/dep-manage/ansible.cfg
  configured module search path = Default w/o overrides
~~~

2. Edit the config file, and append the following.

**NOTE**: Refer /etc/ansible/ansible.cfg, in case of doubt.

~~~
[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=True
~~~

* Explanation
	* become 			: Force further security
	* become_method		: How to force security
	* become_user		: The user to switch to
	* become_ask_pass	: Request password or not

3. The above configuration should prompt for a password when an `ansible` command is executed.

~~~
# ansible --list-hosts all
SUDO password:      <<-- Prompting for a password.
  hosts (2):
    localhost
    servera
~~~

## 10. Command execution on Managed hosts

One of the most used features of Ansible is running commands on external or Managed hosts.

Ansible modules can be used to do different operations. The `command` module can be used to run commands on managed hosts.

* To run a command on a managed host using the `command` module, use:

~~~
# ansible -m command -a "/usr/bin/hostname" <groupname/hostname>
~~~

* Examples:

1. Run the `hostname` command on the hosts grouped under the name `mygroup`.

~~~
$ ansible -m command -a "hostname" mygroup
ansible1 | SUCCESS | rc=0 >>
ansible1

ansible2 | SUCCESS | rc=0 >>
ansible2
~~~

2. Run the `date` command on the host `ansible2`.

~~~
$ ansible -m command -a date ansible2
ansible2 | SUCCESS | rc=0 >>
Sun Jul 24 15:21:47 IST 2016
~~~

**NOTE**: To print the output in a single line for better readability, add the `-o` switch.

3. Run the `date` command on the host `ansible2` and print in a single line.

~~~
# ansible -m command -a date -o  ansible2
ansible2 | SUCCESS | rc=0 | (stdout) Sun Jul 24 15:24:37 IST 2016
~~~

* The `command` module cannot/doesn't access the shell on the managed host, and hence cannot access the shell variables, do redirection, pipe output etc..

* This can be worked around using the module `shell`.

* Examples:

4. Grep the string `ansible` from the output of `/etc/hosts`.

**NOTE**: We're piping the output of `cat /etc/hosts` to `grep`. Even though `grep` can be done directly on the file, we use this example to show that pipes doesn't work in the `command` module.

~~~
$ ansible -m command -a 'cat /etc/hosts | grep ansible' ansible2
ansible2 | FAILED | rc=1 >>
~~~

* This will work fine while using the `shell` module

~~~
$ ansible -m shell -a 'cat /etc/hosts | grep ansible' ansible2
ansible2 | SUCCESS | rc=0 >>
192.168.124.101 ansible1 # Control Node
192.168.124.23  ansible2 # Manage host 1
~~~

## 11. Connection settings for command execution on Managed hosts

When the connection-related parameters have been read, Ansible proceeds with making connections to the managed host.

By default, connections to managed hosts are initiated using the SSH protocol.

The SSH protocol requires the connection be established using an account on
the managed host. This account is referred to by Ansible as the remote user and is defined using the remote_user setting under the `[defaults]` section of the Ansible configuration file.

**NOTE**: The `remote_user` parameter is commented out by default.

When `remote_user` is commented, the remote commands are run with the same account name as the user executing the commands on the Control node. Hence, in case to run at least basic commands, you need the same user on the Control node and the Managed hosts.

If you want the commands to be run as a different user on the Managed hosts, add a user name for `remote_user` in the [defaults] section.

**NOTE**: If Privilege escalation is used (mentioned in section 9.1), the command is run as the root user on the managed hosts.

## 12. Edit remote files

1. Append text in a file on a remote host

**NOTE**: This example appends the string to /etc/motd on the `ansible2` node. Since we're running the ansible command as the user `student`, we won't be able to write the content to the file remotely.

* To elevate the permissions on the managed host, we can use '-u root' so that the content is written as the `root` user.

~~~
$ ansible -m shell -a 'echo "Testing Ansible remote connections" >> /etc/motd' ansible2 -u root
ansible2 | SUCCESS | rc=0 >>
~~~

* A module named `copy` exists which can be used to copy content to specific files.

~~~
$ ansible -m copy -a 'content="Managed by Ansible\n" dest=/etc/motd' -u root ansible2
ansible2 | SUCCESS => {
    "changed": true,
    "checksum": "4458b979ede3c332f8f2128385df4ba305e58c27",
    "dest": "/etc/motd",
    "gid": 0,
    "group": "root",
    "md5sum": "65a4290ee5559756ad04e558b0e0c4e3",
    "mode": "0644",
    "owner": "root",
    "secontext": "system_u:object_r:etc_t:s0",
    "size": 19,
    "src": "/root/.ansible/tmp/ansible-tmp-1469359336.29-144270528682025/source",
    "state": "file",
    "uid": 0
}
~~~

## 13. Handling multiple inventory files

Ansible can support reading from multiple inventory files in a single execution. Certain conditions exist though:

* The inventory files must be in a directory
* The files must be named in an alphabetical hierarchy. The files will be in alphabetical order.
* Dependency on a file that is being read later is not allowed. ie.. if file A refers to a group or host that is mentioned in file D, the process will error out.

* Example:

1. Create two inventory files `inventory-A` and `inventory-B` in a folder named `inventory`, with the following content.

~~~
$ cat inventoryA
[myself:children]
mygroup

$ cat inventoryB
[myself]
localhost

[intraweb]
servera

[everyone]
localhost
servera

[mygroup]
ansible1
ansible2
~~~

2. Try listing the `mygroup` group defined in `inventoryB` by passing `inventoryA`.

~~~
$ ansible -i inventoryA --list-hosts mygroup
inventoryA:2: Section [myself:children] includes undefined group: mygroup
~~~














