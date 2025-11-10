# Source

This would be the shortest writeup that I have written, so enjoy.

I don't have the screenshot for the Nmap scan but i will give a little hint of what you could see on the result
The scan should give you an open http service with the webmin

![image alt](https://github.com/bakel243687/TryHackme/blob/f8c63073244fb2e6eefac8e58e487aa08af26834/Challenges/Images/source/Screenshot_2025-10-23_23-25-19.png)

The main focus of this challenge is the webmin version 1.920 which happened to be vulnerable to unauthenticated remote code execution as seen on [exploitdb](https://www.exploit-db.com/exploits/47230) page.

In this challenge, I used metasploit to exploit the vulnerability.

![image alt](https://github.com/bakel243687/TryHackme/blob/f15a552de88a0be38df6190011b21303dfc3479c/Challenges/Images/source/Screenshot_2025-10-23_23-22-44.png)

Starting up Metasploit.

### Metaploit:
Knowledge is power, especially when it’s shared. A collaboration between the open source community and Rapid7, Metasploit helps security teams do more than just verify vulnerabilities, manage security assessments, and improve security awareness; it empowers and arms defenders to always stay one step (or two) ahead of the game.

![image alt](https://github.com/bakel243687/TryHackme/blob/f15a552de88a0be38df6190011b21303dfc3479c/Challenges/Images/source/Screenshot_2025-10-23_23-23-11.png)

After searching for webmin on metasploit, I looked for the previous vulnerability as in the exploitdb and selected it.

![image alt](https://github.com/bakel243687/TryHackme/blob/f8c63073244fb2e6eefac8e58e487aa08af26834/Challenges/Images/source/Screenshot_2025-10-23_23-23-26.png)

As usual, I read the info for the exploit before utilizing it.

![image alt](https://github.com/bakel243687/TryHackme/blob/f8c63073244fb2e6eefac8e58e487aa08af26834/Challenges/Images/source/Screenshot_2025-10-23_23-23-37.png)

I set up the patload with my IP address (LHOST) and the target IP (RHOST), after which I encountered an error which I fixed by enabling Secured Socket Layer (SSL). With this, I ran the exploit and gained root access to the machine.

![image alt](https://github.com/bakel243687/TryHackme/blob/f8c63073244fb2e6eefac8e58e487aa08af26834/Challenges/Images/source/Screenshot_2025-10-23_23-23-59.png)

Now, we can explore the target machine as root.

