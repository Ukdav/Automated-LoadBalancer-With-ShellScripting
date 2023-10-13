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









