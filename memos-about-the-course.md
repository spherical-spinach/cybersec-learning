# Purpose of the repository

This repo is for notes for what I'm learning about cyber security in TryHackMe and other resources.

## Windows

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
