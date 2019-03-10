# Introduction
A simple vagrant environment setup to run [ansible tutorials](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html#playbooks-best-practices). <br/>

# Prerequisite  
Install [vagrant](https://www.vagrantup.com/downloads.html) <br/>
Install [virtualbox](https://www.virtualbox.org/wiki/Downloads)

# Usage
Run this under the project root directory, it will spin up 4 Linux centos boxes, one master and 3 nodes. The master box is installed with ansible where you can run and test your ansible playbooks
```
vagrant up
``` 

# ansible-example
You can download some of the examples from [here](https://github.com/ansible/ansible-examples) then under the project root directory, run the sample playbook
```
[vagrant@master1 vagrant]$ ansible-playbook ansible/tomcat-standalone/site.yml --become --become-method=sudo --become-user=root
```

# Tips
For macOS users, no virtualbox guest addtion is available so if you wanna sync the directories on your mac host machain to vagrant guest machine, on the project root directory, you may run
```
vagrant rsync-auto
```
