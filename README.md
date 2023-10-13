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





