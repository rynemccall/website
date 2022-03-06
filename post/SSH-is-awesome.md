---
title: SSH is awesome
date: 2014-06-15T22:51:00-0400
updated: 2022-03-06T08:10-0500
tags: technology
---

I prefer using Linux for my home operating system. Unfortunately, this sometimes makes it difficult to work remotely since many corporate tools only cater to Windows or OS X (even at technology companies). While I was able to get my company's VPN (a Juniper offering) working on Ubuntu 13.10 x86, I was never able to get it working on later versions of x64 Debian-flavored Linuxes. Not wanting to be stuck in the past, I had to come up with an alternative.

First, I setup a virtual machine running Ubuntu 13.10 x86 so I could get the VPN client. Then, I used SSH (and mosh, to help with intermittent connection issues) to connect from my x64 OS, through the virtual machine, to resources on my company's corporate network.

```
ssh -p2222 -tt vagrant@localhost mosh <corporate username>@<server on corporate network>
```

Or, if you don't need [mosh](http://mosh.mit.edu/), you can use plain old SSH for the connection from your VPN virtual machine.

```
ssh -p2222 -tt vagrant@localhost ssh <corporate username>@<server on corporate network>
```

While it is frustrating that vendors like Juniper and corporate IT departments aren't able to support Linux operating systems, this solution is workable. As an added benefit (that only the paranoid will appreciate), using a segregated VPN machine cleanly separates your corporate network from your personal one.
