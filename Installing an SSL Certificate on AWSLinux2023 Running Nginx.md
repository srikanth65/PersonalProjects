**Installing an SSL Certificate on AWSLinux2023 Running Nginx**

**Goal**:

The goal of this project is to install a valid SSL Certificate on AWS Linux2023 server running the Nginx web server.

We will be using Let’s Encrypt to obtain a certificate for our domain. Let’s Encrypt is an open-source Certificate Authority (CA) that issues SSL certificates for free.

**Background and Prerequisites:**

This tutorial assumes you are using an AWS Linux 2023 system on the public Internet with a valid DNS A or CNAME record. An A record simply maps a domain name to the IP address of the device hosting that domain. A CNAME, which stands for Canonical Name, is an alias for another domain.

In order to install an SSL certificate, you must have a Web Server installed on your system. In this tutorial, we will install Nginx as our Web Server

**NOTE**: This tutorial demonstrates the installation of an SSL certificate for the demo.aws2online.com domain. Even though this domain will be used throughout this tutorial, you must use your own domain when following along

domain used:  aws2day.online

record created as :  demo.aws2day.online


**Instructions**:

Launched an EC2 instance with AWS Linux 2023 AMI

**Step 1**: Connect to the Server as Root Many of the commands you will be executing will require root privileges. Connect to your Linux server as the root user. If you log with another account, switch to the root account. You can switch to the root account with the "su" command:

su -  or sudo su 

**Step 2**: Install and Configure the Nginx Web Server

The first step is to ensure that the system is up to date. Run the following command to install the latest updates:

dnf update -y

Now, install the Nginx Web Server:

dnf install -y nginx

Next, you need to replace a line in the /etc/nginx/nginx.conf file. Open it with your favorite editor.

vi /etc/nginx/nginx.conf

Find the line that reads:
server_name _;

Change "_" to your domain name. Make sure to include the semicolon (;) after your domain at the end of the line:

server_name demo.aws2day.online;

Check for any syntax errors or typing mistakes with this command:

nginx -t

If you get a message such as "test failed", fix your edits in the

/etc/nginx/nginx.conf file and try again.

You want to ensure that the web server starts on boot, so you need to enable it. Also, you will want to start it now, so you can use the following command to achieve both of those steps.

systemctl enable --now nginx

You can verify the web server started by checking its status.

systemctl status nginx

q  # to exit 

You can also use the "is-active" option to "systemctl" to see if it is running.

systemctl is-active nginx

**Step 3**: Allow Inbound HTTP and HTTPS Traffic

**Step 4**: Test the Web Server

Open up a web browser and connect to your domain name. In this example, I am using http://demo.aws2day.online, but use your domain.

If you type your domain name with https, for example

https://demo.linuxtrainingacademy.com , you will see that the web page will not open. This is because Nginx is only configured to allow HTTP traffic by default.

**Step 5**: 

sudo python3 -m venv /opt/certbot/

When it finished processing nothing prompts (probably several seconds), you will see a new line for your next command. Then run this command.

sudo /opt/certbot/bin/pip install --upgrade pip

The run the following command.

sudo /opt/certbot/bin/pip install certbot certbot-nginx


Next you just need to run this command.

sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot

And now the certbot is successfully installed on your server. Make sure you are able to open your website using http://your.app.domain.name And domain pointing is correctly configured.

When you’ve have your website http version configured successfully you just need to run the following command. And your https server will be setup with certbot for you.

sudo certbot --nginx


You will be asked two questions, simply reply with Y . And when you see something like this your SSL certificate (with letsencrypt) is setup successfully.

Try to see if https://your.app.domain.name is working or not. If not, you might need restart your nginx using the following command.

sudo systemctl reload nginx

Also make sure your network firewall has a rule for HTTPS traffic, namely the TCP port 443 should be open.

