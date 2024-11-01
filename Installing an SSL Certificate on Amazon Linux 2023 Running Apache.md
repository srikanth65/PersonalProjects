**Installing an SSL Certificate on Amazon Linux 2023 Running Apache**

**Goal**:
The goal of this project is to install a valid SSL Certificate on Amazon Linux 2023 server running the Apache  web server.

We will be using Let’s Encrypt to obtain a certificate for our domain. Let’s Encrypt is an open-source Certificate Authority (CA) that issues SSL certificates for free.

**Background and Prerequisites:**
This tutorial assumes you are using an Amazon Linux 2023 system on the public Internet with a valid DNS A or CNAME record. An A record simply maps a domain name to the IP address of the device hosting that domain. A CNAME, which stands for Canonical Name, is an alias for another domain.

In order to install an SSL certificate, you must have a Web Server installed on your system.nwe will install Apache as our Web Server

**NOTE**: This demonstrates the installation of an SSL certificate for the demo.aws2online.com domain. 

domain used:  aws2day.online

record created as :  demo.aws2day.online
Security Network Firewall: HTTP/HTTPS traffic, namely the TCP port 80/443 should be open


**Instructions:**
Launched an EC2 instance with Amazon Linux 2023

**Step 1**:

Connect to the Server as Root Many of the commands you will be executing will require root privileges. Connect to your Linux server as the root user. If you log with another account, switch to the root account. You can switch to the root account with the "su" command:

su -  or sudo su 

**Step 2: **

Install Apache

Start off by installing the Apache HTTP Server. You'll also need to install "mod_ssl" to add SSL support to Apache.

yum install -y httpd mod_ssl

Start and Enable the Web Server: 

systemctl start httpd
systemctl enable httpd

You can verify the web server started by checking its status.

systemctl status httpd

You can also use the is-active option to systemctl .

systemctl is-active httpd

Create a Sample Web Page

Create an index.html file in the DocumentRoot of the web server.

echo demo > /var/www/html/index.html
 

You can also use the "is-active" option to "systemctl" to see if it is running.

systemctl is-active nginx

**Step 3: Allow Inbound HTTP and HTTPS Traffic**


**Step 4: Test the Web Server**

Open up a web browser and connect to your domain name. In this example, I am using http://demo.aws2day.online, but use your domain.

If you type your domain name with https, for example

https://demo.aws2day.online , you will see that the web page will not open. 

You will get an error or warning from the web browser because the server is using a self-signed SSL certificate. That certificate was created by post installation script from the mod_ssl package.

You can also check the web server from the command line using the curl utility:

curl http://demo.aws2day.online

curl https://demo.aws2day.online

Curl will also generate an error due to the self-signed SSL certificate. You can use the -k option to force curl to ignore the invalid SSL cert.

curl -k https://demo.aws2day.online

**Install the Certbot Application**
You're going to use the Certbot application to generate an SSL certificate. It's not part of the baseLinux distribution, but it is available in the EPEL repository. EPEL stands for Extra Packages for Enterprise Linux and it's a Fedora project that builds and maintains quality 3rd party packages for
RHEL based distributions such as CentOS. To add the EPEL repository to your system, simply runthe following command.

yum install -y epel-release

Now that you've added the EPEL repository, install the Certbot application.

yum install -y certbot

By the way, if you are unsure of the package name, you can also search for it with yum .

yum search certbot

If you're still not sure which package is the right one, you can get more detailed information with the

yum info command.

yum info certbot

Install the Apache Certbot Plugin

The Certbot application has a few different plugins that allow it to automatically update theconfiguration for the web server you are using. Since we are using Apache, we'll install the Apache Certbot plugin.


yum install -y python2-certbot-apache

(NOTE: If you are using NGINX, you would install the NGINX plugin which is provided by the python2-certbot-nginx package.)

Request an SSL Certificate from Let's Encrypt
To request the initial SSL Certificate execute the certbot command. If you run the command without any options and you will be prompted for all of the required information. Because we already know that we're using the Apache web server we can specify that on the command line with the " --apache " option. You can also specify your domain with the " -d " option followed by your domain. (Remember, to use YOUR domain name, not demo.aws2day.online.)

certbot --apache -d demo.aws2day.online

If you want to force all traffic to HTTPS, be sure to choose the "Secure" HTTPS access option when prompted. If you want to allow both HTTP and HTTPS traffic, choose the "Easy" option. On the following page is an example execution of the Certbot application including the output it generated. The characters in bold were typed in as input.


**Verify the SSL Certificate**

Open up a web browser and connect to your server over HTTPS. In this example, I am using https://demo.aws2day.online, but remember to use your domain. If the certificate
installation was successful you will not receive any errors or warnings about the SSL certificate. Use your web browser to view the certificate details

You can also check the web server from the command line using the curl utility:

curl https://demo.aws2day.online

If the certificate is valid curl will return the contents of the web site without any errors or warnings. By the way, the certificate files generated by the Certbot application are stored in the /etc/letsencrypt/live directory. The Certbot application will create a subdirectory for each set of certificates created.

find /etc/letsencrypt/live


**** Optional(if faces error for below step): ****

Step A: Create an Apache Virtual Host Configuration

1. Create a new virtual host file in the Apache configuration directory:

sudo vi /etc/httpd/conf.d/demo.aws2day.online.conf

2. Add the following configuration for the virtual host on port 80:

![image](https://github.com/user-attachments/assets/ab5fc85c-d450-4bc8-9bf3-73feaec20051)

<VirtualHost *:80>

    ServerName demo.aws2day.online
    
    DocumentRoot /var/www/html

    <Directory /var/www/html>
    
        AllowOverride All
        
    </Directory>
    
    ErrorLog /var/log/httpd/demo.aws2day.online-error.log
    
    CustomLog /var/log/httpd/demo.aws2day.online-access.log combined
    
</VirtualHost>


3. Save and exit the file

Step B: Restart Apache

sudo systemctl restart httpd

Step C: Run Certbot Again

Now that a virtual host is configured, you can re-run Certbot to request the certificate:

sudo certbot --apache -d demo.aws2day.online

Step D: Verify the SSL Setup

Once Certbot completes successfully, access https://demo.aws2day.online to confirm the SSL certificate is working. Certbot should also automatically configure the HTTPS virtual host and redirect HTTP traffic to HTTPS



**Harden the Apache SSL Configuration - OPTIONAL**

At the time that this document was written, the default Apache SSL configuration that is used on CentOS doesn't account for certain security issues such as the Poodle Vulnerability and Heartbleed.

To address these security issues you'll need to update the Apache SSL configuration.

Open the /etc/httpd/conf.d/ssl.conf file or whichever Virtual Host file you selected when prompted during the Let's Encrypt request process. Feel free to use your favorite text editor such as nano, emacs, or vim. 

vim /etc/httpd/conf.d/ssl.conf

Next, delete or comment out the line that starts with SSLProtocol . To comment out a line, simply insert a # at the beginning of that line.

# Insecure:

# SSLProtocol all -SSLv2

Now add the more secure version of the SSLProtocol configuration.

# Secure:

SSLProtocol all -SSLv2 -SSLv3

Next, delete or comment out the line that starts with SSLCipherSuite .

# Insecure:

# SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5:!SEED:!IDEA


Now add the more secure version of the SSLCipherSuite configuration.

# Secure:

SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH

For complete details on what these settings do, read this . Make sure that the configuration is correct and that there are no errors. If the configuration is valid
the output of the apachectl command will be " Syntax OK ". If there is an error such as a typing mistake, fix it before continuing.

apachectl configtest

Restart the Apache web server so that it uses the updated configuration.

systemctl restart httpd

Renewing SSL Certificates

SSL Certificates issued by Let's Encrypt are valid for 90 days. To attempt an SSL Certificate renewal, use the Certbot application.
certbot renew

Certbot will renew all previously obtained certs that expire in less than 30 days. It will also restart Apache if any certificates are renewed.
Configure Auto Renewal Using Cron

If you don't want to manually renew your SSL certificates, create a cron job that attempts the renewals daily. This can save you from forgetting to renew your certificate and having your website visitors get an Expired SSL Certificate error or warning when they visit your site.

Execute the following command to edit the crontab.

crontab -e

Insert the following configuration. It tells crontab to execute the " certbot renew " command every day at midnight and save the output to the /var/log/certbot.cronlog file.

# Renew SSL Certificates Daily

0 0 * * * /usr/bin/certbot renew &>/var/log/certbot.cronlog

Save your changes. To check that the cron configuration has been updated run the following command.

crontab -l

For reference, below is the crontab format. The first five fields are the time specification. They are minutes, hour, day of the month, month, and day of the week. After the time specification, you provide the command to be executed. The command will only be executed when all of the time specification fields match the current date and time. Typically, one or more of the time specification fields will contain an asterisk (*) which matches any time or date for that field.

* * * * * command
| | | | |
| | | | +-- Day of the Week (0-6)
| | | +---- Month of the Year (1-12)
| | +------ Day of the Month (1-31)
| +-------- Hour (0-23)
+---------- Minute (0-59)

Configure Auto Renewal Using Systemd Timers - OPTIONAL

**NOTE: You only need to schedule automatic SSL cert renewals using either the cron method or the systemd timers method, not both.**


The certbot package includes a certbot-renew systemd service. When you start the service, it attempts to renew the SSL certs on the system just as if you executed " certbot renew" on the command line. This service is not like most services because it executes and then immediately exits. It is not a background service that runs all the time.

systemctl start certbot-renew.service

When you check the status of the service it will report "inactive."

systemctl status certbot-renew.service

The certbot package also includes a systemd timer that will execute the certbot-renew.service daily.

First, start the timer and then enable it so that the timer starts on boot.

systemctl start certbot-renew.timer

systemctl enable certbot-renew.timer

You can view the status of the systemd timers using the following command.

systemctl list-timers


