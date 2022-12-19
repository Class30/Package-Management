@@ -0,0 +1,28 @@
## **Introduction to Ansible**
    https://github.com/Kenmakhanu/Ansible.git
- Ansible is an open-source configuration Management,
deployment and provisioning Automation tool maintained by Redhat.
- It is very, very simple to setup and yet powerful.
- Ansible will be helpful to perform:
    . Configuration Management
    . Application Deployment
    . Task Automation
    . IT Orchestration

## **How Ansible works**
  - Ansible works by connecting to remote nodes (hosts) and pushing out small programs, called “Ansible
    modules” to them.
  - The pushed programs/modules will be executed on remote server by Ansible over SSH and removes them
    when finished.
  - Unlike Puppet or Chef it doesn’t use an agent on the remote host, Instead Ansible uses SSH. It is agentless.
  - It’s written in Python which needs to be installed on the remote host.
  - This means that you don’t have to setup a client server environment before using Ansible

## **Benefits of using Ansible**
 - It is a free open -source Automation tool and simple to use.
 - Uses existing OpenSSH for connection
 - Agent-less – No need to install any agent on Ansible Clients/Nodes
 - Python/YAML based
 - Highly flexible and versatile in configuration management of systems.
 - Large number of ready to use modules for system management
 - Custom modules can be added if needed
 31  
Ansible/01-Introduction/02-Installation.md
@@ -0,0 +1,31 @@
## **Ansible Installation**

- Ansible is designed to run on Unix/Linux systems, therefore windows systems aren’t
supported for the control node.
- Ansible is python based and therefore the control node and the managed nodes need to
have python2 or python3 installed on them.

- Ansible can be installed in three ways:
  . Using yum or apt
  . Using pip
  . Using compile file
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

## **Installing Ansible on Ubuntu**
#
  $ sudo adduser ansible \
  $ echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible \
  $ sudo su - ansible \
  $ sudo apt-add-repository ppa:ansible/ansible \
  $ sudo apt install ansible -y

## **Ansible installation on REDHAT EC2**
#
  $sudo useradd ansible \
  $sudo hostname ansible \
  $echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible \
  $sudo su - ansible # Enable PassowrdLogin and assign password to ansible user \
  $ sudo yum install python3 -y \
  $ sudo alternatives --set python /usr/bin/python3 \
  $ sudo yum -y install python3-pip -y \
  $ pip3 install ansible --user
 36  
Ansible/01-Introduction/03-Ansible_config_file.md
@@ -0,0 +1,36 @@
## **Ansible Directory Structure**

- The default ansible home directory is /etc/ansible
- This directory will consist of:
a) ansible.cfg
b) hosts
c) roles

## **Default ansible configuration file**
- The default location is: /etc/ansible/ansible.cfg,
 in which we can make various settings like location of inventory file, host_key_checking as False
- But we can define ansible configuration file in different location
and for this there is a priority for this files.

**Locations with priority(starting from top to bottom):** \
      - ANSIBLE_CONFIG environment variable \
      - ./ansible.cfg from the current directory \
      - ~/.ansible.cfg file present in home directory \
      - /etc/ansible/ansible.cfg default ansible.cfg file.

- Ansible will only use the configuration settings from the file
which is found in this sequence first, it will not look for the settings
in the higher sequence files if the setting is not present in the file
which is chosen for deployment

## **Host Key Checking**

- Anytime you make an ssh connection with a server for the
first time, you will be prompted to confirm if you want to
continue making the connection.
- This feature is by default set to true in the ansible.cfg file
- Disable this by uncommenting the line in the configuration file

   or

 **export ANSIBLE_HOST_KEY_CHECKING=false**
 80  
Ansible/01-Introduction/04-Managed-nodes_configuration.md
@@ -0,0 +1,80 @@
## **Configuring managed nodes.**
- Launch/select required no of servers (managed
nodes)
- Ansible Engine uses SSH Connection to connect
and work with Manage nodes.
- We can create SSH Connection in two ways:
     a) Password Authentication
     b) Password-less Authentication(This is with SSHKeys)
- Provide the managed Nodes IP/FQDN in
inventory file on Ansible Engine.
- Test the connectivity by running:

 **$ ansible all -m ping**

## a) Password Authentication
=================================
- Create same user(ansible) across all servers and provide
password for all users.

 **$ sudo adduser ansible**

- Provide root privileges to all ansible users on all servers.

 **$ echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible**

- Make sure that PasswordAuthentication is enabled to yes in all servers under
/etc/ssh/sshd_config file.
- Restart the sshd service

 **$ sudo systemctl restart sshd.service**
- Test connectivity by providing a -k option to be prompted to enter the SSH password.

  **$ ansible all -m ping -k**

- By default, Ansible tries to connect to the nodes as a remote user with the same name as your current system user, using its corresponding SSH keypair.

- To connect as a different remote user, append the command with the -u flag and the name of the intended user:

  **$ ansible all -m ping -u sammy**

- If the remote user has a password, use -k option to be prompted to enter the SSH password.\
  **$ ansible all -m ping -u sammy -k**

 **Host and group variables**
- If were run the ping command on all servers, we will get permission denied on the servers that need a password to authenticate.
- instead of that, we can provide the password in the host file
- This is at a host level or host level variable.
#
    **Host variables**
    [db]
    172.31.13.31  ansible_ssh_pass=ansible

- You can also connect using a different user by specifying the user in the hosts file.
#
    [db]
    172.31.13.31  ansible_ssh_user=sammy ansible_ssh_pass=abc123

- Create a file on the managed nodes to see which user its working with. \
   **$ ansible all -m file -a "path=test.txt state=touch"**
#  
    **Group variables:**
    [db:vars]
    ansible_ssh_user=sammy
    ansible_ssh_pass=abc123

- Host variables have the highest priorities. If variables are defined at a host level, then those variables will have precedence over variables that are defined at a group level.

## b) **Password-less Authentication (SSH_Keys)**
- Generate ssh-keys using ssh-keygen command from ansible user in the control machine.
- Copy ssh public key using ssh-copy-id <hostname> from
/home/ansible/.ssh/ location.
- Now login to remote server without providing password with the
following command:

 **$ ssh user_name@hostname**

- Now we can test connection from Ansible Engine to Managed Node
using:

 **$ ansible all -m ping**
 12  
Ansible/01-Introduction/terraform files/ansible-install-rhel.sh
@@ -0,0 +1,12 @@
#! /bin/bash
sudo useradd ansible
echo "ansible:ansible" | chpasswd
sudo hostname ansible
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
sudo su - ansible
# Enable PassowrdLogin and assign password to ansible user
sudo yum install python3 -y
sudo alternatives --set python /usr/bin/python3
# Install pip package manager for for python
sudo yum -y install python3-pip -y
pip3 install ansible --user
 7  
Ansible/01-Introduction/terraform files/ansible-install-ubuntu.sh
@@ -0,0 +1,7 @@
#! /bin/bash
sudo adduser ansible
echo "ansible:ansible" | chpasswd
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
sudo su - ansible
sudo apt-add-repository ppa:ansible/ansible
sudo apt install ansible -y
 6  
Ansible/01-Introduction/terraform files/create_ansible_user.sh
@@ -0,0 +1,6 @@
#!/bin/bash
sudo useradd -d /home/ansible -s /bin/bash -m ansible
sudo echo "ansible:ansible" | chpasswd
sudo echo "ansible  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
sudo sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config
sudo service sshd restart
 24  
Ansible/01-Introduction/terraform files/data0.tf
@@ -0,0 +1,24 @@
data "aws_ami" "ubuntu" {
  most_recent      = true
  owners           = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
  filter {
    name   = "architecture"
    values = ["x86_64"]
  }

}
 24  
Ansible/01-Introduction/terraform files/data1.tf
@@ -0,0 +1,24 @@
data "aws_ami" "rhel" {
  most_recent      = true
  owners           = ["309956199498"]

  filter {
    name   = "name"
    values = ["RHEL_HA-8.4.0_HVM-*-GP2"]
  }

  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
  filter {
    name   = "architecture"
    values = ["x86_64"]
  }

}
 34  
Ansible/01-Introduction/terraform files/ec2.tf
@@ -0,0 +1,34 @@
#resource block
resource "aws_instance" "ubuntu" {
  ami = data.aws_ami.ubuntu.id
  instance_type = var.my_instance_type
  user_data = file("${path.module}/ansible-install-ubuntu.sh")
  key_name = var.my_key

  tags = {
    "Name" = "Ansible-Ubuntu"
  }
}

resource "aws_instance" "rhel" {
  ami = data.aws_ami.rhel.id
  instance_type = var.my_instance_type
  user_data = file("${path.module}/ansible-install-rhel.sh")
  key_name = var.my_key

  tags = {
    "Name" = "Ansible-rhel8"
  }
}

resource "aws_instance" "ubuntu-hosts" {
  ami = data.aws_ami.ubuntu.id
  instance_type = var.my_instance_type
  user_data = file("${path.module}/create_ansible_user.sh")
  key_name = var.my_key
  count = 3
  tags = {
    "Name" = "My-Ubuntu-${count.index}"
    "Type" = "My-Ubuntu-${count.index}"
  }
}
 22  
Ansible/01-Introduction/terraform files/outputs.tf
@@ -0,0 +1,22 @@
# Terraform Output Values
output "ansible_publicip" {
  description = "EC2 Instance Public IP"
  value = aws_instance.ubuntu.public_ip
}

output "rhel_publicip" {
  description = "EC2 Instance Public IP"
  value = aws_instance.rhel.public_ip
}

# EC2 Instance Public IP
output "instance_publicip" {
  description = "EC2 Instance Public IP"
  value = aws_instance.ubuntu-hosts.*.public_ip
}

# EC2 Instance Public DNS
output "instance_privateIp" {
  description = "EC2 Instance Private IP"
  value = aws_instance.ubuntu-hosts.*.private_ip
}
 21  
Ansible/01-Introduction/terraform files/provider.tf
@@ -0,0 +1,21 @@
#terraform block
terraform {
  required_version = "~> 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0" # Optional but recommended in production
    }
  }
}


#provider block
provider "aws" {
  region  = var.aws_region
  profile = "Kenmak"
}
\*
#command to reset your credentials incase you get an authentication error.
#for var in AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN AWS_SECURITY_TOKEN ; do eval unset $var ; done
*\
 14  
Ansible/01-Introduction/terraform files/variables.tf
@@ -0,0 +1,14 @@
variable "aws_region"{
  type = string
  default = "us-west-1"
}

variable "my_instance_type"{
  type = string
  default = "t2.micro"
}

variable "my_key"{
  type = string
  default = "Automation1"
}
 102  
Ansible/02-adhoc-commands/Readme.md
@@ -0,0 +1,102 @@
## **Ad-hoc Commands**

- An Ad-Hoc command is a one-liner ansible command that performs one task on the target host(s)/group(s).

- Unlike playbooks — which consist of collections of tasks that can be reused — ad hoc commands are tasks that you don’t perform frequently, such as restarting a service or retrieving information about the remote systems that Ansible manages.

- This command will only have two parameters,\
        the group / target of a host that you want to perform the task and \
        the Ansible module to run.

 **Modules:**
  These are small programs that do some work on the server. They are the main building blocks
  of Ansible and are basically reusable scripts that are used by Ansible Ad-hoc and playbooks.
  Ansible comes with a number of reusable modules.

  - The basic syntax of an Ad-hoc command is

  **$ ansible [ -i inventory_file ] <server1:server2:Group1:Group2> -m <module> [-a arguments]**

  - To list all available modules:
    **$ ansible-doc -l**

## Testing Connection to Ansible Hosts
The following command will test connectivity between your Ansible control node and all your Ansible hosts. This command uses the current system user and its corresponding SSH key as the remote login, and includes the -m option, which tells Ansible to run the ping module. It also features the -i flag, which tells Ansible to ping the hosts listed in the specified inventory file

  **$ ansible all -i inventory -m ping**

- If this is the first time you’re connecting to these servers via SSH, you’ll be asked to confirm the authenticity of the hosts you’re connecting to via Ansible. When prompted, type yes and then hit ENTER to confirm.

You should get output similar to this:
#
     Output
     server1 | SUCCESS => {
       "changed": false,
        "ping": "pong"
      }
     server2 | SUCCESS => {
       "changed": false,
        "ping": "pong"
      }

- Once you get a "pong" reply back from a host, it means the connection is live and you’re ready to run Ansible commands on that server.

## Adjusting Connection Options
By default, Ansible tries to connect to the nodes as a remote user with the same name as your current system user, using its corresponding SSH keypair.

To connect as a different remote user, append the command with the -u flag and the name of the intended user:

  **$ ansible all -i inventory -m ping -u sammy**

- If you’re using a custom SSH key to connect to the remote servers, you can provide it at execution time with the --private-key option:

 **$ ansible all -i inventory -m ping --private-key=~/.ssh/custom_id**

- Once you’re able to connect using the appropriate options, you can adjust your inventory file to automatically set your remote user and private key, in case they are different from the default values assigned by Ansible. Then, you won’t need to provide those parameters in the command line.

The following example inventory file sets up the ansible_user variable only for the server1 server:
#
    ~/ansible/inventory
     server1 ansible_host=203.0.113.111 ansible_user=sammy
     server2 ansible_host=203.0.113.112

- Ansible will now use sammy as the default remote user when connecting to the server1 server.

- To set up a custom SSH key, include the ansible_ssh_private_key_file variable as follows:
#
    ~/ansible/inventory
    server1 ansible_host=203.0.113.111 ansible_ssh_private_key_file=/home/sammy/.ssh/custom_id
    server2 ansible_host=203.0.113.112

- In both cases, we have set up custom values only for server1. If you want to use the same settings for multiple servers, you can use a child group for that:
#
    ~/ansible/inventory
    [group_a]
    203.0.113.111
    203.0.113.112

    [group_b]
    203.0.113.113

    [group_a:vars]
    ansible_user=sammy
    ansible_ssh_private_key_file=/home/sammy/.ssh/custom_id

- This example configuration will assign a custom user and SSH key only for connecting to the servers listed in group_a.

## Defining Targets for Command Execution
- When running ad hoc commands with Ansible, you can target individual hosts, as well as any combination of groups, hosts and subgroups. For instance, this is how you would check connectivity for every host in a group named db:

   **$ ansible db -i inventory -m ping**

- You can also specify multiple hosts and groups by separating them with colons:

  **$ ansible server1:server2:dbservers [-i inventory] -m ping**

- To include an exception in a pattern, use an exclamation mark, prefixed by the escape character \, as follows. This command will run on all servers from group1, except server2:

  **$ ansible group1:\!server2 -i inventory -m ping**

- In case you’d like to run a command only on servers that are part of both group1 and group2, for instance, you should use & instead. Don’t forget to prefix it with a \ escape character:

  **$ ansible group1:\&group2 -i inventory -m ping**
 17  
Ansible/02-adhoc-commands/adhoc-commands.md
@@ -0,0 +1,17 @@
## **Examples of Ad-hoc Commands**

$ ansible all -m ping          :  => ping module

$ ansible all -a "cat /etc/passwd"     #=> command module/ default - does not work with |,>,<,&

$ ansible all -a "cat /etc/passwd | tail -3" : => will fail

$ ansible all -m shell -a "cat /etc/passwd | tail -3" :  => shell module

$ ansible db -m copy -a 'src=/source/file/path dest=/dest/location'

$ ansible db -m copy -a 'src=/source/file/path  dest=/dest/location remote_src = yes'

$ ansible all -m debug -a 'var=inventory_hostname' # debug module

$ ansible all -m debug -a 'msg={{inventory_hostname}}'
 65  
Ansible/03-Ansible modules/Ansible-variables.md
@@ -0,0 +1,65 @@
- Variables are used to store values.
- Ansible variable names should be letters, numbers, underscore and they should always start with a letter.

**Types of variables:** \
    - Default variables \
    - Inventory Vars (Host Vars and Group Vars) \
    - Facts and Local facts \
    - Registered variables

**Default variables** \
    - inventory_hostname \
    - inventory_hostname_short \
    - groups/ groups.keys()

  **Debug module**
- Executes only on the local host to display some information- message or variable value. We do not need ssh connectivity or password for a debug module.
- When using the debug module, the arguments will either be
    - msg  =  to display a message
    - var  = to display a variable

  **$ ansible all -m debug -a "var='This is a debug module'"** \
  **$ ansible all -m debug -a "msg={{}}"**

#**inventory_hostname**
- You can use debug to display variables.

$ ansible all -m debug -a "var='inventory_hostname'" \
$ ansible all -m debug -a "msg={{inventory_hostname}}"

- If you have an inventory with entries like 'server1.cloud.production.host' then the command below will return server1.

  **$ ansible all -m debug -a "msg={{inventory_hostname_short}}"**

#**groups/groups.keys()**
- You can list the hosts within a group \
 **$ ansible db --list-hosts**
- To list all groups in the inventory, then we can use the debug module \
  **$ ansible localhost -m debug -a "var=groups"**
- To display only the groups without the server ips, then use keys. \
  **$ ansible localhost -m debug -a "var=groups.keys()"**

#**Host and group variables**
- If were run the ping command on all servers, we will get permission denied on the servers that need a password to authenticate.
- instead of that, we can provide the password in the host file
- This is at a host level or host level variable.
#
       Host variables
       [db]
       172.31.13.31  ansible_ssh_pass=ansible

- You can also connect using a different user by specifying the user in the hosts file.
#
     [db]
     172.31.13.31  ansible_ssh_user=sammy ansible_ssh_pass=abc123

- Create a file on the managed nodes to see which user its working with. \
 **$ ansible all -m file -a "path=test.txt state=touch"**

#**Group variables:**
#
     [db:vars]
     ansible_ssh_user=sammy
     ansible_ssh_pass=abc123

- Host variables have the highest priorities. If variables are defined at a host level, then those variables will have precedence over variables that are defined at a group level.
 105  
Ansible/03-Ansible modules/ReadMe.md
@@ -0,0 +1,105 @@
## **Running Ansible Modules**

- These are small programs that do some work on the server.
- They are the main building blocks of Ansible and are basically reusable scripts that are used by Ansible Ad-hoc and playbooks.

- Ansible comes with a number of reusable modules. To list all available modules \
 **$ ansible-doc -l**

- The basic syntax of an Ad-hoc command is

  **$ ansible [ -i inventory_file ] <server1:server2:Group1:Group2> -m <module> [-a arguments]**

- To execute a module with arguments, include the -a flag followed by the appropriate options in double quotes, like this:

  **ansible target -i inventory -m module -a "module options"**

- As an example, this will use the apt module to install the package tree on server1:

 **$ ansible server1 -i inventory -m apt -a "name=git state=present"**

## **Running Bash Commands**
- When a module is not provided via the -m option, the command module is used by default to execute the specified command on the remote server(s).

- This allows you to execute virtually any command that you could normally execute via an SSH terminal, as long as the connecting user has sufficient permissions and there aren’t any interactive prompts.

- This example executes the uptime command on all servers from the specified inventory:

 **$ ansible all -i inventory -a "uptime"**

## **Using Privilege Escalation to Run Commands with sudo**
- If the command or module you want to execute on remote hosts requires extended system privileges or a different system user, you’ll need to use Ansible’s privilege escalation module, become. This module is an abstraction for sudo as well as other privilege escalation software supported by Ansible on different operating systems.

- For instance, if you wanted to run a tail command to output the latest log messages from Nginx’s error log on a server named server1 from inventory, you would need to include the --become option as follows:

 **$ ansible server1 -i inventory -a "tail /var/log/nginx/error.log" --become**

- This would be the equivalent of running a sudo tail /var/log/nginx/error.log command on the remote host, using the current local system user or the remote user set up within your inventory file.

- Privilege escalation systems such as sudo often require that you confirm your credentials by prompting you to provide your user’s password. That would cause Ansible to fail a command or playbook execution. You can then use the --ask-become-pass or -K option to make Ansible prompt you for that sudo password:

 **$ ansible server1 -i inventory -a "tail /var/log/nginx/error.log" --become -K**

## **Installing and Removing Packages**
The following example uses the apt module to install the nginx package on all nodes from the provided inventory file:

  **$ ansible all -i inventory -m apt -a "name=nginx" --become -K**

To remove a package, include the state argument and set it to absent:.

  **$ ansible all -i inventory -m apt -a "name=nginx state=absent" --become  -K**

## **Copying Files**
- With the copy module, you can copy files between the control node and the managed nodes, in either direction. The following command copies a local text file to all remote hosts in the specified inventory file:

  **$ ansible all -i inventory -m copy -a "src=./file.txt dest=~/myfile.txt"**

- To copy a file from the remote server to your control node, include the remote_src option:

  **$ ansible all -i inventory -m copy -a "src=~/myfile.txt remote_src=yes dest=./file.txt"**

## **Changing File Permissions**
- To modify permissions on files and directories on your remote nodes, you can use the file module.

- The following command will adjust permissions on a file named file.txt located at /var/www on the remote host. It will set the file’s umask to 600, which will enable read and write permissions only for the current file owner. Additionally, it will set the ownership of that file to a user and a group called sammy:

  **$ ansible all -i inventory -m file -a "dest=/var/www/file.txt mode=600 owner=sammy group=sammy" --become  -K**

- Because the file is located in a directory typically owned by root, we might need sudo permissions to modify its properties. That’s why we include the --become and -K options. These will use Ansible’s privilege escalation system to run the command with extended privileges, and it will prompt you to provide the sudo password for the remote user.

## **Restarting Services**
- You can use the service module to manage services running on the remote nodes managed by Ansible. This will require extended system privileges, so make sure your remote user has sudo permissions and you include the --become option to use Ansible’s privilege escalation system. Using -K will prompt you to provide the sudo password for the connecting user.

- To restart the nginx service on all hosts in group called webservers, for instance, you would run:

  **$ ansible webservers -i inventory -m service -a "name=nginx state=restarted" --become  -K**

## **Restarting Servers**
Although Ansible doesn’t have a dedicated module to restart servers, you can issue a bash command that calls the /sbin/reboot command on the remote host.

Restarting the server will require extended system privileges, so make sure your remote user has sudo permissions and you include the --become option to use Ansible’s privilege escalation system. Using -K will prompt you to provide the sudo password for the connecting user.

**Warning:** The following command will fully restart the server(s) targeted by Ansible. That might cause temporary disruption to any applications that rely on those servers.

To restart all servers in a webservers group, for instance, you would run:
  **$ ansible webservers -i inventory -a "/sbin/shutdown"  --become  -K**   \
  **$ ansible webservers -i inventory -a "/sbin/reboot"  --become  -K**

## **Gathering Information About Remote Nodes**
The setup module returns detailed information about the remote systems managed by Ansible, also known as system facts.

To obtain the system facts for server1, run:

  **$ ansible server1 -i inventory -m setup**

This will print a large amount of JSON data containing details about the remote server environment. To print only the most relevant information, include the "gather_subset=min" argument as follows:

 **$ ansible server1 -i inventory -m setup -a "gather_subset=min"**

To print only specific items of the JSON, you can use the filter argument. This will accept a wildcard pattern used to match strings, similar to fnmatch. For example, to obtain information about both the ipv4 and ipv6 network interfaces, you can use *ipv* as filter:

  **$ ansible server1 -i inventory -m setup -a "filter=*ipv*"**

If you’d like to check disk usage, you can run a Bash command calling the df utility, as follows:

  **$ ansible all -i inventory -a "df -h"**
 119  
Ansible/03-Ansible modules/Type_module.md
@@ -0,0 +1,119 @@
## **1. Command module**
- Used to execute binary commands
- It is the **default module**
- With the Command module the command will be executed without being proceeded through a shell.
- Consequently, some variables like $HOME and operations like <, >, | and & will not work.
-The command module is more secure, because it will not be affected by the user’s environment \
   **$ ansible all -i inventory -a "uptime"**

   **$ ansible db -a "cat /etc/hosts && pwd"**

## **2. Shell Module**
- Used to execute binary commands
- It is more superior than the command module
- Command will be executed being proceeded through a shell. \
   **$ ansible db -a "cat /etc/hosts && pwd"**

   **$ ansible db –m shell –a "cat /etc/passwd && pwd"**

## **3. File Module**
- Used to create files and directories \
 a) Create a file named file25 \
  **$ ansible group1 -m file -a "path=file26.txt state = touch"**

 b) Create a directory file25 \
  **$ ansible db -m file -a "path=/home/ansible/file26 state=directory mode=777 owner=root group=root" --become**

- If state=absent, the file or directory will be deleted

## **4. Copy Module**
- Used to copy files from the ansible control node to the remote
node or copy files from one location to another in the remote
node. \
a) From ansible control node to remote node \
  **$ ansible group1 -m copy -a "src=/source/file/path dest=/dest/location"** \
  **$ ansible group1 -m copy -a "src=/etc/hosts dest=/home/ansible"**

 b) From one location in remote node to another location in remote node \
  **$ ansible db -m copy -a “src=/source/file/path dest=/dest/location remote_src=yes"**

  **$ ansible group1 -m copy -a "src=/etc/hosts dest=/home/ansible remote_src=yes"**

 c) Change permission of a file in a remote node \
  **$ ansible db -m file -a "dest=/home/ansible/hosts mode=0664"**

## **5. Fetch Module**
- Used to download files from a remote node to the control machine. \
  **$ ansible db -m fetch -a “src=/source/file/path dest=/dest/location"**

- Will copy the files with a directory structure. \
  **$ ansible all -m fetch -a "src=./hosts dest=./"**

- To fetch the file without a directory structure, use the flat=yes option \
  **$ ansible all -m fetch -a "src=./hosts dest=./ flat=yes"**

## **6. Yum Module**
- Used to install a package in the ansible client. \
  **$ ansible db -m apt -a "name=apache2 state=present" --become**

  **$ ansible db -m service -a "name=apache2 state=started" --become**

  **$ ansible db -m apt -a "name=nginx state=present" -b**

## **7. Service Module**
- You can use the service module to manage services running on the remote nodes managed by Ansible. This will require extended system privileges, so make sure your remote user has sudo permissions and you include the --become option to use Ansible’s privilege escalation system. Using -K will prompt you to provide the sudo password for the connecting user.

  **$ ansible all -i inventory -m service -a "name=nginx state=restarted" --become  -K**

  **$ ansible all -m service -a "name=nginx state=stopped" --become  -K**

## **8. User Module**
 - Used to create user accounts.
 - Create a password encryption

 **$ openssl passwd -crypt <desired_password>**

 **$ ansible db -m user -a "name=Peter password=wiyiMQbLhCRUY shell=/bin/bash" -b**

## **9. Setup module**
- This is a default module and is used to gather facts about the hosts.
- The setup module returns detailed information about the remote systems managed by Ansible, also known as system facts.
- To obtain the system facts for group1, run:

  **$ ansible group1 -m setup**

- This will print a large amount of JSON data containing details about the remote server environment.
- To print only the most relevant information, include the "gather_subset=min" argument as follows:

  **$ ansible group1 -m setup -a "gather_subset=min"**

- To print only specific items of the JSON, you can use the filter argument. This will accept a wildcard pattern used to match strings. For example, to obtain information about both the ipv4 and ipv6 network interfaces, you can use *ipv* as filter:

  **$ ansible group1 -m setup -a "filter=*ipv*"**

## **10. Debug module**
- Executes only on the local host to display some information- message or variable value.
- We do not need ssh connectivity or password for a debug module. When using the debug module, the arguments will either be \
      - msg  =  to display a message \
      - var  = to display a variable

      **Default variables**
          - inventory_hostname
          - inventory_hostname_short
          - groups/ groups.keys()

  **$ ansible all -m debug -a "msg='This is a debug module'"** \
  **$ ansible all -m debug -a "var={{}}"**

 **inventory_hostname**
  - You can use debug to display variables.

  **$ ansible all -m debug -a "var='inventory_hostname'"** \
  **$ ansible all -m debug -a "msg={{inventory_hostname}}"**

  - If you have an inventory with entries like 'server1.cloud.production.host' then the command below will return server1. \
  **$ ansible all -m debug -a "msg={{inventory_hostname_short}}"**

  - You can also use debug module to display groups or groups keys in your inventory. \
  **$ ansible all -m debug -a "var='groups'"** \
  **$ ansible all -m debug -a "var='groups.keys()'"**
