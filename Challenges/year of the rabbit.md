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

