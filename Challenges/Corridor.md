# Corridor

![image alt](https://github.com/bakel243687/TryHackme/blob/a46ab134093e60ba6722239e13e450c6effce34f/Challenges/Images/Screenshot_2025-10-22_16-26-22.png)

Honestly, it took me a long time to figure the trick in this room and I feel bad but oh well, I learnt one or two things

So, this is the description of the room
You have found yourself in a strange corridor. Can you find your way back to where you came?

In this challenge, you will explore potential IDOR vulnerabilities. Examine the URL endpoints you access as you navigate the website and note the hexadecimal values you find (they look an awful lot like a hash, don't they?). This could help you uncover website locations you were not expected to access.

# The main deal

Now, I loaded up the machine, put up the ip address into the my search bar because from the description, it is certain a website we are dealing with.

Looking into the source code of the website, I did not think much of the extra href I saw in the code. *So dumb of me

Now, I went on and open burp suite, loaded the site in the burp browser and then I was greeted with the same corridor, on reloading the page, I got a different URL in my search bar and I knew that the endpoint was an encryption like it was said in the description. Putting it in crackstation, I got number 4. A long time of thinking and brainstorming went by and then I deceide to look into the source code again. Lo and behold, it was these hashes that are the sites endpoints.

WHat a life? I copied all the hashes into crackstation and then I ran the beauty of the database. i got the list

On thinking back at the description, I knew I needed the md5 hash of 0. doing so, I can get back to where I started from. *Deep
