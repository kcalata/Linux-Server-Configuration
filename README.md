# Linux Server Configuration
The Linux Server Configuration project is a requirement to finish Udacity's Full Stack Nanodegree program. The goal of the project is to take a baseline installion of a Linux server and prepare it to host web applications. I need to secure the server from a number of attack vectors, install and configure a database server, and deploy one of my exisiting web applications onto it. This page wil explain how to do all of this. Part of the instructions come from Udacity.

You can visit 34.229.188.86

## Step by step walkthrough
### Get your server
1. Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
* Log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.
* Create an instance
* Pick a plain Ubunti Linux image after choosing "OS Only". I chose Ubuntu 16.04 LTS.
* Choose your instance plan. Picking the lowest one to get free-tier access for a month.
* Give your instance a hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it.
* Wait for it to start up.
  * While your instance is starting up, Lightsail shows you a grayed-out display.
  * Once your instance is running, the display gets brighter.
* The public IP address of the instance is displayed along with its name.
2. Follow the instructions provided to SSH into the server.
* From the ```Account``` Menu on Amazon Lightsail, click on the ```SSH keys``` tab and download the Default Private Key.
* Move the file ```LightsailDefaultKey-*.pem```(the * represents where the server is located) into the local folder ```~/.ssh``` and rename it ```udacity_key.rsa```.
* In your terminal, run the following command: ```chmod 600 ~/.ssh/udacity_key.rsa```.
* To connect to the server, run the following command in your terminal, ```ssh -i ~/.ssh/udacity_key.rsa ubuntu@34.229.188.86```, where ```34.229.188.86``` is the public IP address of the server.

### Secure your server
3. Update all currently installed packages.
4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

### Give grader access
6. Create a new user account named ```grader```.
7. Give ```grader``` the permision to ```sudo```.
8. Create an SSH key pair for ```grader``` using the ```ssh-keygen``` tool.

### Prepare to deploy your project
9. Configure the local timezone to UTC.
10. Install and configure Apache to server a Python mod_wsgi application.
11. Install and configure PostgreSQL.
12. Install ```git```.

### Deploy the Item Catalog project.
13. Clone and setup my Item Catalog project.
14. Set it up in your server so that it functions correctly when visiting your server's IP address in a browser. Make sure that your ```.git``` directory is not publicly accessible via a browser.
