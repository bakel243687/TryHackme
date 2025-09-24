YEAR OF THE RABBIT

Let's have a nice gentle start to the New Year!
Can you hack into the Year of the Rabbit box without falling down a hole?

(Please ensure your volume is turned up!)

I will not lie to you but this challenge was quite challenging couple up with the fact that netowrk was messing with me

I started this challenge with nmap and gobuster which gave me promising results.

![image alt]()
nmap gave me open ports, ftp, ssh, http

The gobuster result gave me a directory /assets which in its own gave me a step toward completion. The directory had two files, RickRolled.mp4 and style.css. The video called my attention, so went for it and was surprised to see a video I just watched from someone's whatsapp status not long ago.

On viewing the style.css file, I got a lead. A very good one, I thought. It still led me back to the video but on watchin the video, I got a hint which was not originally part of the video. It said I was not looking in the right place and there was a Burp inbetween the short message. I was facing network issues but honestly, it didn't come to me initially that I should use Burpsuite. After a while away from the challenge, I used BurpSuite and noticed the site routes through a hidden directory before displaying the said video.

moving to the directory, I got a picture, hot_babe.png. Downloaded it and ran strings on it. it was promising

got my user name for the ftpuser and the supposed password. Create a file with the password list given

Running hydra with created password file against the ftp server.

Got access to the ftp server with the credentials and dowloaded Eli's Creds with the get command

Now, this is where it started messing with me big time. I am surprised, impressed and tired of some ingenius things that people come up with. Today, I heard of BrainF*** code. honestly speaking, that is the name given to the code and it was invented by Urban Müller in 1993. It's an esoteric, minimalistic programming language that uses only eight commands: > < + - . , [ ]. Programs manipulate a tape of memory cells (bytes), a data pointer, and produce output with ..

You can read about it here: https://gist.github.com/roachhd/dce54bec8ba55fb17d3a
Decode the code here: https://md5decrypt.net/en/Brainfuck-translator/

After that, I logged into Eli's account via ssh using the just gotten credentials. I was welcomed with a message which gave me the feeling that there is more than one account on this machine. Going through the folders in the machine while forgetting I could just use the find command to look for the any file I wanted.

I went a dirrectory up from Eli folder and saw the second account owned by gwendoline. Moving into the file, I could not read the user.txt file there.

I had to look for the s3cr3t hiding place this guys are talking about

using the locate command, I found promising results which after using cat, I got the credentials for Gwendoline's account.

logged into the account and got the user flag from the user.txt file.

Now I have to gain root access. This is where I had to browse and research for the possible way fo doing this. I checked the user privilege for gwendoline and after that, I checked the sudo version

it was a version exploitable. I didn't know what exploit exactly, so I went to exploitdb and looked up the version. There were many possible exploits but the one that mactch the current scenario was the CVE-2019-14287.

I performed the process to replicate the vulnerablity and got root access. On checking the root directory, I got the flag in a .txt file.
