# Linux Server Configuration
The Linux Server Configuration project is a requirement to finish Udacity's Full Stack Nanodegree program. The goal of the project is to take a baseline installion of a Linux server and prepare it to host web applications. I need to secure the server from a number of attack vectors, install and configure a database server, and deploy one of my exisiting web applications onto it.

## Step by step walkthrough
### Get your server
1. Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
2. Follow the instructions provided to SSH into the server.

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
13. Clone and setup your Item Catalog project from the Github repo you created earlier in this Nanodegree program.
14. Set it up in your server so that it functions correctly when visiting your server's IP address in a browser. Make sure that your ```.git``` directory is not publicly accessible via a browser.
