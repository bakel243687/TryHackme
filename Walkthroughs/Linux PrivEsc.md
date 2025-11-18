# Linux PrivEsc

Linux Privilege Escalation skills on an intentionally mosconfigured Debian VM multiple ways to get root via SSH


## Service Exploit

I did not really get it but will be late explained in the room

## Weak File Permissions - Readable /etc/shadow

copied the shadow (Passwords enrypted in SHA-512crypt) and passwd file of the VM to my localhost and used unshadow to merge both files for bruteforce with john the ripper

> john --wordlist=/usr/share/wordlists/rockyou.txt unshadow.txt

I got the password for root with this. 

## Weak File Permissions - Writable /etc/shadow

In the case where the shadow file can be written to, identifying the hash format in use is needed for privilege escalation.

mkpasswd -m sha-512 newpasswordhere

With the above command, a new password is created in a sha-512 format which we can copy and replace with the root hashed password in the shadow file.
