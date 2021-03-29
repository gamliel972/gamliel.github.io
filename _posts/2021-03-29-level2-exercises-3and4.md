---
layout: post
title: "Level 2 - Exercises 3 and 4 in Crack the Hash room @realTryHackMe"
published: true
---

Well, make it simple, for the other exercises there are writeups where you can take a look if you need help. In this summary I'll show how to see the cracked passwords with hashcat.


For the exercise 3 you must have to copy the hash into a file (without the salt) and define the hashcat syntax like this:

```hashcat -m 1800 sha512.txt /usr/share/wordlists/rockyou.txt --session sha512```

Personally the cracked password didn't match with the answer the exercise wait to receive, so looking in the man page by adding  ```--show``` option at the end of the command used to crack the hash you can see your cracked password, here is the mine:

![Screenshot for sha512 hash cracked](/images/sha512.png)


For the 4th exercise you have to copy the hash and the salt inside a file in this way hash:salt, example:

e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme

Then follow the hint given in the room for this exercise it says **HMAC-SHA1**, thus in order to use this mode in hashcat we have to use **-m 160** as stated in *https://hashcat.net/wiki/doku.php?id=example_hashes*. Here is the command I launched:

```hashcat -a 0 -m 160 sha1.txt rockyou.txt -o 1 -D 1 --force```

![Screenshot for sha1 pass + salt hash cracked](/images/hmac-sha1.png)

Note that in 3rd exercise I did it in a VM so hashcat delayed long, in this exercise I used the rockyou.txt file that is located in /usr/share/wordlists/ but for the 4th exercise I did it in my local Debian machine so in this exercise my hash file and rockyou.txt file were in the same directory.

Hope you enjoyed and learning something new as I enjoyed giving a try to hashcat that I've never used before.

#HappyHashCracking
