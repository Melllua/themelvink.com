---
layout: post
title: MDM via Azure - Part 1
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

# MDM for iOS via Intune - Part 1
This is the first part of a 2-part write-up that will overview how I was able to set up MDM (mobile device management) through Intune. This project opened my eyes to the power of conditional access and MDM. 

## Goal

In order to secure BYOD devices, we will create several policies and profiles to create a secure environment for users to access corporate data without extracting sensitive items.

**We will need to:**

- Set up an **Apple MDM push certificate** \- this will allow management of Apple devices with Intune.
- Create an **enrollment type profile** - this will set the way users will enroll their devices (installing the MDM push certificate to enrolled devices for management).

## Apple MDM Push Certificate Setup

In the Intune admin center, navigate to **Devices → iOS devices → Enrollment**. This is where the Apple MDM Push Certificate can create. You will be required to download a CSR, login to your apple account, paste the CSR and generate the certificate. Again, the purpose of this is to allow _management of iOS devices via Intune_. Otherwise, there would be no management profile for the iOS device to download, disallowing any control over the device.

After the certificate is generated, a status of **Valid** will appear:

![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-1/1.png)

## Enrollment Type Profile

Now that we have our certificate, we can push this to devices that request to be enrolled. How? By setting up an **enrollment type profile**.

1.  Navigate to **Devices → Enrollment → Enrollment types → Create profile.**

2.  Create and name the profile:![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-1/2.png)

3.  Choose the enrollment type:![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-1/3.png)
This will change the user experience for enrolling a device. To test this, I tried both **Determine based on user choice** and **Device enrollment with Company Portal.** The former gave the option to choose whether the device is corporate or personally owned. If personal is chosen, the user can choose to secure the entire device or just the managed applications. Below are two screenshots showing the user experience for both, respectively:
    
     
    **User choice:**
    
    ![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-1/4.png)

![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-1/5.png)

### **Device enrollment:**

![Alt text](/assets/images/MDM-for-iOS-via-Intune-Part-1/6.png)

As shown, device enrollment only allows that form of enrollment, whereas user choice gives much more flexibility.

Finally, select the users/groups to be added to the enrollment profile and create the profile.

## Closing notes

Now that we have created both the certificate and enrollment profile, we can proceed to part two which will **force** users to enroll using the enrollment profile when attempting to access any O365 application.

---

## References

- [Set up user enrollment with Company Portal](https://learn.microsoft.com/en-us/mem/intune/enrollment/apple-user-enrollment-with-company-portal?WT.mc_id=Portal-Microsoft_Intune_Enrollment)