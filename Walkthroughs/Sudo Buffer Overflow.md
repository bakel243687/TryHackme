# SUDO BUFFER OVERFLOW

The CVE-2019-18634 is a vulnerability discovered by Joe Vennix. This vulnerability bypasses the sudo password by abusing pwfeedback feature. To explain it really simply, when a program accepts input from a user it stores the data in a set size of storage space. A buffer overflow attack is when you enter so much data into the input that it spills out of this storage space and into the next "box," overwriting the data in it. As far as we're concerned, this means if we fill the password box of the sudo command up with a lot of garbage, we can inject our own stuff in at the end.

This means we could get a shell as root

This exploit works even if we don't have any sudo permission

Saleem Rashid [saleemrashid](https://github.com/saleemrashid/sudo-cve-2019-18634) wrote an exploit in C which I used in this room.

## Exploit

To perform the exploit, I cloned the repo to the exploit using git clone.

![image alt](https://github.com/bakel243687/TryHackme/blob/fdcdb3ac6deb703efed0a4d41a8f83143386b60d/Walkthroughs/Images/Screenshot_2025-10-16_08-44-51.png)

With this, I compiled the program using gcc -o <output-file> <source-file>
I got the below otuput. The blue coloured file is the compiled program and that's what we are using for the exploit.

![image alt](https://github.com/bakel243687/TryHackme/blob/fdcdb3ac6deb703efed0a4d41a8f83143386b60d/Walkthroughs/Images/Screenshot_2025-10-16_08-45-51.png)


In a real box with this kind of scenario, we would have to upload the compiled exploit to the target machine but in this case, the exploit is already provided on the target machine to work with.

![image alt](https://github.com/bakel243687/TryHackme/blob/fdcdb3ac6deb703efed0a4d41a8f83143386b60d/Walkthroughs/Images/Screenshot_2025-10-16_08-46-39.png)

So, I ran the exploit and it did it's job. Gave me root access and I got the flag in the root directory

