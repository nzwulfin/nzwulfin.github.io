---
title: Setting up a power profile switcher on the laptop
date: 2024-12-13 18:58 -0500
categories:
  - linux
tags:
  - tips
toc: true
comments: false
---
Fedora 40 on the work laptop has been good but the one thing I'd like is to automatically change power profiles on battery or when charging. Gnome will set a profile on low power, but not on other changes. 

I did a little searching and found a few answers using udev or tlp, but I started with a [solution i found](https://gist.github.com/vwbusguy/02992061b3054e6b1cafe9b94bad500e) on a discussion board. It worked great, but since I've been in a tinkering mood, the fact it ran as a system service and was a little noisy in the logs seemed enough of a reason to poke around. I also don't really know a lot about dbus interfaces so that seemed like an opportunity.
## Getting familiar
I had to get the tires replaced on the car, so had a good hour to poke around on battery at all the different dbus enabled objects and understanding what the original gist did.

introspect, figuring out calls, looking at monitoring
## What do I really need?
starting to re-write, do i need a constant monitor? change dbus objects

make a repo https://github.com/nzwulfin/auto_power_profile
## Writing the first pass

move the profile change to a function so i can read it better, make my notes as variables to debug, change to the new monitor, set up as a user service
## Refactoring bash
unexpected unknown catch, move the guts to a function so I can call it at startup
