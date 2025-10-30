# PrintNightmare

## CVE-2021-34527
This vulnerability unlike the CVE-2021-1675, does not require local access to the machine, instead a network. The vulnerability is a simple one and can be easily remediated. CVE-2021-34527, also known as PrintNightmare is a remote code execution vulnerability which exist when print spooler service [MS-RPRN](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/d42db7d5-f141-4466-8f47-0a4be14e2fc1) improperly performs privileged file operations. An attacke whos ssuccessfully exploits this vulnerability could run arbitrary code with SYSTEM privileges.

Yeah, I will make it sound more friendly. There is a service that runs on windows which manages printing when using a printter that is on your network. It makes using a printer in another room possible without having to go to the other room, something like that. Well, Having it enabled on windows 10, windows server 2012 and 2016 (versions before July 7,2021) are vulnerable to this. 

The vulnerability would require prior knowledge of your target and the network topology of the target system, in most cases the network is using an Active Directory. And that is what I would be using too.

## Exploit
Assuming there is a prior knowledge of the target, then we would go ahead to set up our attacking machine for the exploit.

In my desktop, I created two folders one called pn and another called share (You can use any locations on your system, just remember where the needed documents are kept)


In the folder labelled pn, I clone the repo to the exploit I would be using.


After which, I created the payload for this adventure using msfvenom. A basic payload reverse tcp payload

Using metasploit, I configured the handler to the same configurations as the payload created earlier

Now, I ran the handler as a job in the background while making the folder with my payload available as a fake SMB server (Sever Message Block). 


With this, I can ensure that our target machine is vulnerable by using impacket-rpcdump

Now, we run the python code in the directory we cloned from GitHub. The python script may crash after Try 3 (It did crash for me)

Once it's done or you see it crash after Try 3, then I confirmed our exploit success by checking our metasploit job that was running. You would see that a session has been opened with our target. 


Yeah, we have exploited the vulnerability and we can decide to explore the windows machine by interacting with the session and then access the shell of the windows.
