**Jenkins Installation:**
- Launch EC2 instance: medium
  
- Create a Volume of 15GB and attach it to the EC2 instance
  
- SSH into the EC2 instance and do following

ssh-keygen -t rsa 

cat .ssh/id_rsa.pub

copy it to, github - settings - sshkeys and paste the pub key



df -h

fdisk -l

mkfs.

mkfs.ext4 /dev/xvdb 

mkdir -p /var/lib/jenkins

vi /etc/fstab 

/dev/xvdb /var/lib/jenkins ext4 defaults 0 0

mount /var/lib/jenkins

systemctl daemon-reload

df -h

fdisk -l 

- **Install LTS Jenkins**
- 
Search jenkins download on google

https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos

yum install -y wget

Do LTS Jenkins installation 

systemctl status jenkins

systemctl start jenkins

systemctl enable jenkins

systemctl status jenkins


ps -ef | grep -i Jenkins

yum install net-tools -y 

netstat -nltp 

http:ip:8080

**reverse proxy set up**

yum install nginx -y

vi /etc/nginx/nginx.conf

remove :38,54d ( all server configurations)

vi /etc/nginx/conf.d/jenkins.conf

upstream jenkins {

	server 34.204.3.124:8080;
 
}

server {

	listen 80 default;
 
	server_name jenkins.test;

	access_log /var/log/nginx/jenkins.access.log;
 
	error_log /var/log/nginx/jenkins.error.log;

	proxy_buffers 16 64k;
 
	proxy_buffer_size 128k;

	location /  {
 
		proxy_pass http://127.0.0.1:8080;
  
		proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
  
		proxy_redirect off;

		proxy_set_header	Host 			$host;
  
		proxy_set_header	X-Real-IP 		$remote_addr;
  
		proxy_set_header	X-Forwarded-For   	$proxy_add_x_forwarded_for;
  
		proxy_set_header 	X-Forwarded-Proto	$scheme;
	}

}


systemctl start nginx




**firewall**: 

dnf install firewalld -y

systemctl start firewalld

systemctl enable firewalld

systemctl status firewalld

firewall-cmd --list-all

firewall-cmd --list-ports

firewall-cmd --add-port=8080 add-port=443 --add-port=80 --permanent

firewall-cmd --reload

firewall-cmd --list-ports


**Troubleshooting**: 

Check if the Server is Reachable

Try pinging the server:

ping -c 4 44.210.114.214

If ping fails, the server may be down, or a firewall is blocking ICMP traffic.

If ping works, proceed to check the port.

Check if Port 8080 is Open on the Remote Server

Use nc (netcat) or telnet to test connectivity:


nc -zv 44.210.114.214 8080

or

telnet 44.210.114.214 8080

If the connection times out, the port might be blocked or the service isn't running.

If you get "Connection refused", the server is reachable, but nothing is listening on port 8080.

Check SELinux Status

If SELinux is enabled, it may block traffic:

getenforce

If Enforcing, try temporarily setting it to Permissive:


sudo setenforce 0

If this fixes the issue, adjust SELinux policies instead of disabling it permanently.

Verify the Target Server is Running a Service on Port 8080

If you own or have access to 44.210.114.214, check:

sudo ss -tulnp | grep ':8080'

If no process is listening, start the application/service.

Check Firewalls on the Remote Server

If 44.210.114.214 is a cloud server (AWS, Azure, GCP, etc.), ensure:

Security Groups allow inbound traffic on port 8080.

Network ACLs allow traffic from your IP.

For AWS EC2:

Open EC2 Security Groups.

Add an Inbound Rule for port 8080 (TCP) allowing access from 0.0.0.0/0.

For firewalld on the remote server:

sudo firewall-cmd --list-ports

sudo firewall-cmd --add-port=8080/tcp --permanent

sudo firewall-cmd --reload

Check Routing Issues

If the network doesn't have a route to the host, check the default gateway:


ip route show

If missing, add a default route:

sudo ip route add default via <gateway-ip>



**HA Proxy Jenkins:** 

EC2 Jenkins1: Install Jenkins; Create ssh-keygen, copy the private_key to jenkins credentials

yum install nfs-utils -y

systemctl start nfs-server

EC2 Jenkins2: Install jenkins; Create ssh-keygen, copy the private_key to jenkins credentials

yum install nfs-utils -y

systemctl start nfs-server

EFS: Create EFS, Under Network - Select the Security Group attached to EC2 instance: 

EFS Attach: 

sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0b1adc1bfcc2689d2.efs.us-east-1.amazonaws.com:/ /var/lib/jenkins/jobs

df -h

**Crumb Request: to reload the jenkins through API calls**

vi reload.sh

#!/bin/bash

crumb_id=$(curl -s 'http://localhost:8080/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)' -u admin:admin)

curl -s -XPOST 'http://localhost:8080/reload' -u admin:admin -H "$crumb_id"

chmod +x reload.sh

Jenkins - manage Jenkins - Security - CSRF Protection - Enable Proxy Compatability(select)

vi /etc/cron.d/jenkinsreload

*/1 * * * * root root /bin/bash /root/reload.sh

systemctl status crond

**EC2 HA proxy:** 

yum install haproxy -y

vi /etc/haproxy/haproxy.cfg

# remove everything below defaults dG

defaults

  log global

  maxconn 2000
  
  mode http
  
  option redispatch
  
  option forwardfor
  
  option http-server-close
  
  retries 3
  
  timeout http-request 10s
  
  timeout queue 1m
  
  timeout connect 10s
  
  timeout client 1m
  
  timeout server 1m
  
  timeout check 10s

frontend ft_jenkins

  bind *:80
  
  default_backend bk_jenkins
 

backend bk_jenkins

  server  ec2-23-22-204-84.compute-1.amazonaws.com 23.22.204.84:8080 check
  
  server  ec2-54-208-113-155.compute-1.amazonaws.com 54.208.113.155:8080 check backup


systemctl restart haproxy 
