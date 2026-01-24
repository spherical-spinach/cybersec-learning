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

### Useful admin tools (open easily with run prompt)
	
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

## Search

Useful search engines

- [Shodan](https://www.shodan.io/) - search internet connected devices like servers, routers, webcams, IoT devices
- [Censys](https://search.censys.io/) - search internet connected hosts, websites, certificates etc.
- [Virustotal](https://www.virustotal.com/) - scan files, URLs or hashes for viruses with aggregated results from multiple AV engines
- [Have I been pawned](https://haveibeenpwned.com/) - notifies if an email has appeared in a leaked data breach

## Vulnerabilities and Exploits

- [CVE program](https://www.cve.org/) - dictionary of vulnerabilities
- [Exploit database](https://www.exploit-db.com/) - database of exploits for targetting vulnerabilities
