---
layout: post
title:  "Jekyll NeCTAR"
description: "Jekyll doesn't official support windows so I'm going to setup a NeCTAR virtual machine to run Fedora so that I can SSH in and edit my blog from my windows tablet using PuTTY or MobaXTerm."
categories: jekyll nectar ssh
---

Jekyll doesn't official support windows so I'm going to setup a NeCTAR virtual machine to run Fedora so that I can SSH in and edit my blog from my windows tablet using PuTTY or MobaXTerm.

## Setting up a NeCTAR Virtual Machine for Jekyll
[https://nectar.org.au/](https://nectar.org.au/)
After login through the university, I go to Images in the left menu pane.

Considering my struggles with Ubuntu and that I'm familiar with Fedora, Im going to use the Fedora 23 image.

Select Launch Instance.

Give the Instance a name. Choose the Fedora 23 image from the Image Name Box. Change the flavour to m2.small as the tiny one seems a little too tiny. 

Goto the Access & Security tab and select your key pair and enable SSH.

Hit the Launch button and wait for the machine to initialise. 

Grab the IP address from the Instances tab.

Open a terminal a SSH connection with your prefered method 
(PuTTY, Linux Terminal etc.)

The Fedora machine username is ec2-user 
(I tried login as root, told me to login as ec2-user).

## Cloning Jekyll Blog Git Repo
Considering I've already set up my Jekyll blog and I just want a NeCTAR virtual machine to run Jekyll so that I can edit my blog from anywhere I can access a terminal, I'm just going to clone my existing repo so I can push from the VM.
After SSH in, I'm going to need to install git:
sudo dnf install git

Then I clone my repo:
git clone https://github.com/mergeandvary/mergeandvary.github.io.git

Now I cd into my clone repo directory (tab completion is useful here!)
cd mergeandvary.github.io

I've already got a gem file setup from previously. But I just double check its contents with cat:
cat Gemfile

Contents of Gemfile:
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins

## Installing Jekyll on VM
Now I'm ready to install Jekyll:
sudo dnf install ruby-devel redhat-rpm-config gcc zlib-devel
gem install bundler
bundle install

I initially had errors from bundle install, so I tracked down the missing packages: gcc. After that I had errors with nokogiri about zlib. I already had zlib installed but not zlib-devel so I install that also. After that everything worked.

Now I try a localhost server:
bundle exec jekyll serve

And I get the error about javascript, so I install nodejs as per my previous guide:
sudo dnf install nodejs

## Can't Access Localhost Remotely
However, now we run into another problem. We aren't on the local machine so we can't access localhost. This is where X forwarding over SSH comes in.
First I install firefox on my VM:
sudo dnf install firefox

I also need xorg-x11-xauth to allow for X forwarding over SSH:
sudo dnf install xorg-x11-xauth

I have to get 114mb of packages, so time for a cup of tea and I'll come back once its done. Once that is done we need quit out of our SSH session and relaunch it with X forwarding capabilities. There probably is a better method to do this, but it works well enough and it is easy so I'm just doing it this way. 
Now I can launch firefox from my SSH session. I'll use the & ampersand at the end because I also want to continue using my terminal to run the jekyll server:
firefox &
bundle exec jekyll serve

It's pretty slow going to do it this way – firefox takes a while to start up. I'm sure @brian will have some better suggestions for accessing a localhost address remotely. This is a pretty terrible way to achieve this.

## Writing a Post
I've already got my blog setup in terms of style sheets so all I need to do is write a blog post. So I open the _posts folder:
cd _posts

Seeing as I've got an X over SSH session running, I'm just going to use gedit as my text editor:
sudo dnf install gedit

Now I make my post:
gedit 2016-04-07-Jekyll-on-NeCTAR.markdown

I need to add a YAML front matter header
\---
layout: post
title:  "Jekyll NeCTAR"
description: "Jekyll doesn't official support windows so I'm going to setup a NeCTAR virtual machine to run Fedora so that I can SSH in and edit my blog from my windows tablet using PuTTY or MobaXTerm."
categories: jekyll nectar ssh
\---

And then after that I add my post in plain text and use markdown to add headings etc.
I save that. Run Jekyll server to build it and maybe check it out on my terrible Firefox on X over SSH method if I really. Then I can commit it and push back to my github:
bundle exec jekyll build
git commit -a -m “Jekyll nectar post”
git push origin master




