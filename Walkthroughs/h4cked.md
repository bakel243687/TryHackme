# h4cked

Find out what happened by analysing a .pcap file and hack your way back into the machine

It seems like our machine got hacked by an anonymous threat actor. However, we are lucky to have a .pcap file from the attack. Can you determine what happened? Download the .pcap file and use Wireshark to view it.

# Analyzing the Attack

First, we analyze the pcap file provided in the room.

![image alt](https://github.com/bakel243687/TryHackme/blob/4bc0d38936dbebf60319643b4080517b0f2f6fd4/Walkthroughs/Images/h4cked1.png)

From the image provided above, we can see the different attempts to login to the FTP server by an attacker. How do we know? There is the constant connection request from one IP to another IP which has port 21 opened.

With that, we can answer the questions in the room

> The attacker is trying to log into a specific service. What service is this?
> FTP

In case you are not familiar with the answer to the next question, you can browse the creator of the tool. It is a tool used for brute force attempt of usernames and passwords

> There is a very popular tool by Van Hauser which can be used to brute force a series of services. What is the name of this tool? 
> Hydra

Moving on, we can see in wireshark that the username is revealed and that is the user account the attacker is trying to bruteforce. No wam. We just need to find the password the attacker used to get in.

![image alt](https://github.com/bakel243687/TryHackme/blob/7aacff01937cfc468de2b8f852720251ee3b71ef/Walkthroughs/Images/h4ckedshot_2025-09-23_02-03-53.png)

How do we do this? We could use protocol follow in wireshark, by right clicking on the trace you want to follow and you would see the follow option among the list. In this case, we are following a trace that has login successful as that is the way we can tell the password used to login to the FTP server.

![image alt](https://github.com/bakel243687/TryHackme/blob/7aacff01937cfc468de2b8f852720251ee3b71ef/Walkthroughs/Images/h4cked2.png)

Following the picture, we can see that the password is password123

> The attacker is trying to log on with a specific username. What is the username?
> jenny

> What is the user's password?
> password123

After knowing the password used to login, we can also see the commands executed on the server by the attacker. One of which is the present working directory (pwd) command. The output gave us /var/www/html

> What is the current FTP working directory after the attacker logged in?
> /var/www/html

We can then see all the other commands executed like the list command and then the execution of a file called shell.php. This action is suspicious.

> The attacker uploaded a backdoor. What is the backdoor's filename?
> shell.php

The execution of a file of that name is worth investigating. Moving on to analyzing the other traces, we see the execution of the shell.php which once we view it, we can see a line-based text data of over 192 lines. An interesting which once viewed, would expose the reverse-shell gotten from pentestmonkey.net

> The backdoor can be downloaded from a specific URL, as it is located inside the uploaded file. What is the full URL?
> http://pentestmonkey.net/tools/php-reverse-shell

The attack performed a reverse shell to gain access to the web server with admin priveleges. The did was done, reverse shell executed

Following one of the TCP traces, I was able to get the commands executed by the attacker to get admin access of the server

![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-18_22-50-51.png)

> Which command did the attacker manually execute after getting a reverse shell?
> whoami

> What is the computer's hostname?
> wir3

> Which command did the attacker execute to spawn a new TTY shell?
> python3 -c 'import pty; pty.spawn("/bin/bash")'

![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-18_22-51-06.png)

> Which command was executed to gain a root shell?
> sudo su

> The attacker downloaded something from GitHub. What is the name of the GitHub project?
> Reptile

> The project can be used to install a stealthy backdoor on the system. It can be very hard to detect. What is this type of backdoor called?
> Rootkit

After everything, I was required to perform the activities the attacker did to achieve this level of access.

# Recreating the Attack

Moving into the shoes of the attacker, I was able to recreate the attack the performed by the attacker.

![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-18_22-44-42.png)
I ran Hydra against the FTP server with username jenny. I got a password which I used to login to the FTP server. 

I downloaded the shell.php file using wget, edited it with my IP address and port number of the shell.php file using nano and then set up my netcat listening on port 777 (any port of your choice)

![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-19_03-14-55.png)
Uploading the edited shell.php back to the FTP server using FTP PUT command.

![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-19_03-17-04.png)

After uploading, I continued with the steps the attacker took. I requested the shell.php on my terminal using curl

This instantly executed the shell.php and gave me instant access to the web server. 
![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-19_03-19-54.png)
Trying the same commands the attack did give the same output. We know we are on the right track to completely reproducing the attack. Just a little more


![image alt](https://github.com/bakel243687/TryHackme/blob/c51358688faa4464f9a01f8bebe8d99b8e0ade89/Walkthroughs/Images/h4ckedshot_2025-09-19_03-23-38.png)
We execute the command to spawn a new TTY shell. we tried switching user using su which got us to jenny's

We then performed sudo -l to lists the allowed and forbidden commands for the current user when using sudo. It provided information on what actions the user can perform with elevated privileges.

![image alt](Walkthroughs/Images/h4ckedshot_2025-09-19_03-26-00.png)

From here, switched to sudo and got access to the flag the /root/Reptile directory

With that, the walkthrough is done
