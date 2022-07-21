### ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

### Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

### Step 1 – Jenkins job enhancement
1. Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact where all artifacts will be stored after each build.
    - sudo mkdir /home/ubuntu/ansible-config-artifact

2. Change permissions to this directory, so Jenkins could save files there 
    – chmod -R 0777 /home/ubuntu/ansible-config-artifact

3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins

4. Create a new Freestyle project (you have done it in Project 9) and name it save_artifacts.

5. This project will be triggered by completion of your existing ansible project. Configure it accordingly


6. The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. 
    - To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

7. Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch)
    - sudo nano /home/ubuntu/ansible-config-mgt/README.md
    - git add .
    - git commit -a
    - git push origin main

8. If both Jenkins jobs have completed (ansible) job triggering  another(safe_artifacts). Then you will see ansisble Job artifacts inside Jenkins-ansible server path: /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your master branch.  
    - cat /home/ubuntu/ansible-config-artifact/README.md

### REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML

Step 2 – Refactor Ansible code by importing other playbooks into site.yml  

1. Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it refactor.
    - git checkout main
    - git pull 
    - git branch refactor

2. Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored.  
    - mkdir /home/ubuntu/ansible-config-mgt/static-assignments

3. Move common.yml file into the newly created static-assignments folder.
    - mv /home/ubuntu/ansible-config-mgt/playbook/common.yml /home/ubuntu/ansible-config-mgt/

4. Inside site.yml file, import common.yml playbook.
    - nano /home/ubuntu/ansible-config-mgt/playbook/site.yml
    - insert bellow code
```
    - hosts: all
    - import_playbook: ../static-assignments/common.yml
```  

5. Run ansible-playbook command against the dev environment. Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.
    - touch /home/ubuntu/ansible-config-mgt/static-assignments/common-del.yml
    - nano /home/ubuntu/ansible-config-mgt/static-assignments/common-del.yml

6. Update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:  
    - nano /home/ubuntu/ansible-config-mgt/playbook/site.yml  
    - insert bellow code  
````
       - import_playbook: ../static-assignments/common-del.yml  
````      
    - cd /home/ubuntu/ansible-config-mgt/
    - ansible-playbook -i inventory/dev.yml playbooks/site.yaml



### CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER  

Step 3 – Configure UAT Webservers with a role ‘Webserver’
1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT  

2. To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.    
    - Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)
        - mkdir roles
        - cd roles
        - ansible-galaxy init webserver  

3. Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers
    - nano /home/ubuntu/ansible-config-mgt/inventory/uat.yml

4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.
    - /etc/ansible/ansible.cfg
    - roles_path    = /home/ubuntu/ansible-config-mgt/roles

5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

Install and configure Apache (httpd service)
Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
Make sure httpd service is started

### REFERENCE WEBSERVER ROLE  

Step 4 – Reference ‘Webserver’ role  
Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.



The entry point to our ansible configuration is the site.yml file. Therefore, reference the file uat-webservers.yml role inside site.yml.

So, we should have this in site.yml

````
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml

````

Step 5 – Commit & Test  
1. Commit changes in the local branch refactor. 

2. Then create a Pull Request and merge them to master branch.  

3. Make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.
    - Now run the playbook against your uat inventory and see what happens:
