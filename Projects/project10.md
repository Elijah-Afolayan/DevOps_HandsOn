### LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

Task
This project consists of two parts:  

Configure Nginx as a Load Balancer
Register a new domain name and configure secured connection using SSL/TLS certificates.

### CONFIGURE NGINX AS A LOAD BALANCER  

Either uninstall Apache from an existing apache2 Load Balancer server, or create a fresh installation of Linux for Nginx.

1. Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx_LB 

2. Open TCP port 80 for HTTP connections, also open TCP port 443 which is used for secured HTTPS connections. 

![](image/project10_Nginx_LB_SpinUP.png)  

3. Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses
    - sudo nano /etc/hosts
    - update web1 

![](image/project10_Nginx_LB_etc_host.png)  

4. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers.  
(i) Update the instance and Install Nginx  
    - sudo apt update
    - sudo apt upgrade
    - sudo apt install nginx  

![](image/project10_Nginx_LB_UP_host.png)  

5.  Configure Nginx LB using Web Servers’ names defined in /etc/hosts. Open the default nginx configuration file
    - sudo vi /etc/nginx/nginx.conf

```
#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;
```  

- Restart Nginx and make sure the service is up and running
    - sudo systemctl restart nginx
    - sudo systemctl status nginx

![](image/project10_Nginx_LB_UP_verify.png)  
