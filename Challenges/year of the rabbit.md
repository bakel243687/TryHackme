# YEAR OF THE RABBIT 🐇

Let's have a nice gentle start to the New Year!
Can you hack into the Year of the Rabbit box without falling down a hole?

(Please ensure your volume is turned up!)

Note: I had to start the machine again, so my target IP address changed

I will not lie to you but this challenge was quite challenging couple up with the fact that netowrk was messing with me

## Enumeration
I started this challenge with nmap and gobuster which gave me promising results.

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_08-19-57.png)

nmap gave me open ports, ftp, ssh, http

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_08-20-33.png)

The gobuster result gave me a directory /assets which in its own gave me a step toward completion. The directory had two files, RickRolled.mp4 and style.css. The video called my attention, so went for it and was surprised to see a video I just watched from someone's whatsapp status not long ago.

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_08-20-51.png)

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_08-21-21.png)

On viewing the style.css file, I got a lead. A very good one, I thought. It still led me back to the video. 

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_08-21-42.png)

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_08-26-40.png)

The video also came with a message which makes it take us back to the video in the /assets directory but with some added texts. On watching the video, I got a hint which was not originally part of the video. It said I was not looking in the right place and there was a Burp inbetween the short message. I was facing network issues but honestly, it didn't come to me initially that I should use Burpsuite.

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_14-24-46.png)

After a while away from the challenge, I used BurpSuite and noticed the site routes through a hidden directory before displaying the said video.

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_16-36-23.png)

moving to the directory, I got a picture, hot_babe.png. Downloaded it and ran strings on it. it was promising

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_16-42-08.png)

got my user name for the ftpuser and the supposed password. Create a file with the password list given

![image alt](https://github.com/bakel243687/TryHackme/blob/7cabd2848e31350c5d344fa1af987f7f6c1b9d41/Challenges/Images/Screenshot_2025-09-24_16-50-02.png)

Running hydra with created password file against the ftp server.

![image alt](https://github.com/bakel243687/TryHackme/blob/11108e8d870bf6d6643d0de93458d3c7b03de361/Challenges/Images/Screenshot_2025-09-24_16-55-40.png)

Got access to the ftp server with the credentials and dowloaded Eli's Creds with the get command

![image alt](https://github.com/bakel243687/TryHackme/blob/11108e8d870bf6d6643d0de93458d3c7b03de361/Challenges/Images/Screenshot_2025-09-24_16-55-52.png)

Now, this is where it started messing with me big time. I am surprised, impressed and tired of some ingenius things that people come up with. Today, I heard of BrainF*** code. honestly speaking, that is the name given to the code and it was invented by Urban Müller in 1993. It's an esoteric, minimalistic programming language that uses only eight commands: > < + - . , [ ]. Programs manipulate a tape of memory cells (bytes), a data pointer, and produce output with ..

You can read about it here: https://gist.github.com/roachhd/dce54bec8ba55fb17d3a
Decode the code here: https://md5decrypt.net/en/Brainfuck-translator/

## Exploitation

![image alt](https://github.com/bakel243687/TryHackme/blob/11108e8d870bf6d6643d0de93458d3c7b03de361/Challenges/Images/Screenshot_2025-09-24_16-56-55.png)

After that, I logged into Eli's account via ssh using the just gotten credentials. I was welcomed with a message which gave me the feeling that there is more than one account on this machine. Going through the folders in the machine while forgetting I could just use the find command to look for the any file I wanted.

I went a directory up from Eli folder and saw the second account owned by gwendoline. Moving into the file, I could not read the user.txt file there.

I had to look for the s3cr3t hiding place this guys are talking about

![image alt](https://github.com/bakel243687/TryHackme/blob/11108e8d870bf6d6643d0de93458d3c7b03de361/Challenges/Images/Screenshot_2025-09-24_17-08-40.png)

Using the locate command, I found promising results which after using cat, I got the credentials for Gwendoline's account.

Logged into the account and got the user flag from the user.txt file.

## Privilege Escalation

![image alt](https://github.com/bakel243687/TryHackme/blob/11108e8d870bf6d6643d0de93458d3c7b03de361/Challenges/Images/Screenshot_2025-09-24_17-33-45.png)

Now I have to gain root access. This is where I had to browse and research for the possible way fo doing this. I checked the user privilege for gwendoline and after that, I checked the sudo version

It was a version exploitable. I didn't know at first, so I went to exploitdb and looked up the version and saw there were many exploit for this version. There were many possible exploits but the one that mactch the current scenario was the CVE-2019-14287.

![image alt](https://github.com/bakel243687/TryHackme/blob/11108e8d870bf6d6643d0de93458d3c7b03de361/Challenges/Images/Screenshot_2025-09-24_17-33-19.png)

Process:
env /usr/bin/vi /home/gwendoline/user.txt
(together with the env command is all the privilege gwendoline has as in teh suod -l command)

You will be sent into vim where you would do the below
:set shell=/bin/sh
:shell
(performing this gives you root access)

I performed the process to replicate the vulnerablity and got root access. On checking the root directory, I got the flag in a .txt file.
