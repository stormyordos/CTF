# Lovecrypt - Offering

<!--ts-->

* [Lovecrypt - Offering](#Lovecrypt---Offering)
    * [Challenge](#Challenge)
    * [Website](#Website)
    * [Creating a "decrypt" function](#creating-a-decrypt-function)
    	<!--te-->

### Challenge
![img](https://cdn.discordapp.com/attachments/911345800116838420/911524309082701834/unknown.png)



### Website

Opening the link provided in the challenge description brings us to a Lovecraft-themed website:

![img](https://cdn.discordapp.com/attachments/911345800116838420/911523796807192576/unknown.png)

Although most of what is said on the webpage is fluff, two important pieces of information are listed here:

* the encrypted form of the flag to enter (HF-{wpLDu8K0wq3HocSIx4bFi8agx53HvcKoxJLGi8eIxIXCpce0x7rFm8KsxKnCpcakx57Gr8ekxIXCtcKkwrrDow==})

* in the source code of the page, a suspicious ROT-encoded Python function:

	![img](https://cdn.discordapp.com/attachments/911345800116838420/911524022561407006/unknown.png) 

* Using Cyberchef to decode the function (it's a ROT-13), we find its true purpose is to encrypt an "offering":

~~~python
#!/usr/bin/env python3

Ph’nglui mglw’nafh Cthulhu R’lyeh wgah’nagl fhtagn.

--  HANDS OFF IF YOU'RE NOT A CULTIST --

import argparse
import base64

def main():
	parser =  argparse.ArgumentParser()
	parser.add_argument("offering", help="Present your offering to the Old Ones, cultist!")
	args = parser.parse_args()
	offering = args.offering

	print("\nIä! Shub-Niggurath!\n")
	print("---------------------------------")
	encrypt(offering)
	print("---------------------------------")
	print("\nCthulhu fhtagn! ^(;,;)^\n")

def encrypt(payload):
	cipher = ""
	secret = "nyarlathotep"
	key = "r'lyeh" * 13
	key = key[:len(payload)]
	forbidden_chars = []
	for letter in secret:
		if letter not in forbidden_chars:
	    	forbidden_chars.append(letter)
	
	for letter in payload:
		if letter in forbidden_chars:
	    	char_code = ord(letter) - 13
	        letter = chr(char_code)
		char_code = ord(letter) << 2
		letter = chr(char_code)
	    cipher += letter
	cipher = cipher[::-1]
	xored_cipher = ''.join(chr(ord(x) ^ ord(y)) for x,y in zip(cipher,key))
	cipher = (base64.b64encode(xored_cipher.encode('utf-8'))).decode('utf-8')
	print("Here's your encrypted offering :\n")
	print(cipher)

if name == "main":
	main()
~~~


### Creating a "decrypt" function
* If we try describing the "encrypt" function at an algorithmic level, we may end up with this:
	* all characters of the "nyarlathotep" string are forbidden characters
	* all forbidden letters in the cleartext are replaced by the same letter minus 13 in the ASCII code
	* all letters from the cleartext are multiplied by 4 ( << 2, bitwise left shift by 2 positions)
	* we reverse all letters of the text obtained
	* we XOR each letter from the text with each letter at the same position in the string "r'lyeh
		r'lyehr'lyehr'lyehr'lyehr'lyehr'lyehr'lyeh ... " (whose length is equivalent to the cleartext)
		6 - we base64-encode the result 
* Given this above-mentioned algorithm, it is easier to derive the algorithm which will help us reverse the process:
	* we base64-decode the cipher
	* we XOR each letter of the cipher by the letter at the same position in the string "r'lyeh
		r'lyehr'lyehr'lyehr'lyehr'lyehr'lyehr'lyeh ... " (whose length is equivalent to the cipher)
	* we reverse all letters of the text obtained
	* all letters of the text are divided by 4 (bitwise right shift by 2 positions)
	* we add +13 to all letters whose ASCII value is equivalent to "forbidden letters - 13"
	* profit!



After a bit of tinkering, I came up with this crude python function:


~~~python
def decrypt(offering):
    cleartext = ""
    secret = "nyarlathotep"
    key = "r'lyeh" * 13
    key = key[:len(offering)]
    temp_chars = []
    forbidden_chars = []
    for letter in secret:
    if letter not in temp_chars:
        temp_chars.append(letter)
        forbidden_chars.append(chr(ord(letter)-13))    
    

    cipher = base64.b64decode(offering.encode('utf-8')).decode('utf-8')
    xored_cipher = ''.join(chr(ord(x) ^ ord(y)) for x,y in zip(cipher,key))
    cipher = xored_cipher[::-1] # reverses.
       
    for letter in cipher:
        char_code = ord(letter) >> 2
        letter = chr(char_code)
        if letter in forbidden_chars:
            char_code = ord(letter) + 13
            letter = chr(char_code)
        cleartext += letter
    
    print("Here's your decrypted offering :\n")
    print(cleartext)
~~~


Which gives us, when calling it with the encrypted flag:

![img](https://cdn.discordapp.com/attachments/911345800116838420/912485215379795978/unknown.png)

