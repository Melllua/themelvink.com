---
layout: post
title: HTB Sherlocks - Litter
subtitle: Investigation against DNS tunneling w/ Wireshark.
author: Melvin Karuga
categories: homelab
banner:
  video: 
  loop: true
  volume: 0.8
  start_at: 8.5
  image: "/assets/images/banners/wireshark-1.png"
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: HTB-Sherlocks
sidebar: []
---
## Introduction
This is a write-up of a very fun HackTheBox Sherlock room. If you haven't heard of HTB Sherlock rooms, they are meant to be on the defensive side of security. So instead of hacking, we are going to be analyzing attacks after an incident has occurred. 


## Backrground
Khalid has just logged onto a host that he and his team use as a testing host for many different purposes, it’s off their corporate network but has access to lots of resources in network. The host is used as a dumping ground for a lot of people at the company but it’s very useful, so no one has raised any issues. Little does Khalid know; the machine has been compromised and company information that should not have been on there has now been stolen – it’s up to you to figure out what has happened and what data has been taken.

## Given
- A .pcap file

## IoCs
- Signs point to a DNS attack.
- Attacker IP is sitting at 192.168.157.145 already inside the network, and has communicated with 192.168.157.144 several times.
- Attacker ip 192.168.157.145 has set itself up as the DNS for our host. Now the requests going from 192.168.157.144 is being intercepted by 192.168.157.145.

![Alt text](</assets/images/HTB-litter/Pasted image 20231120114353.png>)
- After further analysis, it seems that the strings in front of the URL's are Hex encoded: 

![Alt text](</assets/images/HTB-litter/hex-encoded-urls.png>)
- The first packet sent is decoded to: **!command (DESKTOP-UMNCBE7)** which signifies that a remote machine is controlling the host to execute commands (this desktop is likely our host that has been compromised).
 - After following the UDP stream of the first request, and running the contents through Cyberchef decoding the Hex code, we can see the following output:
 
 ![Alt text](</assets/images/HTB-litter/Pasted image 20231119203138.png>)

- With some cleanup of the text, we have:


```
!command (DESKTOP-UMNCBE7) 

C:\Users\test\Downloads> dir 
Volume in drive C has no label. 
Volume Serial Number is 503A-D127

Directory of C:\Users\test\Downloads

30/04/2023 11:28 <DIR> . 
30/04/2023 11:28 <DIR>  ..
06/05/2021 14:16 1,447,178 7z1900-x64 (1).exe 
01/03/2021 17:29 1,447,178 7z1900-x64.exe 
06/04/2021 12:38 479,396,152 Anaconda3-2020.11-Windows-x86_64.exe 
06/05/2021 14:13 4,763 b574b0333d81b25bdaf0b8bf147d3c607044beb1 (1).zip 
06/05/2021 14:07 4,763 b574b0333d81b25bdaf0b8bf147d3c607044beb1.zip 
06/05/2021 13:32 6,643 be3c0ced06b383dc321e49d1122cf0e53f8cf5db.zip 
04/06/2021 09:26 18,038,784 DB.Browser.for.SQLite-3.12.2-win64.msi 
28/05/2016 21:38 142,336 dnscat2-v0.07-client-win32.exe 
02/03/2021 11:12 3,613,174 ExplorerSuite.exe 
...more files

23 File(s) 794,320,161 bytes 
2 Dir(s) 24,566,407,168 bytes free

C:\Users\test\Downloads> whoami 
desktop-umncbe7\test
```

- There are several more UDP streams to follow, so we will uncover those soon but at least now we can see some of the attacker's malicious activity. They have launched a remote shell into our host and are now conducting reconnaissance. 

### Found answers thus far
![Alt text](</assets/images/HTB-litter/Pasted image 20231119210053.png>)

- At this point, we have uncovered what kind of attack, what attacker IP, and the first command that was executed. 

### Task 4
![Alt text](</assets/images/HTB-litter/Pasted image 20231119210640.png>)

- The next question asks us what DNS tunneling tool the attacker is using. Let's go through the next UDP stream to uncover this. After scrolling through the attacker's cmd activity, we stumble across this:

![Alt text](</assets/images/HTB-litter/Pasted image 20231119205637.png>)

- And there it is. The version is listed as **0.07**. Onto the next question.

### Task 5![Alt text](</assets/images/HTB-litter/Pasted image 20231119210727.png>) 

- To find this, let's filter the traffic to the cmd command that renames file, "ren": 

![Alt text](</assets/images/HTB-litter/Pasted image 20231119210544.png>)

- The attacker wanted to rename the file to 'win_install.exe' but failed to do so. Let's search for "dnscat" to see other cmd activity regarding this file.

![Alt text](</assets/images/HTB-litter/Pasted image 20231119212042.png>)

- From here it look like the attacker chose to name the file 'win_installer.exe' instead. 

### Task 6
![Alt text](</assets/images/HTB-litter/Pasted image 20231119210903.png>)

- For this task, let's think about what kind of cloud storage can be found on a windows machine. The first that comes to mind is OneDrive. After searching for this, we see the following:

![Alt text](</assets/images/HTB-litter/Pasted image 20231119211006.png>)

- There are 0 files found in this directory. 

### Task 7
![Alt text](</assets/images/HTB-litter/Pasted image 20231119212126.png>)

![Alt text](</assets/images/HTB-litter/Pasted image 20231119212231.png>)


- Knowing it was on the C: drive, I filtered by this and found the following file. This is our answer.

### Task 8
![Alt text](</assets/images/HTB-litter/Pasted image 20231119212325.png>)

- There is a massive amount of data, exactly  239,714 bytes, so going through every single line in "user_details.csv" would be inefficient. Let's look at the output of the .csv to see if we can dig anything up.

![Alt text](</assets/images/HTB-litter/Pasted image 20231119232033.png>)

- Here we see that the attacker used the type command to list out all the data found within the file. 

![Alt text](</assets/images/HTB-litter/Pasted image 20231119232225.png>)

- The output from the above command shows the columns that information follows. Knowing that the job is the first column, let's see if the number is displayed just before.

![Alt text](</assets/images/HTB-litter/PII_Data.png>)
- As we can see, there is a number displayed right before each entry. At the end of the file, the last number we see is 721, which is our answer! This room has been completed!

## Conclusion
This was a great room. I learned a new technique used by threat actors and was able to see it executed on the other side. Wireshark is such a great tool and provides so much visibility, you just have to know what you're looking for. 

The Sherlocks rooms are a great way to get some fun experience with responding to alerts and attacker activity!