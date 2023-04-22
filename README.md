# Load-Balancing-Solution-With-Nginx
This project consists of two parts:

Configure Nginx as a Load Balancer
Register a new domain name and configure secured connection using SSL/TLS certificates
Your target architecture will look like this:

![image](https://user-images.githubusercontent.com/102925329/233776158-540cfe6c-78ca-4fdf-adc5-d7b9ee57dbbb.png)

### CONFIGURE NGINX AS A LOAD BALANCER
To create a fresh installation of Linux for Nginx

Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)

Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers


<img width="555" alt="image" src="https://user-images.githubusercontent.com/102925329/233776877-79e31391-fb59-4edf-b71a-7981c753c1b6.png">

Update the instance and Install Nginx

    sudo apt update
    sudo apt install nginx
    
Update /etc/hosts file for local DNS with Web Servers names (e.g. Web1 and Web2) and their local IP addresses.

<img width="468" alt="image" src="https://user-images.githubusercontent.com/102925329/233777264-3ea491fc-8d16-4641-9418-559c3da4dca4.png">

Configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

    sudo vi /etc/nginx/nginx.conf

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

<img width="636" alt="image" src="https://user-images.githubusercontent.com/102925329/233777663-b0b896f6-010b-41db-ab42-dd907327df7d.png">

Restart Nginx and verify server status.

    sudo systemctl restart nginx
    sudo systemctl status nginx
    

