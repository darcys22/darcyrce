---
layout: post
title: Monkey Keygen #1
date: 2014-04-29 19:51:25
---

Okay so this was my second crackme. I am yet to make the keygen for it, partially because it looks really gay and partially because I am over sitting in front of the computer. I probably will though, I need to get my c programming up to speed and the only way is through constant use.

So I went onto CrackMe.de to find another challenge and I decided to pump myself up to the next level. I chose the second difficulty level (Out of Ten) expecting a challenge. This level is "Needs a little brain (or luck)". Unfortunately I was a bit disappointed. The other crackme (lvl 1 noob) was much more difficult.. but whatever.

So I booted this thing up and I realised in my noobiness that I have no idea how to locate the interesting parts of the program. After a lot of googling I found out about intermodular calls and how you should look for things like:

*GetDlgItemTextA
*GetWindowTextA
*lstrcmpA
*GetPrivateProfileStringA
*GetPrivateProfileIntA
*RegQueryValueExA
*WritePrivateProfileStringA
*GetPrivateProfileIntA

I found out about this crazy idea though [R4ndom's Tutorials](http://thelegendofrandom.com/blog/archives/654).

Unfortunately this did me no good. The program used none of them. So I was messing around with it a bit and I noticed that IDA said "Delphi" as in the IDE. I vaguely remember somewhere saying that applications developed using this IDE were a pain in the arse. So I googled around and found a tool that gets useful information out of it. The tool is [Dede](http://stackoverflow.com/questions/335104/is-there-a-program-to-decompile-delphi) and it told you pretty much exactly where to go, which was
pretty nice.

So it gave me the structure of the window and then the callbacks when the buttons are clicked. AKA it pretty much gave me the code that gets executed right after you click on "Register".

So started Ollydbg at that point and went walking through. Pretty quickly I got to the point that I though was the generating routine. A few loops of this confirmed it. In fact it was the only routine involved. And it managed to fit onto my tiny screen (I use an 11' ultrabook). 

It did look a little obfuscated but it was probably more because my noobish eyes don't intuitively see though this stuff yet. Anyway here is the algorithm I worked out:

1) :X is what is used to mix with the characters; This is a 4 character hexidecimal. It starts with the inital value of "4DE1"
2) Right Shift it 8 bytes; This makes the new value "4D"
3) XOR with the character of your username; It will iterate though all characters
4) This actually becomes the character in the serial. So my First Character was "41", Xored with "4D" becomes "0C" --> first char of serial
5) But it will continue building the new value of :X for the next iteration
6) So add the result ("0C") to the original :X --> "4DeD"
7) Multiply by "CE6D"
8) AND the result with "FFFF" (Actually it discards everything but the least significant 16 bits, I though Anding was just easier)
9) ADD "58BF" to that result
10) AND the final result with "FFFF"
11) This will become the new value of X

Pretty straight forward, didn't actually take me too long. I did have a bit of trouble figuring out the multiplication. It was using an IMUL instruction with only one operand. Finding documentation on it wasn't pleasent and it meant that it was only using the 16 bit registers for AX and DX. Because the upper halves of these registers still had garbage it meant the results were polluted.

Actually that was my thoughts on this whole CrackMe, it used half registers a lot and half of the values on the stack. So there was a lot of garbage around and it was unaligned. This might be considered obfuscation but it wasn't difficult, only annoying.

I guess I will try another tomorrow or so. I think I want to try one that has been packed. I am less intimidated by them now because I feel that there will be tools around to help.

I think that is what I got most out of this. Searching for the interesting part of a program sucks, and having tools around to help is super useful. I much prefer once you actually get into the program and start searching around but I will get there.
