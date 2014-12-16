---
layout: post
title: "Tis the season of Wifi"
date: 2014-12-15 19:15:07 +0000
comments: true
categories: 
---
Every year in December my family and I go to a small coastal town in the Western Cape. It's a wonderful little town and the weather is perfect for what you would expect from a South African Summer Holiday, but there is one small problem... No proper internet. Being the tech-head in the family, I was once again turned to, to solve the problem. Luckilly I had a Raspberry Pi, D-Link USB Wifi Adapter and Huawei 3G Dongle in my laptop bag... 

<!--more--> 
###Some More Turkey Please
So to set the scene a little better, my family uses more data on their Smart Phones than the contigual United States consumes turkey on Thanks Giving. It's a `stupid` amount of data and usually its by posting and downloading images of lolcats on social media sites like Facebook, and commenting on the other family members' bad use of grammar. 

This is a fine hobby when at home, since all of them have ADSL and WiFi at home, but here, they are burning through their mobile data bundles like a wildfire in the African Bushveld. 

###Please Sir, Can I have some Pi? 
Safe to say, I had to jump in an do something before my whole family spent their holiday budgets on 3G Data Bundles, but I only had limited hardware at my disposal. More specifically, a Raspberry Pi, D-Link USB WiFi Adapter and the Huawei K4305 Vodafone USB 3G Dongle I travel with. Being the MacGuyver-generation guy that I am, I figured if you hand me a toothpic and a hotpocket, I can do this! And I mean, it's Linux. Of course I can!

###The Plan
I previosly set up a Wireless Hotspot using the [HostApd](http://w1.fi/hostapd/) user-space daemon on a small PC I had lying around at home, so I figured that this would also work on a Raspbery Pi. Granted, I would have some compiling in my future to make this work on ARM. Similarly, a few years ago, I was able to successfully establish an Internet Connection using a Huawei E220 USB Dongle and [WVDial](http://en.wikipedia.org/wiki/WvDial). In theory, everything was there, and I figured, all of this should be relatively easy... 

###Icarus, Flying too close to the sun!
The plan was simple... The implimentation was a bit more ambitions than I initially thought. To start off with, the D-Link USB WiFi Adapter I have, uses the `Realtek rtl8188cus` chipset which is not by default available in the HostApd build. This made it a bit difficuilt to get the Hotpsot up and running. Luckilly, Realtek provides the `rtl8188cus` drivers on their website [Here](http://www.realtek.com/downloads/downloadsView.aspx?Langid=1&PFid=48&Level=5&Conn=4&ProdID=274&DownTypeID=3&GetDown=false&Downloads=true#2742) which includes a customized version of HostApd that works with the `rtl8188cus` chipset. The second problem was that WVDial was useless with the Huawei K4305 Dongle since the device no longer exposes a Serial Device to establish a connection like the E220. Like most of the newer generation dongles, this dongle is a MBIM (Mobile Broadband Implimentation) device which requires you to do some USB Switching in order to expose an ethernet device that you can connect through.

###A Slice of Pi. 
For my little makeshift HotSpot, I used the latest version of Raspbian, downloaded from the [Raspberry Pi Foundation Website](http://www.raspberrypi.org/downloads/). I am not going to go through installing Raspbian in this post, maybe in the future.  

###Switching to crying. 
Lets start with getting the Huawei K4305 Modem to work. The MBIM standard specifies that the dongle will have different USB operation modes in which it can be attached to your computer. Mode 1 is usually a Flash Storage mode which has the Drivers and the switching message for your operating system. Once the drivers and software is installed, the switching message will be sent to the device, to switch it to Mode 2. This mode is a virtual ethernet adapter which allows you to use the device as an Internet Gateway. On OSX and Windows this is easily done by the Firmware available on the device, but as far as I can determine, there is no Linux packages available for these devices. 

###Enter USB_ModeSwitch.
Linux being Linux however, there is always some community based software that makes it a little bit easier. In this case, [USB_ModeSwitch](http://www.draisberghof.de/usb_modeswitch/) 

USB_ModeSwitch is (surprise!) a mode switching tool for controlling `multi-mode` USB devices. It's a software tool that uses the [libusb](http://www.libusb.org/) framework to interact directly with the USB Device and switch the modes of the device, based on a ControlMessage and desired operational mode. 

Before installing USB_ModeSwitch, this is the `lsusb` output you will see when the device is connected. 
``` bash lsusb
Bus 001 Device 004: ID 12d1:1f15 Huawei Technologies Co., Ltd.
```

This mode is pretty much useless to us, and we need to now install USB_ModeSwitch and configure it to get our device into the correct format.  
Download the Latest versions of...  
[USB_ModeSwithch](http://www.draisberghof.de/usb_modeswitch/#download)  
[USB_ModeSwitch-Data](http://www.draisberghof.de/usb_modeswitch/#download)  
[LibUSB](http://www.libusb.org/)  

Unzip each of the tarbals into their respective folders. 
```
tar -xvf name-of-tarbal
```

The first thing we need to install is LibUSB.
``` bash Compile LibUSB
cd /path/to/libusb/source
./configure
make
sudo make install
```

This will compile and move the LibUSB library to the appropriate folders for USB_ModeSwitch to use later. Installing USB_ModeSwitch is quite a bit simpler.  
``` bash Install USB_ModeSwitch
cd /path/to/usb_modeswitch
sudo make install
```

This will move the usb_modeswitch binaries to the relevant folders. Now we need to export the data-set rules for the known USB devices. 
``` bash Install USB_ModeSwitch Data
cd /path/to/usb_modeswitch-data
sudo make install
```

The USB_ModeSwitch-data contains the device database and the rules file, including full paths which tells it how to work with specific devices, and how to switch them. So now we have the switcher installed, but we still need to tell it how to handle the Huawei K4305 device. We do this by adding a config file to the /etc/usb_modeswitch.d/ folder. 

``` bash /etc/usb_modeswitch.d/10-huawei-k4305.conf
########################################################
# Huawei K4305
#
# Contributor: Dario

DefaultVendor= 0x12d1
DefaultProduct=0x1f15

TargetVendor=  0x12d1
TargetProduct= 0x1400

MessageContent="55534243123456780000000000000011062000000000000100000000000000"
```

I don't want to go into too much detail about this file, since it will take some time to explain the Vendor and Product ID's. But take a closer look at the DefaultVendor and DefaultProduct ID's and match them to the lsusb output earlier in the post. You can see they match up to the Mode1 ID's of the device. We want USB_ModeSwitch to switch it to Mode2 with the TargetID's. The MessageContent is the message submitted to the device, in order to flip the device to the correct mode. 

At this point, we plug in the device. Which should automatically switch the mode of the device, but just in case it doesn't you can manually run the command: 

``` bash USB_ModeSwitch Device
usb_modeswitch -c /etc/usb_modeswitch.d/10-huawei-k4305.conf
```

Now, when you do lsusb, you should see the following
``` bash lsusb
Bus 001 Device 004: ID 12d1:1400 Huawei Technologies Co., Ltd.
```

As you can see, the Device is now mounted on the target endpoints, and when you do an ifconfig you should now see a new ethernet device eth1.
``` bash ifconfig output
eth1      Link encap:Ethernet  HWaddr 58:2c:80:13:92:63  
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:288212 errors:0 dropped:0 overruns:0 frame:0
          TX packets:241372 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:263381189 (251.1 MiB)  TX bytes:95811469 (91.3 MiB)
```

At this point, you have your device mounted and you have a new cdc_ether device on eth1. But you still don't have internet connection. However, this part is actually stupidly easy. Its a network device... What is the easiest way to get IP Settings from a routing device? Thats right! DHCP!
``` bash  Get the Device IP Details
sudo dhclient eth1
```

This should now assign a new IP address in the 192.168.9.0/24 range. And should assign a new Default Gateway at 192.168.9.1. 
``` bash Ifconfig Output
eth1      Link encap:Ethernet  HWaddr 58:2c:80:13:92:63  
          inet addr:192.168.9.100  Bcast:192.168.9.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:288212 errors:0 dropped:0 overruns:0 frame:0
          TX packets:241372 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:263381189 (251.1 MiB)  TX bytes:95811469 (91.3 MiB)
```

Congratulations! You now have a switched device and internet access! Woohoo! Which is great, but it's still only available on your Raspberry Pi. We now need to set up HostAPD for our HotSpot. 

###Oh HostAPD, how you trick me so, you trickster. 
As mentioned previously in my post, the DLink USB Wifi Adapter I have uses the rtl8188cus chipset, which is a decent chipset, but doesn't play nice with the usual build of HostAPD. The guys at Realtek however were nice enough to add the HostAPD source to the rtl8188cus drivers. You can go through the whole process of recompiling it yourself, like I did. However, after I finally got mine compiled and working, I found a script that makes installing a Hotspot easier than crashing Windows.

``` bash Download and Install HostAPD
curl -kLsO "https://dl.dropboxusercontent.com/u/1663660/scripts/install-rtl8188cus.sh"
chmod +x install-rtl8188cus.sh
sudo ./install-rtl8188cus.sh
```

This script is created by "Paul" and the original post can be found [here](http://blog.sip2serve.com/post/48899893167/rtl8188-access-point-install-script). It simply asks you a few questions about your environment and sets up your Wifi Hotspot.

###Surfin...Surfin Mossel Baaaay!
Thats it folks! My family now has a working Raspberry Pi powered Hotspot for their Holiday. I went a bit further and installed some QOS software and caching software to save some data and bandwith since there is about 12 devices on the hotspot. But you can now customize your hotspot in any way you want!

The caching turned out to be a great idea, since it seems like they are mostly viewing the same photos and sharing the same content between each other. Which is funny, when you think they are literally standing next to each other when they do it. Hahaha. 

I'm just happy I get to watch Marco Polo on [Netflix](http://www.netflix.com)

