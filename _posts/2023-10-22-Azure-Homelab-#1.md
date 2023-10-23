---
layout: post
title: Building an azure homelab
subtitle: Homelab in the cloud.
author: Melvin Karuga
categories: homelab
banner:
  video: 
  loop: true
  volume: 0.8
  start_at: 8.5
  image: "assets/images/banners/pexels-donald-tong-133953.jpg"
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: Azure-homelab
sidebar: []
---

## Background
It's hard to believe how much time has flown since my last homelab update. Stepping into my role as a cybersecurity engineer after graduating nearly a year ago has profoundly expanded my horizons in the tech sphere. With hands-on experience now supplementing my previous knowledge, I'm eager to delve into the recent enhancements I've made to my homelab setup.

My initial homelab setup was straightforward, primarily focusing on creating a vulnerable virtual Active Directory (AD) environment to simulate attacks and monitor their intricacies in real-time. However, my exposure to AD, Azure, and the comprehensive Microsoft Defender Suite piqued my interest in scaling my homelab further. The expanded version now integrates tools like Splunk, SecurityOnion, a pfSense firewall, and an array of cloud resources. This setup emphasizes a defense-first approach, highlighting critical aspects like incident response and threat hunting.

## What has changed?
As mentioned above, I've significantly reconfigured my homelab. First, I logically separated the virtual network using a pfSense firewall. I created 5 VMnets:

em0 - Internet access

em1 - Kali attack machine

em2 - AD domain (DC, user machines)

em3 - SecurityOnion and management

em4 - Network tap for IDS

em5 - Splunk server

Big thanks to Cyberwoxacademy for the homelab design - it provided a great foundation to then build out the cloud integration myself.

![Alt text](/assets/images/image-1.png)

## The cloud?
Now, with all of the system set up out of the way, I can start explaining how I plan on moving all of this stuff to azure. Microsoft has a great program called the Microsoft 365 Developer Program. This enables users to get access to 25 E5 licenses, which include Office 365, Defender for Identity/Endpoint, Azure Active Directory, and Intune to name a few.

![Alt text](/assets/images/image-3.png)

Utilizing these resources, I created a global admin account allowing for the management of user privileges and services. Additionally, I integrated a public domain, ensuring users aren't confined to connecting via a local domain (this will lead to VPN setup down the road and MFA to enable remote working capabilities). This setup primes the environment for an Azure integration, allowing for seamless authentication using Microsoft accounts, binding individual user identities with their respective devices for a streamlined experience.

## Conclusion
With this, we will start the journey of simulating a real corporate environment with capabiltiies that security teams use to defend against threats and attacks. This will be an invaluable resource to gain insight on how these services are set up and leveraged. Next post will go more in-depth on how I was able to link my "on-prem" infrastructure to the cloud and prep it for MS defender integration.