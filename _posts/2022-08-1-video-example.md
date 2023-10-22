---
layout: post
title: LLMNR / NBT-NS Poisoning
subtitle: Carrying out an active directory attack.
author: Melvin Karuga
categories: homelab 
banner:
  video: 
  loop: true
  volume: 0.8
  start_at: 8.5
  image: "/assets/images/banners/richard-horvath-_nWaeTF6qo0-unsplash.jpg"
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: homelab
sidebar: []
---

## LLMNR/NBT-NS Poisoning
What is LLMNR? LLMNR stands for *link-local multicast name resolution*.  It is used to resolve host names on local networks.
  The main fuction is to allow communication between hosts. LLMNR's predecessor, NetBIOS or NBT-NS, is now outdated. 

This attack takes advantage of this protocol.  When a user enters the name of a request, DNS resolves it.  However, if DNS cannot find the request or if the request is spelt wrong for example, the host sends out a broadcast to the network asking for someone to retrieve their request.  
	If an attacker is sitting on the network and receives this request, they can respond and ask the host to send it's authentification hash over, providing full user access for the attacker once the hash is cracked.

## The Attack:
On our Kali Linux attacking machine, we will be running Responder, a tool that allows us to execute this attack.  We will use the following command:
```bash
sudo python Repsonder.py -I eth0 - rdwv
```
Once this is ran, Responder begins listening for events.  In a real world scenario, an attacker would set this up in the morning, before everyone logs on to their environments so that the attacker can receive the most traffic.

[![respondinglistening.png](https://i.postimg.cc/R0316BMq/respondinglistening.png)](https://postimg.cc/McSfNgf8)

Now we can see that Responder is successfully listening for events.  At this point, we are setup to begin receiving broadcast messages.  After sending a request that does not exist on the user "fcastle", our attacking machine has received a connection:

[![Auto-Hotkey-x-Mq-U1-K6-Iw-I.png](https://i.postimg.cc/TPwyhqJ9/Auto-Hotkey-x-Mq-U1-K6-Iw-I.png)](https://postimg.cc/MnCZFB4M)
## Hash Cracking:
After receving the hash, we will crack it using hashcat on our attacking machine. We will use the rockyou.txt.gz wordlist to run against the NetNTLMv2 hash.  After running haschat, the results are:

[![Capture.png](https://i.postimg.cc/WzfbJQpb/Capture.png)](https://postimg.cc/64nNkbGD)

And just like that, the password has been cracked. At the end of the hash, the password is displayed.  For purposes of this lab, the password was simple and easy to crack. However, after receiving a hash, an attacker will be patient and work on cracking the hash, no matter how diffucult. The usage of weak passwords is surprisingly common within networks that do not have proper security measures set in place. After cracking a hash, an attacker now has access and it is just a matter of enumerating and moving around the network.  Password reuse can also be an issue for networks, so an attacker's next steps would be to spray this password around and see what they can expose.

## Defense:
 So, what can be done to mitigate this issue?
	 The best defense is to disable LLMNR and NBT-NS to completely eradicate the possibility of this event occuring. Another step is to require much stronger passwords.  14+ character passwords are very difficult to crack and would pose a great challenge for any attacker.  However, if an attacker is persistent, the hashes can still be cracked no matter the difficulty.
