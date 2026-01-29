# Purpose of the repository

This repo is for notes for useful resources and knowledge I'm learning about cyber security in TryHackMe and other resources. Mostly reflects my [TryHackMe profile](https://tryhackme.com/p/tryhackme.ulny8) progress. Written without using generative AI.

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

## Search

Useful search engines

- [Shodan](https://www.shodan.io/) - search internet connected devices like servers, routers, webcams, IoT devices
- [Censys](https://search.censys.io/) - search internet connected hosts, websites, certificates etc.
- [Virustotal](https://www.virustotal.com/) - scan files, URLs or hashes for viruses with aggregated results from multiple AV engines
- [Have I been pawned](https://haveibeenpwned.com/) - notifies if an email has appeared in a leaked data breach

## Vulnerabilities and Exploits

- [CVE program](https://www.cve.org/) - dictionary of vulnerabilities
- [Exploit database](https://www.exploit-db.com/) - database of exploits for targetting vulnerabilities
