---
layout: post
title: First CrackMe - Lafarge-CrackMe2
date: 2014-04-27 21:21:48
---

Okay so this was my first attempt at doing a crackme. Today I successfully created a keygen that spits out a working serial. I borrowed/stole a keygen template from Cyclops and posted my solution into it.

All this can be found on my Github here: [https://github.com/darcys22/ReverseMeTutorials/tree/master/lafarge-crackme2]

This one was really fun. It was noob level (1 out of 10 on CrackMe.de) but that seems appropriate.

The general gist of it was this:

1) First it took the Name and would XOR it against a byte array. This byte array had only 4 elements, after it reached the last element it would start xoring against the letter of your name 4 places earlier.

So if I put "ABCDEFG" as the name it would XOR ABCD against the respective bytes in the array. Then "E" would get XOR'd against "A", "F" against "B" etc

2) It would then do the same thing but backwards, so starting at the last position would work its way back to the start position

3) It would do another XOR function both forwards and backwards. So four XOR functions in total, 2 forwards and 2 backwards.

The arrays were as follows:

    [0xAA,0x89,0xC4,0xFE]
    [0x78,0xF0,0xD0,0x03]
    [0xF7,0xFD,0xF4,0xE7]
    [0xB5,0x1B,0xC9,0x50]

4) After it had done this it would sum the characters into 4 bytes. So it would take the character position modulus 4 and then sum it to that position.

Using ABCDEFGHIJKL as an example, this function would Sum A+E+I into the first byte, then B+F+J into the second byte, for four bytes.

5) This would then be turned into an integer (4 characters of 1 byte become a single integer of 4 bytes) but it would be in the little endian format.
So for my example I got an integer of: "4F2C9AD4" (in hex) but changing it to little endian made it "D49A2C4F"

6) The next bit was kinda cool actually. It would create a new number by dividing the integer by 10 (decimal) and sticking the remainder into its own position. 

The output of for mine was "9763786653", you can test this out by dividing 0xD49A2C4F by 0xA. The first division gives a remainder of "9". Then it divides the answer by 0xA again, which gives a remainder of 7, then 6 etc. 

7) lastly it would reverse the numerals. so "9763786653" became "3566873679"

You can find the crackme here (exe)  : [https://github.com/darcys22/ReverseMeTutorials/blob/master/lafarge-crackme2/crackme.exe]
and you can find my keygen here (exe): [https://github.com/darcys22/ReverseMeTutorials/blob/master/lafarge-crackme2/keygen/bin/Keygen.exe]

Test it out
Name  : AAAA
Serial: 3566873679

The tricky/awesome interesting thing about this crackme was that there was a bug in it. it completely skipped the first letter of the UserName but would still continue for the whole string length. So it would do calculations on a null character at the end.

This one was also good for me because I got to brush up on my C skills. I have been using ruby for quite some time and I forgot how many low level quirks this language had (Can only declare variables at the start of a function in visual studio :/)

Anyway going to start on another tomorrow. I spent way too much time today setting up this webserver (Completely free because I'm cheap lol)
