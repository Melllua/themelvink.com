---
layout: post
title: Building a virtual homelab
subtitle: Created using the power of virtualization.
author: Melvin Karuga
categories: homelab
banner:
  video: 
  loop: true
  volume: 0.8
  start_at: 8.5
  image: "/assets/images/banners/computers.jpg"
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: homelab
sidebar: []
---

## Why?
Reading up on material and theory can be helpful, but there was only so much I was able to learn by studying network fundamentals and how vulerabilities are exploited.  I needed to actually go out and experiment with these concepts.  There are alternatives, such as tryhackme and hackthebox, but I wanted to also dig deeper; I wanted to create the simulation myself with an active directory, a domain controller, and an up and running server with users and administrators.  The aim was to simulate a real working environment, all with the help of VMware.

## What is a homelab?
A homelab is an environment that you can create yourelf to practice skills and theories that you run across.  Want to experiment with LLMNR and NBT-NS poisoning? Want to attempt using kerberoasting? Do you simply want to get an idea of what an active directory domain looks like and how it's built? A homelab is a great way of getting this crucial hands-on experience.  

## The Setup
With this homelab, I will be utilizing active directory on a Windows 2019 Enterprise server; this will also act as my domain controller.  I will be also using two Windows 10 virtual machines to act as users in this environment.  As for the attacking machine, I will be deploying a Kali Linux VM.  In the future as I dig deeper, I will be expanding this homelab and adding more machines, more users/admins, and deploying a pfSense firewall as my attacks get more advanced overtime.

## Hardware
As with a virtual lab, I need a decent physical machine to be able to run all of these virtual machines.  My hardware is listed below:
* CPU: AMD Ryzen 5 3600 6-Core Processor  
* RAM: Corsair Vengeance 32GB 3600MHz DDR4  
* GPU: NVIDIA RTX 2070  
* MOTHERBOARD: B450 Pro4  

## Installing and Configuring:
After I installed all of the machines, I began working on the Windows Server and configuring it.  Firstly, we will need two certificates.  We will need both the "Active Directory Certicate Services" or AD CS and "Active Directorty Domain Services" or AD DS.  

**What is AD CS?** 
The importance of AD CS is the usage of PKI, which is the software and tech that allows you to secure your data.  PKI uses digital certificates to authenticate its services. AD CS provides the PKI components and lets it run on your domain.

**What is AD DS?** 
This is the service that is used to set the server as the domain controller. This allows us to store information consisting of users, networks and its applications.

After I have configured the domain controller, I will connect both of the user accounts to the domain.  I'll be doing this by simply joining each device to the local Active Directory domain. Before this, I will need to grap the IP address of the domain controller and input it to both the machine's preferred DNS server. Once this is done, there is now a connection between the users and the server.  Now, we can join the users to the domain. 

To take this a bit further, I am going to make both of the users a local adminsitrator on their respective machines.  This will allow me to have access to several common attacks and be able to execute them.  Attacks such as SMB relay attacks and LLMNR poisoning are now avalaible to me and I will be able to view these attacks in a real environment.  SMB being enabled in the environment also means that I can utilize attacks such as pass-the-password and pass-the-hash.

## Conclusion
As it can be seen, this active directory environment is purposefully set in a vulnerable state in order to execute attacks.  I will be recording my progress with these several vulnerabilities in more depth in future posts; this post was meant for introducing the lab and what it's intended for in the future.  As my knowledge grows, I'll expand this environment to allow for more advanced and sophisticated attacks. Thank you for reading! 