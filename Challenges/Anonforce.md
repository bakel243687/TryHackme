# Startup

Description
Read user.txt and root.txt

Yeah, there was no hints of any kind in this challenge

Started off with a little reconnaisance. The usaual nmap to determine which port is open. Unlike me, I use the -A to go aggressive on the scan, and I discovered two open ports, ftp and ssh.

I believed I could make something out from this information I had gotten. Carefully looking at the ftp result on the nmap scan, I saw that anonymous login is allowed and the directories on the server had writeable on two directories whereby one looked interesting. I'll get to the directory that got my interest later, let me walk you through getting the user flag.

Now that I am done with my nmap scan, I would login to the ftp anonymously as that is our only way forward. In case you didn't know, anonymous login does not require password. After logging in, I saw that teh same directory I saw from the nmap scan is what we have here (as expected).

On changing directory to home, I saw a user named melodias. Entering the directory, I was met with the user flag. how simple it is to get that flag. Just know the basics

Getting the root flag is where teh main deal is. I would not lie to you but it gave me a tough time which I eventually did. So you are not coming here in vain.

Onward we go.

So that interesting directory I talked about earlier is called notread, I'm sure you must have noticed it before you reached this far. In this directory, I saw backup.pgp and a private.asc. These are encrypted data which I would be needing for gaining root privileges on my target machine. It took me 4 bottles of water to think of a way to get this two file together into getting ahead.

I know that I needed to import the secret key for the private.asc but the file required a passphrase before I could proceed. This kept me thinking for a while till I thought of john the ripper which was right, but this was after many research and surfing of the internet. 

By using john the ripper, I was able to get the passpharase for the file which I used to import the secret key. With that, I can successfully decrypt the backup.pgp file. Upon decryption as in the image, I saw that this is the output of the /etc/shadow file. The shadow file stores the hashed password fro a linux system

Back to the situation, with this another round of browsing and I found out that I could merge the /etc/passwd and the /etc/shadow file together using unshadow command. Interesting, right?

Using john again and rockyou.txt, I got the password to root on the target machine.

Logged in as root and got the flag for root.

A lot to learn with such little time
