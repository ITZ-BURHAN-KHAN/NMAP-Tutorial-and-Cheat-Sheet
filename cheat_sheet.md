# NMAP CHEAT SHEET

### Scan IP Address (Targets)

**Command:**
- `nmap 10.0.0.1`
- `nmap 192.168.10.0/24`
- `nmap 10.1.1.5-100`
- `nmap -iL hosts.txt`
- `nmap 10.1.1.3 10.1.1.6 10.1.1.8`
- `nmap www.somedomain.com`

**Description:**
- Scan a single host IP
- Scan a Class C subnet
- Scan the range of IPs between 10.1.1.5 up to 10.1.1.100
- Scan the IP addresses listed in the text file “hosts.txt”
- Scan the 3 specified IPs only
- First, resolve the IP of the domain and then scan its IP address

**NOTE:**
Because no other switches are specified in the commands above (except the target IP address), the command will perform host discovery by default and then scan the most common 1000 TCP ports by default.

### Port Related Commands

On the section above, we have not specified any ports which means the tool will scan the 1000 most common ports. However, in real engagements, you should specify port numbers as well as shown below.

**Command:**
- `nmap -p80 10.1.1.1`
- `nmap -p20-23 10.1.1.1`
- `nmap -p80,88,8000 10.1.1.1`
- `nmap -p- 10.1.1.1`
- `nmap -sS -sU -p U:53,T:22 10.1.1.1`
- `nmap -p http,ssh 10.1.1.1`

**Description:**
- Scan only port 80 for the specified host
- Scan ports 20 up to 23 for the specified host
- Scan ports 80, 88, 8000 only
- Scan ALL ports for the specified host
- Scan ports UDP 53 and TCP 22
- Scan http and ssh ports for the specified host

### Different Scan Types

Nmap is able to use various different techniques to identify live hosts, open ports, etc. The following are the most popular scan types.

**Command:**
- `nmap -sS 10.1.1.1`
- `nmap -sT 10.1.1.1`
- `nmap -sU 10.1.1.1`
- `nmap -sP 10.1.1.0/24`
- `nmap -Pn 10.1.1.1`

**Description:**
- TCP SYN Scan (best option)
- Full TCP connect scan
- Scan UDP ports
- Do a Ping scan only
- Don’t ping the hosts, assume they are up.

**Overview:**
- `-sS`: This sends only a TCP SYN packet and waits for a TCP ACK. If it receives an ACK on the specific probed port, it means the port exists on the machine. This is fast and pretty accurate.
- `-sT`: This creates a full TCP connection with the host (full TCP handshake). This is considered more accurate than SYN scan but slower and noisier.
- `-sP`: This is for fast checking which hosts reply to ICMP ping packets (useful if you are on the same subnet as the scanned range and want a fast result about how many live hosts are connected).

### Identify Versions of Services and Operating Systems

Another important feature of NMAP is to give you a wealth of information about what versions of services and Operating Systems are running on the remote hosts.

**Command:**
- `nmap -sV 10.1.1.1`
- `nmap -O 10.1.1.1`
- `nmap -A 10.1.1.1`

**Description:**
- Version detection scan of open ports (services)
- Identify Operating System version
- This combines OS detection, service version detection, script scanning, and traceroute.

### Scan Timings

These switches have to do with how fast or slow the scan will be performed.

**Command:**
- `nmap -T0 10.1.1.1`
- `nmap -T1 10.1.1.1`
- `nmap -T2 10.1.1.1`
- `nmap -T3 10.1.1.1`
- `nmap -T4 10.1.1.1`
- `nmap -T5 10.1.1.1`

**Description:**
- Slowest scan (to avoid IDS)
- Sneaky (to avoid IDS)
- Polite (10 times slower than T3)
- Default scan timer (normal)
- Aggressive (fast and fairly accurate)
- Very Aggressive (might miss open ports)

### Output Types

For each scan, we recommend outputting the results in a file for further evaluation later on. Nmap supports 3 main output formats as below:

**Command:**
- `nmap -oN [filename] [IP hosts]`
- `nmap -oG [filename] [IP hosts]`
- `nmap -oX [filename] [IP hosts]`
- `nmap -oA [filename] [IP hosts]`

**Description:**
- Normal text format
- Grepable file (useful to search inside file)
- XML file
- Output in all 3 formats supported

**Example:**
`nmap -oN scan.txt 192.168.0.0/24` (this will scan the subnet and output the results in a text file "scan.txt")

### Discover Live Hosts

There are various techniques that can be used to discover live hosts in a network with nmap. Depending on whether you are scanning from the same LAN subnet or outside of a firewall, different live host identifications can be used (we will discuss this later).

**Command:**
- `nmap -PS22-25,80 10.1.1.0/24`
- `nmap -Pn 10.1.1.0/24`
- `nmap -PE 10.1.1.0/24`
- `nmap -sn 10.1.1.0/24`

**Description:**
- Discover hosts by TCP SYN packets to specified ports (in our example here the ports are 22 to 25 and 80)
- Disable port discovery. Treat all hosts as online.
- Send ICMP Echo packets to discover hosts. Ping scan.

### NSE Scripts

Did you know that nmap is not only a port scanner? Actually, there are hundreds of included scripts that you can use with nmap to scan for all sorts of vulnerabilities, brute force login to services, check for well-known weaknesses on services, etc.

**Command:**
- `nmap --script="name of script" 10.1.1.0/24`
- `nmap --script="name of script" --script-args="argument=arg" 10.1.1.0/24`
- `nmap --script-updatedb`

**Description:**
- Run the specified script towards the targets.
- Run the script with the specified arguments.
- Update script database

### Other Useful Commands

Some other miscellaneous but useful commands

**Command:**
- `nmap -6 [IP hosts]`
- `nmap --proxies url1,url2`
- `nmap --open`
- `nmap --script-help="script name"`
- `nmap -V`
- `nmap -S [IP address]`
- `nmap --max-parallelism [number]`
- `nmap --max-rate [number]`

**Description:**
- Scan IPv6 hosts
- Run the scan through proxies
- Only show open ports
- Get info and help for the specified script
- Show currently installed version
- Spoof source IP
- Maximum parallel probes/connections
- Maximum packets per second
