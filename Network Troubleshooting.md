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

- TCP is a connection-oriented protocol on the transfer layer (use for security); 
- UDP is also on the transfer layer but it’s connectionless (use for speed)
- Routing is the process of sending information over a network.
- The DNS network translates computer language to human language and gives names to domains across the network. You can do lookups to find the IP address associated with a certain DN

**HTTP**

**HTTP Headers**

HTTP headers allow the client to add additional information to a request for purposes such as authentication, caching, and specifying the type of client device sending the request. Headers are widely used to control traffic and implement features such as canary releases, blue-green deployments, and a/b testing.

Headers fall into 4 general contexts:

General Header: A header that works for both response and requests messages.

Request Header: A header that only applies to request messages from a client.

Response Header: A header that only applies to responses from a server.

Entity Header: A header that gives information about the entity itself or the resource requested.

Headers are case-insensitive in the structure Name: Value.

**HTTP Methods**

You’ve probably heard about Hypertext Transfer Protocol, also known as HTTP. HTTP allows you to interact with Web pages, HTML documents, and APIs. It is the foundation of any data exchange on the Internet
An HTTP request is a request message from a client to a server asking for access to a resource.

There are 7 main HTTP request methods:

Name	Desription

GET	    Requests the data of an object. The data entity is returned in a response body.

HEAD	  Identical to GET, but without a response body. More for meta information or testing.

POST	  Submits a change to an object.

PUT	    Replaces an object.

PATCH	  Updates an object.

OPTIONS	Describes the communication options for an object.

DELETE	Deletes an object.

**Routing**
So how do we get a packet of information from a host on one network to a host in another? In one word: Routing

**Domain Name System (DNS)**
the process of retrieving an IP address from a web browser request using the DNS lookup process.
1. Client initiates a query to a Recursive Resolver.
2. The Recursive Resolver connects to a Root Server.
3. The Root Nameserver then responds to the resolver with the address of a Top Level Domain (TLD) Server (such as .com or .net)
4. The Root Resolver makes a request to the TLD Server.
5. The TLD Server returns the IP address of the Domain Nameserver, which stores the information about the requested domain.
6. The Recursive Resolver sends a query to the Domain Nameserver.
7. The IP address for the requested domain is returned to the Recursive Resolver from the Domain Nameserver.
8. The Recursive Resolver provides the client with the IP address of the requested domain.

**DNS record types**

DNS records, also known as zone files, provide information about a domain. This includes the IP address that is associated with this domain and how to handle queries for it. Each DNS record has a time-to-live setting (TTL) which indicates how often a DNS server will refresh it. 

Below are the most commonly used types of DNS records and their meaning.

Type	- Name - 	Description
A	- Host address - 	The most basic and the most commonly used DNS record. It translates human-friendly domain names into computer-friendly IP addresses.

AAAA -	IPv6 host address -	Same as A but for IPv6 (a host address that can have more than one IP address).

CNAME -	Canonical name for an alias -	Maps a name to another name. It should only be used when there are no other records on that name.

ALIAS -	Auto resolved alias	- Maps a name to another name, but can coexist with other records on that name.

MX -	Mail eXchange	- Specifies the e-mail server(s) responsible for a domain name.

NS -	Name Server	- Identifies the DNS servers responsible for a zone. One NS record for each DNS server in a zone.

TXT	- Descriptive Text -	Holds general information about a domain name such as who is hosting it, contact person, phone numbers, etc. Widely used for domain ownership verification.











