# The Cod Caper

## Intro

Hello there my name is Pingu. I've come here to put in a request to get my fish back! My dad recently banned me from eating fish, as I wasn't eating my vegetables. He locked all the fish in a chest, and hid the key on my old pc, that he recently repurposed into a server. As all penguins are natural experts in penetration testing, I figured I could get the key myself! Unfortunately he banned every IP from Antarctica, so I am unable to do anything to the server. Therefore I call upon you my dear ally to help me get my fish back! Naturally I'll be guiding you through the process.

Note: This room expects some basic pen testing knowledge, as I will not be going over every tool in detail that is used. While you can just use the room to follow through, some interest or experiencing in assembly is highly recommended

## Host Enumeration

Started the host enumeration with ```nmap -sC -sV IP-ADDR -v``` which had a result of two open ports, ssh and http (22 and 80). Further examining the result of nmap, the ssh service is running on an outdated Openssh version as well as the web server which is an Apache 2.4.18

## Web Enumeration

Time to look into the web page as it would be the best option due to lack of credentials to access the ssh service. Enumerating the web page diretories and files using ```gobuster -u http://<IP-ADDR>/ -w wordlists -x php,txt,html``` 
The enumeration gave a file, administrator.php

## Web exploitation

