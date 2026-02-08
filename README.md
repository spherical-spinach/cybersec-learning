# Purpose of the repository

This repo is for notes for useful resources and knowledge I'm revising and learning about cyber security in TryHackMe and other resources. Mostly reflects my [TryHackMe profile](https://tryhackme.com/p/tryhackme.ulny8) progress. Written without using generative AI.

## Windows administration

### Active Directory

`dsa.msc` - Active Directory Users and Computers. Use this snap-in to manage AD services. (dsa stands for Directory Services Administration)

`gpmc.msc` - Group Policy Management. Use this snap-in to manage Group Policies in AD environment.

#### What is Active Directory (AD)?

- Way to control an organisation's devices (like workstations, file servers, database servers) from a single point called Domain Controller (DC). No need for tech-support to visit on-site for seven different offices fixing computers and configuring users.

#### What an Active Directory Domain is?

- Domain is a group of users and computers under the admin of the network
- TWO main advantages
  1. Centralised identity management
  2. Managing security policies

#### What components go into an Active Directory Domain?

- Domain Controller (DC) - Server running AD services
- Organisational Units (OUs) - containers that could e.g. reflect company's structure: IT, Sales, HR.
- Group Policy Objects (GPOs) - Set of policies that can be applied to an account or OU. E.g. "Sales can't open control panel".

#### Forests and Domain Trust

- Multiple domains can be joined, making a tree
- Multiple trees can be joined, making a forest
- Domain A can trust domain B, granting Domain B access to resources in Domain A

### Command line

`cmd.exe` - Windows' default command line interpreter

#### All around useful commands

`more` - Show output page by page. Pipe other commands to it like this: `[command] | more`. Or display long text files: `more file.txt`. Press Spacebar to flip page and Enter to go line by line

`cls` - Clears the screen

`help` or `/?` - Show help info for a command. Use: `help [command]` or `[command] /?`

`shutdown /s` - Shuts down system

#### Commands for system info

`ver` - Display Windows version

`systeminfo` - Displays operating system configuration info

`tasklist` - List running processes

`set` - Displays, sets, or removes cmd.exe environment variables (e.g. Path)

`driverquery` - Display a list of installed device drivers

#### Commands for network troubleshooting

`ipconfig` - Display network info like IP address, subnet mask and default gateway. `ipconfig /all` shows more info.

`ping` - Test if a machine can reach destination in internet. Also displays avg round trip time.

`netstat` - Display current network connections and listening ports

`tracert` - Traces network route traversed to reach the target

`nslookup` - Looks up host or domain and returns its IP address

### Powershell

`powershell` - Open powershell, an object-oriented command-line shell

#### General cmdlets

PowerShell commands are known as `cmdlets`. They follow Verb-Noun naming convention, e.g. Get-Content or Set-Location.

`Get-Command`

Display available commands. Returns `CommandInfo` objects with properties `CommandType`, `Name`, `Version` and `Source`. Filter results with e.g. `Get-Command -CommandType "Function"`

`Get-Help`

Usage: Display help page for a cmdlet. Usage: `Get-Help` `cmdlet` [optional: `-examples`]

`Invoke-Command` - used to run scripts and commands on local and remote computers

#### Piping: Sorting, filtering, comparison operators and search (grep)

Sort like this:

`Get-ChildItem | Sort-Object Length`

Cmdlets return objects. Objects and their properties can be piped. E.g. get all objects in current directory, and show only those whose "extension" property is ".txt":

`Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"`

The command above uses `Where-Object` cmdlet and `-eq` (equal to) **comparison operator**. Useful **comparison operators**:

`-eq` - equal to

`-gt` - greater than

`-ge` - greater or equal to

`-lt` - less than

`-le` - less than or equal to

There is also the matching operator: `-like`. It matches a specified pattern, e.g. all current directory's objects that name starts with "the":

`Get-ChildItem | Where-Object -Property "Name" -like "the*"`

This is how you can select properties from piped input, e.g. select only properties `Name` and `Length` from current directory's objects

`Get-ChildItem | Select-Object Name,Length`

Lastly, grep with `Select-String` like this:

`Select-String -Path ".\captain-hat.txt" -Pattern "hat"`

#### System and Network information

`Get-ComputerInfo` - retrieves system information about OS, hardware, BIOS and more. Return more stuff that cmd's `systeminfo`

`Get-LocalUser` - lists all the local user accounts on the system. Use for managing user accounts and understanding machine's security conf.

`Get-NetIPConfiguration` - similar to ipconfig, displays network interfaces, IP adresses, DNS servers, gateway conf

`Get-NetIPAddress` - detailed info on all (both active and inactive) IP addresses configured on the system

#### Real-Time System Analysis

`Get-Process` - show running processes, useful for troubleshooting. E.g. find process by pid: `Get-Process -Id 1234`

`Get-Service` - show running, stopped and paused services. Used for troubleshooting and digital forensics for suspicious services

`Get-NetTCPConnection` - display current TCP connections for finding hidden backdoors or suspicious connections

`Get-FileHash` - generate file hashes. Useful for IR, threat hunting, malware analysis

### Useful GUI admin tools (open easily with run prompt)

`msconfig` - **System configuration**. The _tools_ tab offers useful listing of some admin tools

`mmc` - **Microsoft Management Console**. Basically abstracted version of all admin tools. Used to load and save admin tools into "snap-ins". Use this if you have common set of tools to use (like firewall, Disk Management and Device Manager) and don't like to open them every time separately.

`compmgmt.msc` - **Computer Management**. View and configure system settings and components. Useful for basic admin tasks, since it includes Device Manager, Disk Management, Task Scheduler and more.

`WF.msc` - **Windows Firewall**. Add rules to allow or disallow inbound or outbound traffic and more.

`msinfo32.exe` - **System Information**. Used to check information about system hardware and software components

`resmon.exe` - **Resource Monitor**. Monitor CPU, Memory, Disk, Network resources. Includes live graphs.

`regedt32.exe` - **Registry Editor**. Configure, manage and troubleshoot by editing Windows registry keys and their values.

### Other notes

Mind blow:

A tool called Microsoft Management Console (MMC) seems to be the basis for all other admin tools. It enables you to save or open console sessions a.k.a. "snap-ins". The file extension for snap-in is .msc, which stands for Microsoft Saved Console. Basically any commonly used admin tool like **Windows Defender Firewall with Advanced Security** or **Device Manager** is a simplified instance of MMC. This can be demonstrated by opening MMC (type mmc to run prompt) and adding a new snap-in to the console:

File -> Add/Remove Snap-in.

Even the MMC UI looks alike to many admin tools with its three info columns.

## Linux shells

- Most distros use Bash as their default shell. You can switch shell with `[shell name]`. Some common shells:

- `bash`
- `zsh` - very customizable
- `fish` - quite friendly with auto-correction and syntax highlighting
- `rbash`
- `dash`
- `tmux`

- To see which shell is running: `echo $SHELL`

### Scripts

- Shebang means chars at the start of the script that tell the interpreter, e.g. bash:

`#!/bin/bash`

### Useful commands

`ip a s` (short for `ip address show`) and `ifconfig` - Display network interfaces, IP addresses and more

`telnet [IP address] [port]` - access and manage devices over a network. Unencrypted, so be careful. Use for troubleshooting servers only. Example: `telnet 192.168.2.15 80` would connect to HTML server on `192.168.2.15`

`wc -l` - count how many lines on command output or file

## Search

Useful search engines

- [Shodan](https://www.shodan.io/) - search internet connected devices like servers, routers, webcams, IoT devices
- [Censys](https://search.censys.io/) - search internet connected hosts, websites, certificates etc.
- [Virustotal](https://www.virustotal.com/) - scan files, URLs or hashes for viruses with aggregated results from multiple AV engines
- [Have I been pawned](https://haveibeenpwned.com/) - notifies if an email has appeared in a leaked data breach

## Vulnerabilities and Exploits

- [CVE program](https://www.cve.org/) - dictionary of vulnerabilities
- [Exploit database](https://www.exploit-db.com/) - database of exploits for targetting vulnerabilities

## OSI-model and general networking stuff

Everything but DNS in this table uses TCP as a transport protocol. DNS uses UDP or TCP as a fallback.

| Unsecure protocol | <- Default Port Number | Default port number -> | Secure protocol |
| ----------------- | ---------------------- | ---------------------- | --------------- |
| TELNET            | 23                     | 22                     | SSH & SFTP      |
| HTTP              | 80                     | 443                    | HTTPS           |
| FTP               | 21                     | 990                    | FTPS            |
| SMTP              | 25                     | 465 and 587            | SMTPS           |
| IMAP              | 143                    | 993                    | IMAPS           |
| POP3              | 110                    | 995                    | POP3S           |
| DNS               | 53 (UDP or TCP)        | -                      | -               |

RFC 1918 defines the following three ranges of private IP addresses:

- `10.0.0.0` - `10.255.255.255` `(10/8)`
- `172.16.0.0` - `172.31.255.255` `(172.16/12)`
- `192.168.0.0` - `192.168.255.255` `(192.168/16)`

- Port numbers use two octets, so they range from 1 to 65536. Port 0 is reserved to indicate that port number is not specified or is unknown. Also DHCP uses it to indicate that a client doesn't have an assigned IP address yet and is looking for one.

### OSI model

Open Systems Interconnection model.

Important concepts that relate to each layer. Mnemonic device for layer initials:

`Please Do Not Throw Spinach Pizza Away!` ---> PDNTSPA

#### Layer 1 - Physical

- Cables or antennas
- Electrical, optical or wireless signal
- Defining binary digits 0 and 1

#### Layer 2 - Data link

Represents protocol that transfers data between two nodes on the same network segment, e.g. computer A and B both connected to a switch.

- MAC addresses
- IEEE Ethernet standard, code: 802.3
- IEEE WiFi standard, code: 802.11
- There are wireless standards from different eras. Explanations:
  - **802.11b**: released 1999, operates at 2.4GHz, max speed 11 Mbps
  - **802.11g**: released 2003, operates at 2.4GHz, max speed 54 Mbps
  - **802.11n**: released 2009, operates at 2.4 and 5 GHz, max speed 600 Mbps
  - **802.11ac**: released 2013, operates at 5GHz band, max speed 3.46 Gbps
  - **802.11ax**: released 2020, operates at 2.4 and 5 GHz, max speed 9.6 Gbps

- 802.11ax is also called Wi-Fi 6.

#### Layer 3 - Network

Layer 2 transfers data within a network. Layer 3 represents transferring data between networks.

- Logical addressing and routing
- IP, ICMP, and VPN protocols like IPSec and SSL/TLS VPN

#### Layer 4 - Transport

Enables end-to-end communication between running apsp on different hosts. Like mobile browser and some website.

- flow control
- segmentation
- error correctoin
- TCP, UDP

#### Layer 5 - Session

Responsible for establishing a **session** between two apps on different hosts. This includes

- Initiating
- Maintaining
- Synchronising
- Examples: Network File System (NFS) and Remopte Procedure Call (RPC)

#### Layer 6 - Presentation

Ensures data is delivered in a correct form for the application layer to understand. It handles data

- Encoding (ASCII, Unicode)
- Compression
- Encryption
- Examples: JPEG, GIF, PNG, MIME

#### Layer 7 - Application

Provides network services to end-user apps.

- Examples: HTTP, FTP, DNS, POP3, SMTP, IMAP

### TCP/IP model

TCP/IP model comprises of 4 layers, top to bottom:

- Application layer
- Transport layer
- Internet layer
- Link layer

| Layer Number | ISO OSI Model      | TCP/IP Model (RFC 1122) | Protocols                                       |
| ------------ | ------------------ | ----------------------- | ----------------------------------------------- |
| 7            | Application Layer  | Application Layer       | HTTP, HTTPS, FTP, POP3, SMTP, IMAP, Telnet, SSH |
| 6            | Presentation Layer |                         |                                                 |
| 5            | Session Layer      |                         |                                                 |
| 4            | Transport Layer    | Transport Layer         | TCP, UDP                                        |
| 3            | Network Layer      | Internet Layer          | IP, ICMP, IPSec                                 |
| 2            | Data Link Layer    | Link Layer              | Ethernet 802.3, WiFi 802.11                     |
| 1            | Physical Layer     |                         |                                                 |

- Encapsulation

1. Application data
2. TCP or UDP adds TCP **segment** or UDP **datagram** header
3. IP adds an IP header to the TCP segment or UPD datagram, making it a **packet**
4. Ethernet/WiFi adds header and trailer to the packet, making it a **frame**

### Unsecure networking protocols

#### DHCP - Dynamic Host Configuration Protocol

When device is accessing network, these three things need to be known:

1. IP address and subnet mask
2. gateway address
3. DNS server address

These can be configured manually. But messing up the manual config could create problems like **address conflict**, i.e. two devices are configured with same IP address => host can't use network resources.

Introducing DHCP (layer 7 protocol), which does this config automagically!

- Uses UDP
- Server listens on UDP port 67
- Client sends fro UDP port 68

DHCP follows four steps, abbreviated **DORA**:

1. DHCP **Discover** - The client broadcasts DHCPDISCOVER message trying to reach local DHCP server
2. DHCP **Offer** - The server responds with DHCPOFFER message with IP address available for the client
3. DHCP **Request** - The client responds with DHCPREQUEST message indicating it has accepted the IP
4. DHCP **Acknowledge** - The server responds with DHCPACK message to confirm that the IP is now assigned to the client

#### ARP - Address Resolution Protocol

- Layer 2 (data link layer) protocol for finding MAC addresses on local network
- MAC address is needed if two hosts in local network want to send data link frames to each other
- ARP is encapsulated directly to data link frames, no UDP or IP needed

#### ICMP - Internet Control Message Protocol

-
- Mainly used for network diagnostics and error reporting
- Used by `ping` and `traceroute` (Windows: `tracert`)
- `ping` sends **ICMP Echo Request** (ICMP Type 8). Server sends back **ICMP Echo Reply** (ICMP Type 0)
- `traceroute` utilizes IP field TTL (Time-To-Live). Every router decrements TTL by 1. When last router decrements it to 0, it drops it and sends **ICMP Time Exceeded message** (ICMP Type 11). Traceroute sets TTL from 1 to N so that routers between host and server send their info with ICMP Type 11.

#### DNS - Domain Name System

- Used for mapping domain names to IP addresses
- Layer 7 (Application layer) protocol
- Uses UDP port 53 or TCP port 53 as a default fallback

- Different DNS records:
  - **A record** - A for address. It maps hostname to **IPv4** address(es)
  - **AAA Record** - Same as A record, but for **IPv6**
  - **CNAME Record** - Maps domain name to another domain name (like example.com -> example.org)
  - **MX Record** - Mail exchange record. Informs about domain's mail server

#### HTTP and HTTPS

- HTTP uses TCP port 80. Less common is 8080
- HTTPS uses TCP port 443. Less common is 8443

#### FTP - File transfer protocol

- Listens on TCP port 21 by default
- Faster than HTTP
- Example commands in FTP
  - `USER` - used to input username
  - `PASS` - used to enter password
  - `RETR` - used to download (retrieve) a file from the FTP server
  - `STOR` - used to upload (store) a file to the FTP server

#### SMTP - Simple Mail Transfer Protocol

- SMTP server listens on TCP port 25 by default
- Used for sending and receiving email
- Common commands:
  - `HELO` or `EHLO` - initiates SMTP session
  - `MAIL FROM` - specifies the senders email address
  - `RCPT TO` - specifies the recipient's email address
  - `DATA` - indicates that the client will be sending the email content
  - `.` - sent on a line by itself to indicate end of email

#### POP3 - Post Office Protocol version 3

- listens on TCP port 110 by default
- Used to retrieve email
- Client sends email using SMTP, and retrieves them using POP3. As a methaphor, SMTP would be giving your mail to mail person, and POP3 would be checking your post box on your yard for mail.
- Common POP3 commands:
  - `USER <username>` - identifies the user
  - `PASS <password>` - provides password
  - `STAT` - requests number of messages and total size
  - `LIST` - lists all messages and their sizes
  - `RETR <message number>` - retrieves a message by number
  - `DELE <message number>` - marks a message for deletion
  - `QUIT` - ends the POP3 session applying changes like deletions

#### IMAP - Internet Message Access Protocol

- Listens on TCP port 143 by default
- Used for accessing emails from multiple devices. POP3 will just delete the accessed message, but IMAP syncs read, moved and deleted messages accross devices.
- Uses more storage than POP3
- Common IMAP commands
  - `LOGIN <username> <password>` - authenticates user
  - `SELECT <mailbox>` - selects mailbox folder to issue commands to
  - `FETCH <mail_number> <data_item_name>` - E.g. `fetch 3 body[]` gets message 3 header and body
  - `MOVE <sequence_set> <mailbox>` - moves the specified messages to another mailbox
  - `COPY <sequence_set> <data_item_name>` - copies specified messages to another mailbox
  - `LOGOUT` - logs out

### Secure networking protocols

The unsecure protocols can't guarentee data:

- **Confidentiality** (somebody can read the message)
- **Integrity** (somebody can tamper with the message)
- **Authenticity** (somebody can fake a server sending the message)

#### TLS

- Operates at layer 4 (transport layer)
- Securing protocols like HTTP -> HTTPS and DNS -> DoT
- If server wants to use TLS, it needs TLS certificate from CA (Certificate Authority)
  - Let's Encrypt provides them for free
- Certs are installed on the host to confirm server is legit

#### HTTPS (again)

- Uses port 443 by default
- HTTPS = HTTP + TLS
- TLS is added after TCP handshake:
- Unsecure **HTTP** steps for requesting a web page
  1. Resolve domain name to IP address using DNS
  2. Establish TCP three-way handshake with the server
  3. Use HTTP requests like `GET / HTTP/1.1`
- **COMPARE** this to **HTTPS** steps
  1. Resolve domain name to IP address using DNS
  2. Establish TCP three-way handshake with the server
  3. **_Establish a TLS session_**
  4. Use HTTP requests like `GET / HTTP/1.1`

#### SMTPS, POP3S, IMAPS, FTPS

- Same principle than HTTPS, **TLS session is established after TCP handshake**
- They need TLS cert

#### SSH & SFPT

- Default port TCP 22
- Use `-X` with SSH to support GUI
- SFTP (SSH File Transfer Protocol) is part of SSH suite

### Routing

Here are common routing protocols

- **OSPF** _(Open Shortest Path First)_ - routers share info about network topology
- **EIGRP** _(Enhanced Interior Gateway Routing Protocol)_ - Cisco's proprietary protocol
- **BGP** _(Border Gateway Protocol)_ - primary routing protocol used on the Internet
- **RIP** _(Routing Information Protocol)_ - creates routing table with hops. Routers use route with fewest hops. Used in small networks

#### NAT - Network Address Translation

- Used by routers to map local IP to public IP
- Saves a lot of IPv4 addresses, and increases security
- NAT translates both local IP and port number to public IP and public port number
- From Internet's perspective, local network has only one IP address, which is the router's

## Tools for analyzing networks

### Wireshark

- Used for
  - Detecting network problems like load failure points and congestion
  - Detecting security anomalies like rogue hosts, weird port usage or suspicious traffic
  - Learning protocol details like response codes and payload data

- Packet details view shows OSI-model layer protocols and objects like Ethernet, MAC, TCP, IP, HTTP
- Useful keyboard shortcuts
  - `Ctrl+G` -> Go to packet by number
  - `Ctrl+F` -> Find packet by content

#### Packet filtering

- You can filter captured or displayed packets by e.g. protocol name, port, IP address
- You can combine these with boolean operators

Useful filter stuff

- **Analyse -> Apply as filter** or dragging will apply a packets field as filter
- **Analyse -> Conversation Filter** will apply e.g. a TCP conversation between hosts
- **Analyse -> Colorize Conversation** same as above, but no display filter, just coloring packets
- **Analyse -> Prepare a filter** will add a filter without applying it. Then you can add more filters with **Apply as filter -> and/or...**
- **Analyse -> Apply as Column** will add a packet's selected field e.g. TTL as a column
- **Analyse -> Follow stream** will show reconstructed data stream of a conversation between hosts. Use to see cleartext POSTed passwords and server responsed easily.

### Tcpdump

- Uses `libpcap` library for packet capturing, same as Wireshark. While Wireshark provides GUI, `tcpdump` is used in the CLI.
- Common useful arguments for `tcpdump`
  - `-i INTERFACE` - specify network interface to listen to. `-i any` listens to all interfaces. List interfaces with `ip a s`
  - `-w FILE` - save capture to a `.pcap` file
  - `-r FILE` - read captured file
  - `-c COUNT` - limit the number of captured packets
  - `-n` - don't resolve IP addresses to avoid unnecessary DNS lookups
  - `-nn` - don't resolve IP addresses AND port numbers like 80 -> http
  - `-v`, `-vv`, `-vvv` - verbose output, adding e.g. TTL, packet ID and length. More v's, more stuff

Examples:

`tcpdump -i eth0 -c 50 -v` will capture 50 packets listening to eth0 interface, and displays packets verbosely.

`tcpdump -i eht0 -w capture.pcap` will listen to eth0 interface, and write captured packets to capture.pcap

#### Filtering

You can filter captured packets by

- host
- port
- protocol
- packet length - `greater LENGTH`, `less LENGTH`
- tcp flags

- many more, check `man pacp-filter`

And chain filter together with `and`, `or` and `not`.

**Host** filtering examples:

`tcpdump host example.com -w http.pcap` - capture all packets coming from example.com and save them to `http.pcap`. Host address can be domain name or IP.

`tcpdump src host [IP address] -w http.pcap` - capture packets where host address is `[IP address]`. Also `dst` a.k.a. destination address can be specified.

**Port** filtering examples:

`tcpdump -i ens5 port 53 -n` - capture port 53 (DNS) packets without resolving IP address

`src port PORT_NUMBER` and `dst port PORT_NUMBER` work nicely too.

**Protocol** filtering examples:

`tcpdump -i ens5 icmp -n` - filter by ICMP protocol, don't resolve IP addresses
