---
date: 2012-01-29 07:24:44+00:00
layout: post
title: Differentiating Between Windows Home and Pro
tags: nmap nse psexec windows wmic
---

In planning for an Active Directory network,  I need to figure out how many Windows Home versions there are on our network so I can calculate the costs of upgrades.  From what I've gathered in using Nmap's "-O" option, it will only tell give me results such as "Windows XP" or "Windows 7"; it won't detail specifically if it's Home or Pro.  I posted a new thread on [EthicalHacker.net](http://www.ethicalhacker.net/) (highly recommend this site/forum, by the way) and posted on Twitter to see if there was a way to figure this out remotely so that I didn't have to visit each machine (if you haven't also gathered, this process will be helping create an updated database of machines on the network as well).

I got 2 replies back rather quickly on EH-net (thanks dynamik and tturner!): one suggesting to use "psexec systeminfo" and the other using WMIC.  @JGamblin on Twitter also suggested "psexec systeminfo".  Here's the psexec and the WMIC suggestions:
```
psexec systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```
```
wmic /node:machinename /user:username os get | find "Windows Home"
```

It was also said that "Windows Home" may not be the exact string in the WMIC query there, but that's easily tweaked.

I've played around with the pstools before so I was a little familiar with them, but didn't know about the systeminfo command of psexec.  As for WMIC, I've never used it, let alone heard of it.  Research and testing time!

I gave both a whirl to check the output and if they'd work given our current network setup.  I found that they both worked, except that given the way both of these methods queried the remote systems, they required valid usernames and passwords on the remote systems...and this would be impossible to do with the way the machines are currently setup: no standard admin account on every machine (something else I know I'm going to have to remedy before implementing AD - it's on the list).

Not too long after, I got another reply on EH-net (thanks hell_razor!) suggesting an nmap NSE script:
```
nmap -sS -p 445 --script=smb-os-discovery.nse target_ip
```

I got varied results with this, but I think it depends on the operating system.  It would show "Home" or "Professional" for Windows Vista or Windows 7 systems, but not when it came to Windows XP.  Regardless, since psexec and WMIC require credentials to get the OS info, I decided to go the nmap NSE script route.

![](/images/win7_smb-os-discovery.png)

![](/images/winxp_smb-os-discovery.png)

If you couldn't tell already from the script name (smb-os-discovery), it works by connecting to each system via the SMB protocol over port 445 or 139 using the anonymous account if no account is specified (I just used the anonymous account).   This script retrieves not only the OS, but the company name, domain/workgroup, time on the remote system, etc.  Some good information not just for my purpose here, but also for attackers.
