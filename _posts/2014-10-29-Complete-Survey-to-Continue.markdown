---
layout: post
title: Complete Survey to Continue
date: 2014-10-29 02:46:22
---
We have all had to deal with the really annoying survey sites when trying to find material online. I feel as if it is common knowledge that these sites are full of shit, they don't actually contain the file you want but will definitely say that they do simply to get ad revenue from you.

Anyway I thought it would be kinda interesting to reverse engineer how one of these sites works, see if I could find the file itself or just learn a thing or two about it. Unfortunately for me (and clever of them.. or actually just common sense for them) all the file information is kept server side. But here is what I did discover:

I was working on this site here http://www.downloadsnack.com/files/file2332/password

Firstly I fired up a new virtual development environment, because these sites are dodgy as hell. Then I pulled up the sources panel in firebug.

It was downloading a few js files from places, google analytics, jquery and [tipsy.js](https://github.com/jaz303/tipsy); nothing too special here.

There was however a script being downloaded from https://www.adworkmedia.com/, pulled it up and it was obscured as hell.. Bingo. Also at the bottom was some inline javascript that was equally obnoxious.

Now there wasn't much to getting this readable. Between [Beautify](http://jsbeautifier.org/) and [JSDetox](http://www.relentless-coding.com/projects/jsdetox/) it came out very clean. JSDetox is prety nifty, these sneaky Javascripts essentially build up the real code in a string then call an eval() on it. JSDetox stops whenever there is an eval() call and says "what are you evaluating?" then it just copy and pastes it for you.

anyway so some interesting stuff in the code:

###Block_Firebug
This kinda surprised me, I had used firebug no problem. But it was doing a check for console.firebug and was meant to redirect to an error page if it found it. However I guess the team picked up on that so they stopped returning true. Anyway it did nothing essentially.

###Gload
This set a function on window load to start its checks. If a certain variable was true then another function was called. Next to find out what set that variable and what that function did.

Followed the function and all it did was clear the popup that the download now link produced. Somewhat disappointing.

There was an interesting function (Most of these functions had gobbledygook as their names so I'll skip that) that would send an XMLHttpRequest every 3000 milliseconds. All the request contained was the id 4703. Looking around there was several references to 4703 being the GID - Possibly standing for Global Identifier.. Whatever.

Anyway checking firebug and sure enough there was a request going out every 3 seconds. The response would always be 

  0$0$0$0
  
Following the Ajax request function through it would take that response, split it at the $ and put it into an array called uStatus:

  if uStatus[0] == 1
    if uStatus[3] > 0 && uStatus[2] == 1
      Top.location.href == uStatus[1]
    endif
  endif
  
There it is: it sets a new href (Which is going to be the link location) to the response. Bummer, nothing saved on client side (Obvious in hindsight)

I pulled up [Charles Proxy](http://www.charlesproxy.com/) for good measure. There is nothing special going on with the request. Its just going to check if your IP address has completed a survey valid for the GID.

I didn't try doing a survey. But there will be some magic going on there aswell. There were hints of the revenue site doing checks for time taken, fuzzy name and address verification and other black magic to evaluate whether you actually took the surveys (Or purchased the scams.. Whatever) then the server will respond to this request with the actual link location.

Boring result.. But a kinda interesting journey (For me at least).
