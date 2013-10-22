---
layout: post
title: "Proxmark3 on Nokia n900"
date: 2013-10-22 10:09
comments: true
categories: 
---

Ever since I got my [Proxmark3][1] tool I wanted to make it portable. 
Of course, Proxmark3 has a standalone mode which means you can power it
off of a USB powerbank and operate it with its single button. This option
is obviously limited and I wanted to be able to do more. 

On the other hand, I have a good old Nokia n900. The two seemed like a perfect
pair. N900 doesn't support usb host mode out of the box, but latest firmware 
from the awesome maemo community and a couple of modules and tools does allow
for n900 to act as a USB host. To enable the host mode, you'd need to install 
H-E=N or host mode enabler GUI (the package is called hostmode-gui) which
will in turn install kernel-power (so be careful unless you already have it). 

To actually connect anything to n900 in a host mode you'd probably need some 
adapter to go from female micro USB to female type A USB port. I had a OTG cable
lying around so I used that. But you could just use a female type A to female type A
USB adapter which would be a cheaper option. 
{% img /images/otg.png %}

Start H-E-N and select "Full speed hostmode" and turn VBUS boost on. 
Do note that running HEN will disable battery level monitoring so battery level
indicator won't work. 
{% img /images/hen.png %}

Now, when you connect the proxmark3 board, press "Enumerate" in HEN. This will
enumerate all usb devices attached and make them available to the system. After 
enumeration, the LEDs on proxmark3 board should light up and you should hear the 
relay click. This means that the board is powered up.  Here's how my setup looks:
{% img /images/setup.png %}

If all goes well, you should now be able to see proxmark3 registered either via 
looking at `dmesg` or `lsusb` output.
{% img /images/dmesg.png %}

All is well so far. Next thing we need is a proxmark3 client. Building stuff 
for n900 is a bit painfull so I opted for [debian chroot][4]. You can install debian
chroot package from the repositories (it will take a while, the package is big)
and by doing so get access to all the standard debian packages (most importantly 
for us, dev and build tools). After you've installed debian chroot, to enter it 
from the terminal, just run `debian` as root. Go grab the [proxmark3 source][2]. 
You'll need few aditional packages listed at [installation instructions][3] tho 
you can skip the devkitARM stuff as you probably don't want to build the OS and 
firmware on your phone. You'd probably need to install a few development packages 
for the chroot env (probably a few more than listed, like automake,etc...
but just add them as you go):
```
apt-get install subversion p7zip build-essential libreadline5 libreadline-dev libusb-0.1-4 libusb-dev perl pkg-config
```

After you install all the required packages, building the client is as easy as running
`make`. After the successful build, you should be able to run the proxmark3 client:
{% img /images/pm3.png %}

To be able to talk to the proxmark3 board, you need it attached to a serial port. 
Easiest way to achieve that on the n900 is to install additional kernel modules
which comes with `usbserial` module (additional kernel modules can be found in the 
repositories under the name `kernel-hostmode-modules` and `kernel-hostmode-modules-extra`). 
To load the usbserial module use `modprobe` with suitable arguments (NOTE: run modprobe
in a regular root shell, not the debian chroot):
```
modprobe usbserial vendor=0x2d2d product=0x504d
```

After this, a ttyUSB0 device should be create which you can use to talk to the
proxmark3 and we are done!

{% img /images/ttyusb.png %}

Check if the antenna is working (atm I had only LF antenna attached):
{% img /images/lf.png %}

Hooray!!! I now have a very very portable very very neat RFID tool!
Now to write some scripts to automate the tasks and all and we are all set. 
Small problem is that currently I can't plot :/
{% img /images/noplot.png %}
But I'll fix that soon too

Not sure how long the battery will hold (n900 is not famed for it's battery 
durability) but rough calculations tell me it will work for more than half an 
hour which is enough for my needs. 

Have fun,

ea




 






[1]: http://proxmark3.com/
[2]: https://code.google.com/p/proxmark3/
[3]: https://code.google.com/p/proxmark3/wiki/Linux
[4]: http://wiki.maemo.org/Easy_Debian