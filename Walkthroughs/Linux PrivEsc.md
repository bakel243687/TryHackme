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

Switching to the root user using the new password gives root access.

## Weak File Permissions - Writable /etc/passwd

Looking at the permissions of the /etc/passwd file, if it has been misconfigured, then we should be able to edit the passwd file and update it with a new passwd. 

Note: the password in the /etc/passwd file is hashed, so we need to edit the password with the right hash.

Using ```openssl passwd newpasswordhere```, we generate a password hash for the password newpasswordhere. you can feel free to use any password of your choice. 

Editting the passwd file with any editing tool like nano or vim, and place the password hash in between the first and second colon (:) of the root user's row (replacing the "x"). Neat.

## Sudo - Shell Escape Sequence

Using ```sudo -l```, we can discover the list of programs your user can run using sudo. [GTFOBins](https://gtfobins.github.io/) has listed programs which can be used to escape a shell to gain root access. 

Environment Variables

LD_PRELOAD and LD_LIBRARY_PATH are both inherited from the user's environment. LD_PRELOAD loads a shared object before any others when a program is run. LD_LIBRARY_PATH provides a list of directories where shared libraries are searched for first.

We compile the below C code using ```gcc -fPIC -shared -nostartfiles -o /tmp/preload.so /home/user/tools/sudo/preload.c```

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
	unsetenv("LD_PRELOAD");
	setresuid(0,0,0);
	system("/bin/bash -p");
}
```

