---
layout: post
title: Extreme Feedback Device
---
One thing I always wanted to build was an extreme feedback device. It is a device that shows the status of your builds 
on a continuous integration server (CI server). It is so much in your face that you just can not ignore it.

Usually your CI server builds your projects like the reliable work horse it is. In the beginning everyone is eager and 
checks the CI after they checked in some source. But after a while the eagerness goes away. One day you suddenly 
discover that you checked out broken code and that it has been like this for several days. And no one noticed. This is 
when you turn on email notifications on each project. 

This fixes the problem for a certain amount of time. Until one day you check out broken source again and again you 
discover it has been like this for several days. What has happened? Everyone got the email, right? Everyone should 
have noticed the problem, right? But this time everyone decided: "Oh no! Not this time! I did not break the build! 
Somebody else of the team should take care this one. I will do it again the next time." 

And then... they forget about this email. Just like that. They do get on with their work and so the build stays broken.
Because once I read this email and marked it as not important right now, I will forget about it. Cause I have a lot of 
other things on my mind. It happens to all of us. It is what makes us human. 

So what we need is something more persistent. Something that does not go away with just a click. Something that continues
to nudge you: "Hey, you! There is something wrong! One of your builds is broken!"

This is where the [extreme feedback device](https://schneide.wordpress.com/2008/10/27/extreme-feedback-device-xfd-the-onoz-lamp/)
comes into play. It is usually some kind of lamp that is in a good place so everyone
can see it. And it turns a bright red when one of the builds on the CI server breaks. And everyone who sees it, even people
who are not developers. Suddenly your boss says something along the lines "This lamp thingy glows red? Is everything ok?"
And you are ashamed. And that is something you can not ignore. And it is so in your face that you start fixing it. 
Even if it was not your fault that made the lamp red. It is the fault of your team and that is enough.

Up to now I never had the opportunity to build one. But in my current company the time was ripe. I presented the idea 
to my boss and asked for a little funding, which he provided eagerly.

And here is what I used for our lamp:

* [A Raspberry PI 2B](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)
* [A power supply for the Raspberry PI](https://www.raspberrypi.org/products/universal-power-supply/)
* [A Rapberry PI Case](https://www.raspberrypi.org/products/raspberry-pi-case/)
* A micro SD card for the operating system of the pi
* [A USB powered light indicator](https://www.getdigital.de/USB-Mail-Melder.html)
* Some pole to fix the lamp to it. It's higher an everybody can see it better. You can also hang your lamp high.

The USB powered light indicator is really cool. It can glow in any RGB color you like and it is supported by Linux.

1. [Install Raspbian](https://www.raspberrypi.org/downloads/noobs/)
2. Change Hostname and default password
3. Activate sshd
4. Install usb-lib: `sudo apt-get install libusb-1.0-0 libusb-dev`
5. Get and install the software to control the lamp from https://github.com/danielkaefer/usblamp
 
With easy commands you can now control your lamp from cli:

`````bash
usblamp red                   # Turns the lamp red
usblamp blue                   # Turns the lamp blue
usblamp off                   # turns lamp off
usblamp -r 30 _ff6600 off     # Lamp blinks 30 times yellowish
`````

All you need now is a program that queries the REST interface of your Jenkins and controls the lamp according to the
results. I run this script every minute by cron and this is the result:

![Blue lamp indicating good build](/images/XFD_blue_small.jpg) ![Red lamp indicating broken build](/images/XFD_red_small.jpg)
