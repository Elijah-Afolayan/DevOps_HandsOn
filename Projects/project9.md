Project9

Task  
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

## INSTALL AND CONFIGURE JENKINS SERVER  
Step 1 – Install Jenkins server  

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins" and also open port 8080.

2.  Install JDK since Jenkins is a Java-based application  
    - sudo apt update  
    - sudo apt install default-jdk-headless  

3.  Install Jenkins  
    - wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add
    - sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \ /etc/apt/sources.list.d/jenkins.list'
    - sudo apt update
    - sudo apt-get install jenkins  

4.  Make sure Jenkins is up and running 
    - sudo systemctl status jenkins

5.  Perform initial Jenkins setup.  
    - access http://\<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080  
    - sudo cat /var/lib/jenkins/secrets/initialAdminPassword

### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

1. Enable webhooks in your GitHub repository settings

2. Go to Jenkins web console, click "New Item" and create a "Freestyle project"

3. To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself  

4. In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.