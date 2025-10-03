# Insecure randomness

Yet again, another one. This one was quite a hard one for me to understand, but I got it eventually

Insecure randomness occurs when web applications use predictable or poorly generated random values, making them vulnerable to attacks. While randomness is essential for securing tokens, session IDs, and cryptographic keys, insecure implementation can allow attackers to exploit these predictable values to bypass authentication, hijack sessions, or even decrypt sensitive data. 

Fundamental concepts to better understand this vulnerability are: Randomeness, Entropy, cryptographic keys, session tokens, unique identifiers and seeding

 Randomness refers to the lack of pattern or predictability in data, making it an essential component in secure systems.Image of three colorful dice showing different numbers. In cryptography, true randomness ensures an attacker cannot predict values such as keys, tokens, and nonces.

 
