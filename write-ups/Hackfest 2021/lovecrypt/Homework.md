# Lovecrypt - Homework

<!--ts-->

- [Lovecrypt - Offering](#Lovecrypt---Offering)
	- [Challenge](#Challenge)
	- [Website](#Website)
	- [Creating a "decrypt" function](#creating-a-decrypt-function)
		<!--te-->

### Challenge

![img](https://cdn.discordapp.com/attachments/911345800116838420/911524250081460245/unknown.png)



### At the beginning was the Website 

Opening the link provided in the challenge description brings us to another Lovecraft-themed website:

![img](https://cdn.discordapp.com/attachments/911345800116838420/911524393212076032/unknown.png)



This page basically provides us with two pieces of pertinent information:

- A long list of hashes 
- A list of criteria to help us narrow down the one hash we need to crack to find the flag



To review, here are the 6 criteria for hash selection:

1. doesn't start with a consonant, 0 or an even number
2. doesn't have a number in second position
3. contains the letter "c" at least once
4. contains a sequence of 2 or more vowels in a row after the mandatory "c" (not necessarily directly after "c" though)
5. has at least 4 digits in a row before the very last character
6. ends with an odd number, but not one that divides 9



### Then there was Regex

Using the standard KDE text editor, Kate, and its Perl regex processing features, we will proceed by thinning out the list of hashes:



#### Criterion 1: "doesn't start with a consonant, 0 or an even number"

The reverse proposition can be modeled more easily, by selecting all hashes starting by a consonant, 0 or an even number, and then eliminating them. 

We notice that our text to be processed is made of hexadecimal numbers, so our characters are limited to digits from 0 to 9 and letters from A to F inclusively.



Taking advantage of this information, our reverse proposition will look like this:

```bash
(^[bcdf02468].*)$
```



#### Criterion 2: "doesn't have a number in second position"

As with criterion 1, the reverse proposition can be modeled more easily.

We will select all hashes containing a number in second position and eliminate them:

```bash
(^.\d.*)$
```



#### Criterion 3: "contains the letter "c" at least once"

Again with a reverse proposition, we will select all hashes containing NO "c" in any position and eliminate them:

```bash
(^[^c]*)$
```



#### Criterion 4 : "contains a sequence of 2 or more vowels in a row after the mandatory "c" (not necessarily directly after "c" though)"

In this case however, we will proceed by modelling the exact proposition and eliminating the hashes that do not fit. I found it easier this way.



We will select all hashes containing a sequence of TWO OR MORE characters in the {a,e} set (the only vowels in an hexadecimal number) in any position FOLLOWING a "c":

```bash
(^.*c.*[ae]{2,}.*)$
```



#### Criterion 5 : "has at least 4 digits in a row before the very last character"

Same as with the precedent criterion, I found it easier to select the hashes that fit the proposition and eliminate the rest.



We will select all hashes containing FOUR OR MORE digits in the penultimate position in the text:

```bash
(^.*\d{4,}.)$
```



#### Criterion 6 : "ends with an odd number, but not one that divides 9"

At this point, you should end up with about a dozen hashes left. Following the previous steps there should only be one hash fitting this criterion, the one ending with a "5":

```bash
eb88d7636980738cd0522ea69e212905
```

This one fits all 6 criteria, and is the only one to do so.





### And the Web saw that it was good

Hashcat can be a reflex at this point, but looking up the hash on the Web (Hashkiller, Crackstation, Gromweb) will yield the solution quicker here:



![img](https://cdn.discordapp.com/attachments/911345800116838420/911440771943632916/unknown.png)



### Putting it all together

Remember the flag format mentioned in the challenge description, we can now build our working flag:

```bash
HF-fearfearfearfearfearfearfearfear
```





