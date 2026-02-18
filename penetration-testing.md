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
