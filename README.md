## CONFIGURE NGINX AS A LOAD BALANCER

- You can either uninstall Apache from the existing Load Balancer server, or create a fresh installation of Linux for Nginx.

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)

- Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

- Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers


### Update the instance and Install Nginx

``
sudo apt update
sudo apt install nginx

``

- Configure Nginx LB using Web Servers’ names defined in /etc/hosts

      `sudo vi /etc/hosts`
      
   
 - Open the default nginx configuration file
      
`      
        #insert following configuration into http section upstream myproject {
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

`

- Restart Nginx and make sure the service is up and running

![nginx actice](https://user-images.githubusercontent.com/65022146/199515350-c96a3f21-f0a7-4daf-a85e-7980276621c2.png)

 

` 
       sudo systemctl restart nginx
       sudo systemctl status nginx
`    



- Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)

- Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP

- Update A record in your registrar to point to Nginx LB using Elastic IP address

![Record Created in the Hosted Zone](https://user-images.githubusercontent.com/65022146/199515806-de5b4df1-9b9b-430f-b499-8d979efd551d.png)


- Configure Nginx to recognize your new domain name 

- Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com

- Install certbot and request for an SSL/TLS certificate


    `sudo systemctl status snapd`
    
- Make sure snapd service is active and running

     `sudo systemctl status snapd`
   
  ![Snapd active and running](https://user-images.githubusercontent.com/65022146/199519039-1f2dd82a-ec8d-4668-8594-979e96c4f61d.png)
  
  
  - Install certbot
  
    `sudo snap install --classic certbot`
   
- Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on step 4)

 `     
      sudo ln -s /snap/bin/certbot /usr/bin/certbot
        sudo certbot --nginx
  `
  
  ![Certificate issued 2](https://user-images.githubusercontent.com/65022146/199521542-14624f38-3b49-4d92-a6c1-72ca6ec5a7b7.png)
  
  
  - Test secured access to your Web Solution by trying to reach https://<your-domain-name.com>
  
  ![without certificate2](https://user-images.githubusercontent.com/65022146/199522737-5649fdd3-5c1b-48ce-8018-288091bea079.png)
  

- You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s search string.
Click on the padlock icon and you can see the details of the certificate issued for your website.

![Domain certificate details](https://user-images.githubusercontent.com/65022146/199523086-f898867b-eca0-43d5-91bf-d680decce22f.png)

- Set up periodical renewal of your SSL/TLS certificate By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

- You can test renewal command in dry-run mode

  `sudo certbot renew --dry-run`
  
- Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

To do so, lets edit the crontab file with the following command:

    `crontab -e`
    
- Add following line:

    `* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`
    
- Note that Add following line you can always change the interval of this cronjob if twice a day is too often by adjusting schedule expression.


## END OF THE PROJECT-TEN IMPLEMENTATION

      






  

     
    


