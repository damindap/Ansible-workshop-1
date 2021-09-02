# Hands-on Ansible-01 : Using Playbook with Tasks

Purpose of the this hands-on training is to give students the knowledge of basic Ansible skills.

## Learning Outcomes

At the end of the this hands-on training, students will be able to;

- Learn ansible playbooks.

## Outline

- Part 1 - Install ansible

- Part 2 - Ansible Playbooks

## Part 1 - Install Ansible


- Create 3 Amazon Linux 2 EC2 instances. Then name the instances as below. For Security Group, allow ssh and http when creating EC2.
1-control-node
2-node1
3-node2

- Connect to the control node with SSH and run the following commands.

```bash
sudo yum update -y
sudo amazon-linux-extras install ansible2
```
### Confirm Installation

- To confirm the successful installation of Ansible. You can run the following command.

```bash
$ ansible --version
```
### Configure Ansible on AWS EC2

- Connect to the control node and for this basic inventory, edit /etc/ansible/hosts, and add a few remote systems (manage nodes) to the end of the file. For this example, use the IP addresses of the servers.

```bash
$ sudo su
$ cd /etc/ansible
$ ls
$ vim hosts

[webserver]
node1 ansible_host=<node1_ip> ansible_user=ec2-user

[dbserver]
node2 ansible_host=<node1_ip> ansible_user=ec2-user

[all:vars]
ansible_ssh_private_key_file=/home/ec2-user/<pem file>


```

- Edit /etc/ansible/ansible.cfg as adding below. 

```bash
$ vim ansible.cfg
[defaults]
interpreter_python=auto_silent
```

- Copy your pem file to the /etc/ansible/ directory. First go to your pem file directory on your local computer and run the following command.

```bash
$ scp -i <pem file> <pem file> ec2-user@<public DNS name of the control node>:/home/ec2-user
```

- or you can create a file name <pem file> into the directory /etc/ansible on the control node and copy your pem file into it.

## Part 2 - Ansible Playbooks

- Create a yaml file named "playbook1.yml" and make sure all our hosts are up and running.

```bash
---
- name: Test Connectivity
  hosts: all
  tasks:
   - name: Ping test
     ping:
```

- Run the yaml file.

```bash
ansible-playbook playbook1.yml
```

- Create a text file named "testfile1" and write "Hello Clarusway" with using vim. Then create a yaml file name "playbook2.yml" and send the "testfile1" to the hosts. 

```yml
---
- name: Copy for webserver
  hosts: all
  tasks:
   - name: Copy your file to the webservers
     copy:
       src: /home/ec2-user/testfile1
       dest: /home/ec2-user/testfile1

```

- Run the yaml file.

```bash
ansible-playbook playbook2.yml
```

- Connect the nodes with SSH and check if the text files are copied or not. 

- Create a yaml file named playbook3.yml as below.

```bash
$ vim playbook3.yml

```yml
- name: Copy for webserver
  hosts: webserver
  tasks:
   - name: Copy your file to the webservers
     copy:
       src: /home/ec2-user/testfile1
       dest: /home/ec2-user/testfile1
       mode: u+rw,g-wx,o-rwx


- name: Copy for node2
  hosts: node2
  tasks:
   - name: Copy using inline content
     copy:
       content: '# This file was moved to /etc/ansible/testfile2'
       dest: /home/ec2-user/testfile2

   - name: Create a new text file
     shell: "echo Hello World > /home/ec2-user/testfile3"
```

- Run the yaml file.

```bash
ansible-playbook playbook3.yml
```
- Connect the node1 with SSH and check if the text files are there.


