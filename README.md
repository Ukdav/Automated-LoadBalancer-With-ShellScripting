# Automated-LoadBalancer-With-ShellScripting

**Introduction:** 

Automated Load Balancing with Shell Script

In today's digital landscape, the ability to efficiently distribute network traffic and system workloads is a crucial component of maintaining high availability and optimal performance for web services and applications. Load balancing is the practice of evenly distributing incoming requests across multiple servers, ensuring that no single server becomes overwhelmed. While there are various sophisticated load balancing solutions available, the power and flexibility of shell scripting can be harnessed to create custom, automated load balancing systems.

This introduction explores the concept of automated load balancing with shell scripts, shedding light on the importance of load balancing, the benefits of using shell scripting, and the potential it holds for creating dynamic, adaptable load balancing solutions. By the end of this exploration, you will have a foundational understanding of how to leverage shell scripting to build and maintain a robust load balancing system tailored to your specific needs.

**Automate the Deploymeent of webservers**

In the implementing load balancer with Nginx, we deployed two backend servers, with a load balancer distributing traffic across the webservers. we did that by typing commands righ on our terminal.

In this course we will be automating the entire process. we will do that by writing a shell ssssscript that when ran, all that will did manually will be done automatically. As a DevOps Engineer automation is at the heart of the work we do. Automation helps us speedup the deployemnt of service and resuce the chance of making erroes in our day to day activity.

This course will give a great introduction, all the process we need to deploy our webservers has been codified in the shell script.

copy the below code and paste in in you Vi editor of Nano Editor:

#!/bin/bash

####################################################################################################################
##### This automates the installation and configuring of apache webserver to listen on port 8000
##### Usage: Call the script and pass in the Public_IP of your EC2 instance as the first argument as shown below:
######## ./install_configure_apache.sh 127.0.0.1
####################################################################################################################

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure

PUBLIC_IP=$1

[ -z "${PUBLIC_IP}" ] && echo "Please pass the public IP of your EC2 instance as an argument to the script" && exit 1

sudo apt update -y &&  sudo apt install apache2 -y

sudo systemctl status apache2

if [[ $? -eq 0 ]]; then
    sudo chmod 777 /etc/apache2/ports.conf
    echo "Listen 8000" >> /etc/apache2/ports.conf
    sudo chmod 777 -R /etc/apache2/

    sudo sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8000>/' /etc/apache2/sites-available/000-default.conf

fi
sudo chmod 777 -R /var/www/
echo "<!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: "${PUBLIC_IP}"</p>
        </body>
        </html>" > /var/www/html/index.html

sudo systemctl restart apache2

Following the steps below to run the script:

Step 1: Provsion an EC2 instance running ubuntu 20.4

Step 2: OPen port 8000 to allow traffic from anywhere using the security group

Step 3: connect to the webserver via the terminal using SSH Client

Step 4: Open a file, paste the scrip above and close the file using the command below:

*sudo vi install.sh*

Step 5: Change the permission on the file to make an executable using the command below:

*sudo chmod +x install.sh*

Step 6: Run the shell script using the command below. Make sure you read the instructions in the she script to learn how to use it

*/install.sh PUBLIC_IP*

![run script 1](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/9cacae6d-b7ae-4d13-9627-513841c47a4e)

![webserver2](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/9840b6cc-5a0b-45c5-aa17-5fe7299a0a38)

![VI editor script](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/704b3846-979f-426f-a56d-e8f13bfea509)

![vi editor for webserver2](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/ba1efdeb-8fb1-48d7-a2c0-c5fe5d3024ed)

copy the Public IP address of the two server and place it on your browser, you will get the following display:

![webserver1 on browser](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/74d90942-3f7a-4938-93ed-a30ab633f325)

![webserver2 on browser](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/8366009a-552c-4f65-bc56-460c527ffdca)

**Deploying of Nginx as a Load Balancer using Shell Script**

Automate the deployment of Nghinx as a Load Balancer using shell script

Having successfully deployed and configures two webservers, we will move on the load balancer. As a prerequisite, we need to provision an EC2 instance running 22.04 , open port 80 to anywhere using the security group to connect to the load balancer via the terminal.

All the steps followed in the implementating load balancer with Nginx course has been in the script below:

Read the instructions carefully in the script to learn how to use the script, copy the code below and paste it on your Vi editor:

#!/bin/bash

######################################################################################################################
##### This automates the configuration of Nginx to act as a load balancer
##### Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
##### the webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
##### ./configure_nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
#####  ./configure_nginx_loadbalancer.sh 127.0.0.1 192.2.4.6:8000  192.32.5.8:8000
############################################################################################################# 

PUBLIC_IP=$1
firstWebserver=$2
secondWebserver=$3

[ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

[ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

[ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure


sudo apt update -y && sudo apt install nginx -y
sudo systemctl status nginx

if [[ $? -eq 0 ]]; then
    sudo touch /etc/nginx/conf.d/loadbalancer.conf

    sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
    sudo chmod 777 -R /etc/nginx/

    
    echo " upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server  "${firstWebserver}"; # public IP and port for webserser 1
            server "${secondWebserver}"; # public IP and port for webserver 2

            }

           server {
            listen 80;
            server_name "${PUBLIC_IP}";

            location / {
                proxy_pass http://backend_servers;   
            }
    } " > /etc/nginx/conf.d/loadbalancer.conf
fi

sudo nginx -t

sudo systemctl restart nginx

**Steps to run the shell script**

**Step 1:** On your terminal, open a file nginx.sh using the command below:

*sudo vi nginx.sh*

**Step 2:** Copy and paste the script inside the file

**Step 3:** Change the file permission to make it and executable using the command belwo:

*sudo chmod +x nginx.sh*

**Step 4:** Run the script with teh command below:

*./nginx.sh PUBLIC_IP Webserver-1 Webserver-2*

![Script instal New](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/863d4f71-3fea-48a5-8c0a-2a944dbc3f7a)

![edited scipt bash](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/7214f0ed-3f91-4921-bd3d-be1caa963d93)

**Verifying the setup**

![browser 143](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/df36d3c6-31ba-41de-902c-f7c84671d6f3)

![browser 171](https://github.com/Ukdav/Automated-LoadBalancer-With-ShellScripting/assets/139593350/bf4318ee-5de0-4249-8f76-b6f09ed06026)

Automated load balancing with shell scripting exemplifies the synergy between the power of traditional system administration and the demands of today's dynamic, data-driven world. As we conclude this exploration, it is clear that the versatility, efficiency, and precision offered by shell scripting in the realm of load balancing is an invaluable asset for professionals striving to optimize their infrastructure. By embracing this approach, organizations can proactively manage and distribute their workloads, ensuring the seamless operation of their services, even in the face of surging demand and unexpected challenges. In the ever-evolving field of information technology, the capability to adapt and innovate is paramount, and automated load balancing with shell scripting stands as a testament to the ingenuity and resourcefulness of those who seek to harness its potential.

In an era where every microsecond counts and uninterrupted access is non-negotiable, the art of automated load balancing with shell scripts is a potent tool at the disposal of IT professionals. Its promise lies not only in the scripts themselves but in the creative solutions they enable. As the landscape continues to shift and evolve, embracing this approach will undoubtedly prove to be a cornerstone of success in the realm of network infrastructure and server management.




















