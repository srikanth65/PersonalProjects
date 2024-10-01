https://yourdevopsmentor.com/blog/networking-for-devops-a-complete-guide/

ping www.google.com             #When you ping, you get information back, including the percentage of lost packets and average round-trip latency

traceroute www.google.com      # “traceroute” is a diagnostic command (also sometimes written as “tracert”)

telnet google.com 443         # A telnet command testing the connection to google.com at port 443

curl http://example.com     # This will send an HTTP GET request to the example.com host.
curl -I http://example.com     #checking an exact response code and only want to see response headers, you can use curl -I
curl -X POST http://example.com    # sends a curl HTTP POST (not GET) request (submitting a change to an object—in this case, a change to http://example.com).

**dig** 
It is used for troubleshooting Domain Name System (DNS) problems and verifying DNS records. dig performs DNS Lookups and then shows you the answers returned from the name server(s)
The basic syntax of dig for a DNS record lookup is [dig] [domain name]. This will make an NS query and return the “A record”—Address record—for a given domain name
**dig google.com**
DNS record for google.com on specifically the 8.8.8.8 server. As you can see, it returns a different ANSWER from the /etc/resolv.conf file used in the default above.
dig @8.8.8.8 google.com
dig -t [record type] [domain name]           # The example below specifics the TXT record for the google domain


**netstat** Netstat shows active TCP connections as well as ports on which the server is listening
netstat -nltp

**nmap**
nmap, or “network mapper”, is also a free, open-source tool like netstat. A use case for nmap is almost identical to that for netstat, with one minor difference. When you’re using netstat, you have to log into a server. But nmap scans all servers in a network
ifconfig # Our IP address inet x.x.x.x and netmask x.x.x.x corresponds to /20 ex: Our IP address is 172.31.44.35 and 255.255.240.0 netmask corresponds to /20
nmap -sn 172.31.44.35/20    # We can see now that there are x hosts in the specified subnet.
nmap -A 172.31.36.237      # Let’s test one of these hosts and find out which ports it is listening to. 
The nmap command helps us see that the there is an SSH and HTTP processes listening to ports on this server
Since we know that a curl is a great tool for HTTP, we can then try obtaining the webpage using curl
curl http://172.31.36.237 

**SSH stands for Secure Socket Shell, or just Secure Shell**
basic syntax to launch SSH is:
[ssh] [user_name@hostname] or [ssh] [user_name@ipaddress]

**SCP (Secure Copy Protocol)** SCP is a similarly secure way to execute actions between a local and remote host, but instead of connecting to a server and executing commands, it transfers file
https://yourdevopsmentor.com/wp-content/uploads/2022/08/image-8.png![image](https://github.com/user-attachments/assets/41c7e086-5cf4-47bd-808f-ffde983e0739)




