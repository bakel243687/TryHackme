# PrintNightmare

## Description
Per Microsoft, "A remote code execution vulnerability exists when the Windows Print Spooler service improperly performs privileged file operations. An attacker who successfully exploited this vulnerability could run arbitrary code with SYSTEM privileges. An attacker could then install programs; view, change, or delete data; or create new accounts with full user rights".

## CVE-2021-34527
This vulnerability unlike the CVE-2021-1675, does not require local access to the machine, instead a network. The vulnerability is a simple one and can be easily remediated. CVE-2021-34527, also known as PrintNightmare is a remote code execution vulnerability which exist when print spooler service [MS-RPRN](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/d42db7d5-f141-4466-8f47-0a4be14e2fc1) improperly performs privileged file operations. An attacker who successfully exploits this vulnerability could run arbitrary code with SYSTEM privileges.

Yeah, I will make it sound more friendly. There is a service that runs on windows which manages printing when using a printer that is on your network. It makes using a printer in another room possible without having to go to the other room, something like that. Well, Having it enabled on windows 10, windows server 2012 and 2016 (versions before July 7,2021) are vulnerable to this. 

The vulnerability would require prior knowledge of your target and the network topology the target system is on, in most cases the network is using an Active Directory. And that is what I would be using too.

## Exploit
Note: The targets IP address in this room was 10.10.183.24 and my host machine was on 10.21.7.59

Assuming there is a prior knowledge of the target, then I would go ahead to set up our attacking machine for the exploit.

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-01-34.png)

In the desktop folder, I created two folders one called pn and another called share (You can use any locations on your system, just remember where the needed documents are kept). 
In the folder labeled pn, I cloned the repository to the exploit I would be using. [CVE-2021-34527](https://github.com/tryhackme/CVE-2021-1675.git)

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-02-24.png)

After which, I created the payload for this adventure using msfvenom. A basic payload reverse tcp payload

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-02-51.png)

Using metasploit, I configured the handler to the same LHOST and LPORT as the payload created earlier


![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-03-25.png)![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-03-46.png)

Now, I ran the handler as a job in the background while making the folder with my payload available with impacket fake SMB server (Sever Message Block), impacket-smbserver. 

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-04-02.png)

With this, I can ensure that our target machine is vulnerable by using impacket-rpcdump and extended grep to get the info we want

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-04-27.png)![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-04-50.png)

Now, I ran the python code in the directory we cloned from GitHub. The python script may crash after Try 3 (It did crash for me)

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-05-02.png)

My smb server would have a feedback like this after the python script, yours should do the same too

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-05-18.png)

Once it's done or it crash after Try 3, then I confirmed the exploit success by checking the metasploit job that was running. I discovered that a session has been opened with our target. 

![image alt](https://github.com/bakel243687/TryHackme/blob/de08d8a7561038e389327bcaa4ce9448710a4185/Walkthroughs/Images/printNightmare/Screenshot_2025-10-30_17-05-37.png)

Yeah, we have exploited the vulnerability and we can decide to explore the windows machine by interacting with the session and then access the shell of the windows.
