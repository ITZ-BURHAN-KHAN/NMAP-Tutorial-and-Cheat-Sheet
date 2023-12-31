# NMAP Tutorial and Examples

This is the second part of this article where I'll show you some examples, use cases, and techniques of using Nmap in practical penetration testing and security assessment engagements.

## #1 My Personal Favorite Way of Using Nmap

Whenever I start a penetration test, I follow the steps below with Nmap.

### Step 1a: Host Discovery with Well-Known Ports


nmap -PS21-25,80,88,111,135,443,445,3306,3389,8000-8080 -T4 -oA hostdiscovery 100.100.100.0/24
The above command will perform host discovery to identify live hosts using well-known ports (21-25, 80, 443, etc). The output will be 3 files (gnmap, xml, txt) with the filename “hostdiscovery”. We assume the target network range is 100.100.100.0/24.

This technique is efficient if you are scanning a large public IP range, and you know there is a firewall in front and that only limited ports are visible because of the firewall. The above ports will most probably be visible on public hosts.

## Step 1b: Host Discovery with ICMP

nmap -PE -oA hostdiscovery 192.168.1.0/24
The above is a variation of the previous step (Step 1a) whereby Nmap sends ICMP packets to discover live hosts.

This technique is effective if you are scanning from the same LAN subnet as the target range, and there is no firewall in front of the hosts, and also ICMP ping is not blocked from the hosts.

The end result is the same as the previous step. Live hosts will be recorded in the filename “hostdiscovery” with several ports marked as open for each IP address.

## Step 2: Filter Above Files to Create a Clean Live Hosts List
The filename created above (“hostdiscovery”) will contain hosts with open ports. We can filter all IP addresses in the file above that have at least one open port and create a clean list of live host IPs.

I use the Linux “awk” command for this task as shown below:


awk '/open/{print $2}' hostdiscovery.gnmap > livehosts.txt
From Step 1 before, there are three files created, and one of them is a greppable format file with the extension gnmap (“hostdiscovery.gnmap”).

We run awk to search for open ports in that file and then redirect the output to another file “livehosts.txt”. This file will only contain a list of IP addresses that correspond to live hosts in the target network.

## Step 3: Perform Full Port Scan using the Live Hosts List
Now after identifying the live hosts in the whole subnet, we can perform a full port scan with Nmap towards these hosts only.

By doing this, we managed to be more efficient and perform scans faster than doing a full port scan on the whole target range from the beginning.

nmap -p- -Pn -sS -A -T4 -iL livehosts.txt -oA fullscan
-p-: This scans all ports
-Pn: Do not perform host discovery again
-sS: Perform TCP SYN scan
-A: This combines OS detection, service version detection, script scanning, and traceroute
-T4: Pretty fast and accurate scanning
-iL livehosts.txt: Scan the IPs contained in the file “livehosts.txt”
-oA: Export the results in the file “fullscan”
#2 Scan Network for EternalBlue (MS17-010) Vulnerability
In 2017, a huge zero-day vulnerability in Windows SMB was leaked to the public with the name “EternalBlue” (reference code MS17-010 from Microsoft). This is a critical risk vulnerability that allows easy compromise of remote Windows machines.

You must scan your networks to find out if you have Windows machines that are not patched for this, and the following Nmap script is very useful for this.

nmap -Pn -p445 --script=smb-vuln-ms17-010 192.168.1.0/24 -oN eternalblue-scan.txt
The command above will scan the whole Class C network 192.168.1.0/24 on port 445 (SMB port) for the EternalBlue vulnerability and will write the results in file “eternalblue-scan.txt”.

## 3 Find HTTP Servers and Then Run Nikto Against Them
The following scans the target range (100.100.100.0/24) for HTTP servers (ports 80 and 443) and then pipes the result to “Nikto” for further HTTP scans. Nikto is an open-source tool for identifying well-known HTTP vulnerabilities.
nmap -p80,443 100.100.100.0/24 -oG - | nikto.pl -h –

## 4 Find Servers Running Netbios (Ports 137,139, 445)
nmap -sV -v -p 137,139,445 192.168.1.0/24

## 5 Find Geo-Location of a Specific IP Address
The following command uses the geolocation script “ip-geolocation-ipinfodb” to find the geographic location of a specific IP address. To use the above script you need to create a free account at https://ipinfodb.com/register.php and get an API key to use in the command as shown below (in script-args).

nmap --script=ip-geolocation-ipinfodb --script-args=ip-geolocation-ipinfodb.apikey=[APIKEY] 8.8.8.8
Nmap scan report for google-public-dns-a.google.com (8.8.8.8)
Host is up (0.0097s latency).
Not shown: 998 filtered ports
PORT STATE SERVICE
53/tcp open domain
443/tcp open https
Host script results:
| ip-geolocation-ipinfodb:
| 8.8.8.8
| coordinates (lat,lon): 37.406,-122.079
|_ city: Mountain View, California, United States

# 6 Detect if a Website is Protected by WAF
A WAF (Web Application Firewall) can be a software or hardware device in front of webservers to protect from HTTP web application attacks.

The following command uses a script to detect if the target website is protected by a Web Application Firewall (WAF). The http-waf-detect script uses two arguments to try the tool’s built-in attack vectors for evaluating if the target web domain is protected by a WAF.

nmap -p80,443 --script http-waf-detect --script-args="http-waf-detect.aggro,http-waf-detect.detectBodyChanges" www.networkstraining.com
