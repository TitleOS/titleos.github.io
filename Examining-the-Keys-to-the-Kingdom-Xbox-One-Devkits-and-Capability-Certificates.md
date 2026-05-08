---
title: Examining the Keys to the Kingdom - Xbox One Devkits and Capability Certificates
slug: keys-to-the-kingdom
createdAt: 2020-06-04T17:32:25.000Z
updatedAt: 2022-02-15T16:12:21.000Z
publishedAt: 2020-06-04T18:52:01.000Z
FeatureImage: 
---
Most Xbox One devkits start out life as an off the shelf retail console (with the exception of special SP kits I will cover below). An Xbox One's devkit type and abilities or capabilities are defined by a file stored on the flash called " [certkeys.bin](https://xosft.dev/wiki/xbox-boot-file-system/)." \- certkeys.bin is referred to as the "Capability Certificate" by the Platform Security Processor (PSP) and it will be referred to as such from here on out.

> It should be noted that capability certificates are locked to a particular console via the SOCID (Reported as Console ID in settings). The entire certificate is then signed to prevent tampering.

A capability certificate defines what capabilities an Xbox One console is allowed to enable (This is regulated via the PSP and to a degree, HostOS.) The capabilities range from enabling Devmode and the respective developer services, ignoring requests to blowing e-fuses, HostOS telnet and Retail debugging, and much more.

### There are 5 standard Xbox One devkit types:

> Devkit types refer to a standard collection of capabilites. With every devkit mentioned below, the certificate will be identical capability-wise to another one of the respective type.

**UWA Devkit (Formerly SRA Devkit)** \- This is the "$20" devkit, and allows for the development and debugging of UWP apps on SystemOS. This is the most common devkit certificate by far and expires every 24 hours.

**Game Devkit** \- This is the devkit most people think of when they picture a Xbox One devkit. Game devkits in addition to the UWA capabilites, also allows for the activation of the ERA VM partition in Devmode. This allows for the development, debugging and profiling of XDK and GameCore based games. This is also the lowest devkit that is able to connect to the PC XDK/GDK, due to the XTF services running on TitleOS, GameOS (GameCoreOS) and SystemOS.

**MS Internal Devkit** \- This is where things get exciting. In addition to all the capabilities above, MS internal devkits enable a SYSTEM level telnet shell on all 3 (4) operating systems (SystemOS, GameOS, and HostOS), however they are unable to run "Green" (Production) content like most games, and production builds of the Xbox OS. These certificates _generally_ never expire.

**SP (Security Processor) Devkit** \- SP devkits are the top-tier of the standard capability certificates. In addition to all the aforementioned abilities, they also allow for custom code to be ran on the PSP/Security Processor. This includes debug versions of the bootloaders like 2BL, and PSP firmware such as 1SP. Due to this ability, most SP devkits are not converted from standard retails and instead prepared from a virgin factory board (which has yet to have the PSP programmed.) These certificates _also generally_ never expire.

**In addition to those standards, there are a couple more devkit-types worth mentioning:**

**Retail Devkit** \- These devkits are Green versions of MS internal devkits. This allows them to debug _retail_ games, run production versions of the Xbox OS, etc while all retaining the aforementioned mentioned capabilities.

**Godbox** \- Godboxes are the absolute top tier of devkit, capable of kernel-mode and user-mode debugging on all Green operating systems while connected to Production Xbox Live. There are two subtypes of Godboxes, Full Godboxes and Restricted Godboxes. Full Godboxes are retail or lower tier developer consoles converted into a permanent Godbox, in addition to having JTAG and serial headers soldered onto the motherboard. Restricted Godboxes are the exactly same as above, except for the fact that they require a constant connection to Microsoft's CORPNET to activate it's developer functions.

**Godbox Certificate** \- A magical capability certificate ($Diagnosis/debug.bin on a NTFS USB) that will temporaily activate a retail console as a limited Godbox for 24 hours. Kernel/User-Mode debugging is only possible on SystemOS and GameOS, _not HostOS_, and the temporary kit requires authentication against Live.

With that explanation covered, let's get into certkeys.bin's file structure and how to read the capabilities from your certificate.

The header contains the file magic "CP" and the certificate's targeted SOCID. Starting at offset 0x80, the capabilities are defined as 2 byte reversed entries. For example, 0x2001 - SRA\_DEVKIT, which would be written as 01 20 in the certificate. An SRA Devkit's certificate would contain 0x2001 and 0x2002 respectively, while an MS Internal Devkit would contain 0x6001 and 0x6002 to enable host telnet.

A complete list of capabilities and their values can be extracted from "XboxOneExtensions.dll" at "C:\\Windows\\DefaultApp\\XboxOneExtensions.dll" on an Xbox. A more detailed file structure breakdown and complete list of capabilities is also available at [https://xosft.dev/wiki/certificates/](https://xosft.dev/wiki/certificates/).
