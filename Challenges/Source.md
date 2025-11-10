# Source

This would be the shortest writeup that I have written, so enjoy.

I don't have the screenshot for the Nmap scan but i will give a little hint of what you could see on the result
The scan should give you an open http service with the webmin

The main focus of this challenge is the webmin version 1.920 which happened to be vulnerable to unauthenticated remote code execution.

In this challenge, I used metasploit to exploit the vulnerability.

![image alt](https://github.com/bakel243687/TryHackme/blob/f15a552de88a0be38df6190011b21303dfc3479c/Challenges/Images/source/Screenshot_2025-10-23_23-22-44.png)

Starting up Metasploit.

Knowledge is power, especially when it’s shared. A collaboration between the open source community and Rapid7, Metasploit helps security teams do more than just verify vulnerabilities, manage security assessments, and improve security awareness; it empowers and arms defenders to always stay one step (or two) ahead of the game.
