---
layout: post
title: MDM via Azure - Part 2
subtitle: Securing BYOD/corporate devices in an organization.
author: Melvin Karuga
categories: homelab
banner:
  video: 
  loop: true
  volume: 0.8
  start_at: 8.5
  image: "/assets/images/banners/intune.jpg"
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: Azure-homelab
sidebar: []
---

# MDM for iOS via Intune - Part 2

## Goal: iOS devices enrollment to access O365 applications.

The first part of MDM for iOS involved setting up the prerequisites, such as creating the certificate and the enrollment profile. To actually utilize these properties, we must create policies forcing these requirements upon users accessing O365 applications.  
<br/>We will need to:

- Create a **Conditional Access** policy requiring that devices accessing O365 apps need to be compliant in Intune.
    - This step alone forces the user logging into any O365 apps to enroll their device via the **Company Portal** app on iOS.
- Add an additional **Device Compliance** policy setting some additional security measures to ensure that compliant devices meet certain requirements.
- Set an **App protection** policy to set requirements for using any O365 application.

The goal here is to have a more secure BYOD setup. By configuring and creating these policies, we will require users to enroll their device **only** if it is compliant. We can then set the compliance requirements; only after this will the users be able to access the applications. However, even **after** all of the prior steps, we have the App protection policy to ensure that any actions taken within the app are backed by the security policy.

## Conditional access policy

Now, we will need to create a CAP to force a user to enroll their device into Intune in order to access any O365 apps under their company identity. This is especially useful because in tangent, we can create a device compliance policy setting the requirements to be compliant.

Firstly, the CAP. Navigate to Devices → Conditional Access → Create new policy.

- Let’s select the applications we want to grant access to. I will choose **Office 365** here.![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-2/1.png)
- Scroll down to conditions and set the device platform to be **iOS**.
- Finally, select grant and choose **Require device to be marked as compliant**.![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-2/2.png)

Now, we’re set. As soon as this CAP is deployed, users logging into any O365 application will get a pop up guiding them to enrolling their device.

## Device Compliance Policy

Enrolling the device alone is not enough. However, now that we have the devices being enrolled, we can fine tune our compliance policy to further secure what devices are accessing corporate data.

- First, navigate to **Devices → iOS → Compliance → Create Policy.**
    - Jailbroken devices: Block
    - Require the device to be at or under the machine risk score: Low (ideal for corporate or devices that are fully managed. If a device is deemed a risk by Microsoft Defender for Endpoint, this device will no longer be compliant and users won’t be allowed to access O365 applications).
    - Require a password: Require
        - Simple passwords: Block
        - Password expiration (days): 90

There are several other rules to set that I will not be covering, these are just a handful of important rules.

## App protection policy

Finally, we’ll set up the app protection policy allowing us to set parameters within the actual applications.

- Navigate to **Apps → App protection policies → Create Policy.**
    - Target policy to: All Microsoft Apps
    - Configure data transfer, encryption, and functionality rules. Most notable rules that I configured were only allow copy/pasting from managed applications, PIN for access and block backing up organization data to iTunes/iCloud.

## Closing notes:

Now that we have set up all of these policies, there is a secure user flow. For example, a user will try to access outlook on their iPhone. Upon login, there will be a prompt to enroll their device. After, the compliance check will start and if the device is compliant, the user will have access to the managed apps. Within the apps, there will be safeguards to further secure company data.

This was a lot of fun to set up and see it develop real-time. In my study for the SC-300, I wanted to simulate what a real company would do to implement MDM. I hope that anyone reading this was able to learn something new!

---

## References

- [Microsoft article](https://learn.microsoft.com/en-us/mem/intune/fundamentals/deployment-guide-enrollment-ios-ipados) that goes over iOS enrollment