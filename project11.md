## ANSIBLE_AUTOMATE_PROJECT_7_TO_10

### Installing Ansible on Jenkins Server

I installed ansible on the  jenkins server and rename it to Jenkins-Ansible

sudo apt update

sudo apt install ansible

![jenkinsansible](https://github.com/Oolabanji/test_/assets/136812420/0777b5bb-5e8e-4349-983a-998b2e4423e8)



I created a new repository called ansible-config-mgt on github and set up webhooks on it.

https://<3.144.118.34:8080/github-webhooks>

On the Jenkins server, I created a job called ansible and configure automatic builds when a trigger is made on the ansible-config-mgt directory via GITScm polling.

![jenkinswebhook](https://github.com/Oolabanji/test_/assets/136812420/6f06c34b-bcab-45fe-ad4d-8071fe12604a)

I tested configuration by updating a README file on github.

![console output](https://github.com/Oolabanji/test_/assets/136812420/7c0c7008-d109-4d30-8550-3992338f4946)


### Prepare Development using VSCode



### Ansible Configuration

I clone ansible-config-mgt repo on local machine and created a new branch for development

-Created a playbooks directory for storing playbooks
-Created an inventory directory for storing inventory files
-In the playbooks folder, created a common.yml file
-In the inventory folder, created dev.yml, prod.yml, staging.yml and uat.yml for dev, prod, staging and uat environments respectively.

![playbook1](https://github.com/Oolabanji/test_/assets/136812420/797e3ded-6e36-40b5-ae58-43c1918f8369)

### Setting Up Inventory

I  created inventories to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. 

I need to ssh into the target servers defined in the /inventory/dev.yaml

using SSH-Agent to upload the ssh public key to the jenkins-ansible server

eval `ssh-agent -s`

ssh-add C:\Users\BUKOLA\Downloads\benjy.pem

I confirmed the key has been added with the command below

ssh-add -l

Now, I ssh into my Jenkins-Ansible server using ssh-agent

ssh -A ubuntu@public-ip

updating our /inventory/dev.yaml

[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'

### Creating a Common Playbook

I updated code in /playbooks/common.yaml

 ---
- name: update web, nfs and db servers

  hosts: webservers, nfs, db

  remote_user: ec2-user

  become: yes

  become_user: root

  tasks:

    - name: ensure wireshark is at the latest version

      yum:

        name: wireshark

        state: latest

- name: update LB server

  hosts: lb

  remote_user: ubuntu

  become: yes

  become_user: root

  tasks:

    - name: Update apt repo

      apt: 

        update_cache: yes


    - name: ensure wireshark is at the latest version

      apt:

        name: wireshark

        state: latest

        

![playbook3](https://github.com/Oolabanji/test_/assets/136812420/14f4139f-7ab8-41f3-b9ae-0dfb2e1c3926)



Next I push code into repository and create a pull request to the main branch. Jenkins checksout the code and builds an artifact that is published on the ansible server.


![jenkins2](https://github.com/Oolabanji/test_/assets/136812420/d15c0fcd-751c-4bb2-9ad9-60403744b5f6)

### RUN FIRST ANSIBLE TEST

ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/10/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/10/archive/playbooks/common.yml


![build1](https://github.com/Oolabanji/test_/assets/136812420/79d402c8-a0ce-4d6b-8f19-4ad39b5ef265)

![build2](https://github.com/Oolabanji/test_/assets/136812420/e631b82b-b3cb-4446-8731-ff9ca6ba7ac7)




