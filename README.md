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
    

## REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES

1. Register a new domain name with any registrar

2. Assign an Elastic IP to Nginx LB server and associated domain name with the Elastic IP.

- Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

- In the navigation pane, choose Elastic IPs.

- Select the Elastic IP address to associate and choose Actions, Associate Elastic IP address.

- For Resource type, choose Instance.

For instance, choose the instance with which to associate the Elastic IP address. You can also enter text to search for a specific instance.

(Optional) For Private IP address, specify a private IP address with which to associate the Elastic IP address.

Choose Associate.

<img width="812" alt="image" src="https://user-images.githubusercontent.com/102925329/233778126-6d8e8855-3d33-4e2f-9b71-e9cb2f07a0ff.png">

3. Update A record in your registrar to point to Nginx LB using Elastic IP address

4. Configure Nginx to recognize the new domain name. This was done by Updating the /etc/nginx/nginx.conf file with

    Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com

5. Install certbot and request for an SSL/TLS certificate

    Make sure snapd service is active and running
    
   <img width="654" alt="image" src="https://user-images.githubusercontent.com/102925329/233778342-6a1ac876-310e-49eb-8aef-f6195f2965f1.png">

        sudo snap install --classic certbot
        
6. Make a Request your certificate for the domain name.

        sudo ln -s /snap/bin/certbot /usr/bin/certbot
        sudo certbot --nginx
        
Follow the instruction displayed.

Lets Encrypt renews every 90 days and you can renew your certificate manually by running the following command.

         sudo certbot renew --dry-run
         
We can also create a cron job to do this same thing at a stipulated time.

Edit cron file

        crontab -e
Add the following line to the crontab file

    5 */12 * */2 *   root /usr/bin/certbot renew > /dev/null 2>&1
Save the crontab file
