---
layout: post
title: Getting an old transceiver to work
category: amateur_radio
tags: amateur radio, electronics, stornophone
---

I got my hands on an old [Stornophone 6000 mobilophone](http://www.storno.co.uk/stornophone_6000.htm) which was used for fire department communications back in the day.
After some googling around, it turned out that it can be used on the [amateur 2m band](http://en.wikipedia.org/wiki/2-meter_band) if you can either get it into service mode, or by reprogramming its
internal EEPROM.

However the one I have did not even have a power connector. On the back there is a Male DB25 connector which can be used to interface 12V power, a speaker and a microphone.
After reading [this helpful guide](http://a29.veron.nl/pa1fmr/Storno_CQM6000_handleiding.pdf) (apparently these things are quite popular within the ham community) I got out my trusty soldering iron
and heat gun and got to work.

About half an hour later, [success](/images/posts/2015-01-03_storno.jpg)! I had power, could switch channels, and the speaker worked fine. I managed to get into service mode by holding "1" and "*" while turning the device on, but other than that
no service mode commands seem to work. Still, lots of fun.