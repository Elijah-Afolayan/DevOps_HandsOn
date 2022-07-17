## Ansible Client as a Jump Server (Bastion Host)

## Task  
1. Install and configure Ansible client to act as a Jump Server/Bastion Host  
2. Create a simple Ansible playbook to automate servers configuration

### STEP1 INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.  

2. In your GitHub account create a new repository and name it ansible-config-mgt.

3. Instal Ansible on the Jenkins-Ansible EC2 instance 
    - sudo apt update
    - sudo apt install ansible  

3. Check the  Ansible version installed on the EC2 instance 
    - run ansible --version

4. Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.  

    - Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
    - Configure Webhook in GitHub and set webhook to trigger ansible build.
    - Configure a Post-build job to save all (**) files, like you did it in Project 9.  

5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder
    - ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/


### Step 2 – Prepare your development environment using Visual Studio Code

1. First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor.

There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs

    - sudo snap install --classic code  
                or  

    1. Update the packages index and install the dependencies by running the following command as a user with sudo privileges :  
        - sudo apt update
        - sudo apt install software-properties-common apt-transport-https wget  

    2. import the Microsoft GPG key using the following wget command :
        - wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -  

    3. Then enable the Visual Studio Code repository by typing: 
        - sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"  

    4. Once the apt repository is enabled , install the Visual Studio Code package: 
        - sudo apt install code

        - sudo apt install code
    5. When a new version is released you can update the Visual Studio Code package through your desktop standard Software Update tool or by running the following commands in your terminal:
        - sudo apt update
        - sudo apt upgrade
        

2. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.  

3. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
    - git clone \<ansible-config-mgt repo link>


BEGIN ANSIBLE DEVELOPMENT

1. In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature  

2. Checkout the newly created feature branch to your local machine and start building your code and directory structure
3. Create a directory and name it playbooks – it will be used to store all your playbook files.  
4. Create a directory and name it inventory – it will be used to keep your hosts organised.  
5. Within the playbooks folder, create your first playbook, and name it common.yml  
6. Within the inventory folder, create an inventory file (.yml) for each environment  (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.  


### Step 4 – Set up an Ansible Inventory  
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

1. Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.
    - nano inventory/dev.yml  
2. Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:
    - eval `ssh-agent -s`
    - ssh-add \<path-to-private-key>
    - ssh-add -l (Confirm the key has been added) 

3. Now, ssh into your Jenkins-Ansible server using ssh-agent
    - ssh -A ubuntu@public-ip

4. Update your inventory/dev.yml file with this snippet of code:
````
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
````
CREATE A COMMON PLAYBOOK
### Step 5 – Create a Common Playbook
1. Giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.yml

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

2. Update your playbooks/common.yml file with following code:  

````
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

        ````  

3. Feel free to update this playbook with following tasks:  
    - Create a directory and a file inside it
    - Change timezone on all servers
    - Run some shell script  

### Step6 Update GIT with the latest code
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".  

2. Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

3. Commit your code into GitHub: use git commands to add, commit and push your branch to GitHub.
    - git status
    - git add <selected files>
    - git commit -m "commit message"  

4. Create a Pull request (PR)

5. Wear a hat of another developer for a second, and act as a reviewer. If the reviewer is happy with your new feature development, merge the code to the master branch.

6. Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

7. Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.


