---
layout: post
title:  "Setting up Nectar Cloud with X forwarding over SSH"
description: "The government spent a ton of money on NeCTAR and we get two free machines through the university, so lets set one up."
categories: nectar nectarcloud nectarresearch research
author: Cooper B
---

![Nectar](https://nectar.org.au/wp-content/uploads/2015/10/nectardirectorate-logo.png)

The government spent a ton of money ($4.94 million allocated in 2015) on National eResearch Collaboration Tools and Resources (NeCTAR) and we get two free machines through the university, so lets set one up. 

[https://nectar.org.au/](https://nectar.org.au/)

## Setting up NeCTAR
Goto Cloud Login

After login through Macquarie, go to Images in the left menu pane.
Find the NeCTAR Ubuntu 14.04 Image and choose “Launch Instance” from right side.

You can then give your instance a name. Choose your flavour which basically is just how powerful you want your VM to be. Larger sizes increase RAM, Disk Space, and Processing Power – however, they also use more of your NeCTAR alocation.

On the right hand side you can see the details of your flavour and how much of your allocation that it will use. I'm going for the small, as tiny seemed a little too, well, tiny...

Don't forget to add your SSH key in the Access & Security Tab! I thankfully have Petra's blog post to follow, yet despite the warnings I was overly eager to set up the VM and missed this step. Had to delete it and restart, because I also think I can just dive in without following instructions… :p

Choose your Key Pair and Check the SSH box

After hitting the green Launch button, the instance will build in the Instances tab on the left (Surprise! Who would have thought.)


## Launching into SSH.
After the Instance is initialised, get the IP address from Instances tab
Open Terminal. (I'm using Linux for initial tests. I will switch to Windows once I get it working on Linux).
I saved my key file to ~/.ssh/Nectar_Key so I ran
{% highlight bash %}
ssh -i ~/.ssh/Nectar_Key ubuntu@ip.address.of.instance
{% endhighlight %}

And I got this error!

INSERT IMAGE HERE!!!

So I had to use chmod 600 to change my key's permissions to only give me (file owner) read/write access (the 6 which is 2 + 4 i.e. read + write bits) and no one else in the file group or outside the file group (the two 0s).
Then I ran a simple apt-get update / apt-get dist-upgrade on the VM to make sure everything was up to date.

## Now on to the fun part. Forwarding X over SSH
the -X flag for ssh provides X forwarding e.g.
{% highlight bash %}
ssh -i ~/.ssh/Nectar_Key ubuntu@ip.address -X
{% endhighlight %}

However, I quickly noticed that because we are using a Server image of ubuntu, no X11 tools or GUI elements are installed. I installed them with the following command
{% highlight bash %}
sudo apt-get install ubuntu-desktop
{% endhighlight %}

This command needs to fetch about 559mb of archives and uses 2,128mb of space (2.0gb pretty much!). So this is where I'm glad that I chose the Small flavour rather than the Tiny one, as the extra 25gb of space goes a long way!
Looks like about 25min wait time on MQ network, so I'll leave this running in my terminal and go do some other work in the meantime. See you soon!

And I'm back…
Ubuntu desktop has installed. Now I exist my SSH session and relogin with X forwarding enabled.
{% highlight bash %}
ssh -i ~/.ssh/Nectar_Key ubuntu@ip.address -X
{% endhighlight %}

I know that nautilus is the file browser in Ubuntu, so I type the command `nautilus` and hit enter

Success!

Now on to Windows!

## The Joy </sarcasm> of Windows.
PuTTY has the ability for X11 forwarding over SSH.
HOWEVER, as we're not in Kansas (Linux) anymore, that means we also need an X server like Xming. 
As I'm not really that keen on setting up an X server in windows, I'm going to go another route which is using MobaXTerm. MobaXTerm is a terminal program for windows that has an X11 server built in and is free for personal use.
[http://mobaxterm.mobatek.net/download.html](http://mobaxterm.mobatek.net/download.html)

When you open mobaXterm you get a home page like this:

IMAGE FILE MISSING

Select Start local terminal
And then the commands are the same as before. I recommend saving your Nectar_Key pem file on the Desktop. Then you can use the following command:
{% highlight bash %}
ssh -i Desktop/Nectar_Key ubuntu@ip.address -X
nautilus
{% endhighlight %}

Success!


## Terminal Tips
Don't forget you can use Ctrl+C as a terminal break command. Nautilus will be running in the terminal window so just switch back to the terminal and hit Ctrl+C to end the process if you need to. You can use an ampersand & at the end of a command to run it as a separate process thread if you still need to do work in the terminal window at the same time. e.g. `nautilus &`


## A Final Note: Reseting NeCTAR VM
One thing I noticed was that after I installed ubuntu-desktop. Apt-get was telling me that my system needed a restart. Not quite sure how I go about that. I'm going to try in the SSH terminal:
`sudo shutdown -r now`

And see if my Nectar VM blows up! 
Basically the shutdown command tells the machine to, well, shutdown… The -r flag is for reboot and the now at the end tells it to do it now. Pretty self explanatory. 
After you run the command terminal / putty etc. will kick you out of the SSH session as the machine has gone offline. I'm going to try reconnecting now and see what happens.
And after I try reconnecting I get this scary message:

IMAGE MISSING 

Considering I just rebooted (and its probably related to that) and I'm not that worried about the integrity of my VM, I'm just going to  delete my key and start again:
`ssh-keygen -R ip.address.of.vm`

It finds my host key and removes it from my keychain. Then I just login again. Accept the new ECDSA fingerprint and we're away!