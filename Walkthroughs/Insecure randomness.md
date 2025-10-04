# Insecure randomness

Yet again, another one. This one was quite a hard one for me to understand, but I got it eventually

Insecure randomness occurs when web applications use predictable or poorly generated random values, making them vulnerable to attacks. While randomness is essential for securing tokens, session IDs, and cryptographic keys, insecure implementation can allow attackers to exploit these predictable values to bypass authentication, hijack sessions, or even decrypt sensitive data. 

Fundamental concepts to better understand this vulnerability are: Randomeness, Entropy, cryptographic keys, session tokens, unique identifiers and seeding

Randomness refers to the lack of pattern or predictability in data, making it an essential component in secure systems.Image of three colorful dice showing different numbers. In cryptography, true randomness ensures an attacker cannot predict values such as keys, tokens, and nonces.

Entropy represents the amount of randomness or unpredictability in a system and is often used to assess the security of cryptographic keys, tokens, or random values. Higher entropy indicates greater uncertainty, making it more difficult for attackers to predict or guess the values, which is essential for secure cryptographic operations. Low entropy can lead to weak security, increasing the risk of attacks like brute-forcing or token prediction. 

Cryptographic keys are secret values used in algorithms to encrypt and decrypt data, ensuring confidentiality, integrity, and authentication. They are critical components in symmetric and asymmetric encryption methods and must be securely generated and managed to prevent unauthorised access. The strength of a cryptographic key depends on its length and randomness.

Session tokens and unique identifiers are used to maintain user sessions and track interactions in web applications. They must be securely generated with sufficient randomness and uniqueness to prevent token prediction and session hijacking. Proper management and protection of these tokens are essential to ensure secure user authentication and authorisation.

Seeding refers to providing an initial value, known as a seed, to a secure cryptographic function to generate a sequence of random-looking numbers. While these secure functions produce numbers that appear random, the sequence is entirely determined by the seed, meaning the same seed will always result in the same sequence.


