# STARTUP

### Description
Read user.txt and root.txt

### Tools Used
1. Nmap
2. ftp
3. ssh
4. john the ripper
5. gpg
6. unshadow
7. google


Yeah, there was no hints of any kind in this challenge

## Reconnaissance

![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_20-52-46.png)

Started off with reconnaisance as usual with nmap to determine which port is open. Unlike me, I use the -A to go aggressive on the scan, and I discovered two open ports, **ftp and ssh**.



![image alt](https://github.com/bakel243687/TryHackme/blob/4da3f83a7d4d970c48d4312cb25481a6dac151ff/Challenges/Images/Anonforce/Screenshot_2025-11-01_20-53-13.png)

I believed I could make something out from this information I had gotten. Carefully looking at the ftp result on the nmap scan, I saw that anonymous login is allowed and the directories on the server had writeable on two directories whereby one looked interesting. I'll get to the directory that got my interest later, let me walk you through getting the user flag.


Now that I am done with my nmap scan, I would login to the ftp anonymously as that is our only way forward. In case you didn't know, anonymous login does not require password. After logging in, I saw that the same directory I saw from the nmap scan is what we have here (as expected).



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-26-43.png)

On changing directory to home, I saw a user named **melodias**. Entering the directory, I was met with the user flag. how simple it is to get that flag. Just know the basics


## Exploitation

Getting the root flag is where the main deal is. I would not lie to you but it gave me a tough time which I eventually did. So you are not coming here in vain.

> Onward we go.


![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-27-29.png)

So that interesting directory I talked about earlier is called **notread**, I'm sure you must have noticed it before you reached this far. In this directory, I saw backup.pgp and a private.asc. These are encrypted data which I would be needing for gaining root privileges on my target machine. It took me 4 bottles of water to think of a way to get this two file together into getting ahead.



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-29-12.png)

I know that I needed to import the secret key for the private.asc but the file required a passphrase before I could proceed. This kept me thinking for a while till I thought of john the ripper which was right, but this was after many research and surfing of the internet. 



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-29-37.png)
![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-31-33.png)
![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-31-51.png)

By using john the ripper, I was able to get the passpharase for the file which I used to import the secret key. 



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-32-03.png)
![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-32-23.png)

Alas, I can successfully decrypt the backup.pgp file. Upon decryption as in the image, I saw that this is the output of the /etc/shadow file. The shadow file stores the hashed password for a linux system



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-37-22.png)

Back to the situation, with this another round of browsing and I found out that I could merge the /etc/passwd and the /etc/shadow file together using unshadow command. Interesting, right? Yeah, thus I journeyed back to the ftp server to retrieve the /etc/passwd for the machine using the get command



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-37-22.png)

Merging the shadow and passwd files together using unshadow



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-37-32.png)

Using john again and rockyou.txt, I got the password to root on the target machine.



![image alt](https://github.com/bakel243687/TryHackme/blob/f8f9c7db1ce8b00ae56267499b8751b4068e4904/Challenges/Images/Anonforce/Screenshot_2025-11-01_22-37-44.png)

Logged in as root and got the flag for **root**.

A lot to learn with such little time
