---
title: Xploring Xbox
slug: xploring-xbox
createdAt: 2020-04-04T01:55:13.000Z
updatedAt: 2023-07-29T17:24:36.000Z
publishedAt: 2020-04-04T02:16:01.000Z
FeatureImage: 
---
### A brief synopsis of my Xbox research.

‌             ‌

### Xbox Live Messaging - RCE Via Embedded Content

The Xbox messaging system supports the embedding of protocols for one-click redemption of service messages containing 5x5 game codes, viewing shared images, club invites, etc. By crafting my own service message whilst chaining another vulnerability that allowed for remote custom WinJS execution (i.e. [ToastMyConsole](http://toastmyconsole.com/\)), I was able to construct a message allowing remote code execution on any console, given the stipulation of the user interacting and clicking on the button.

```json
{"messagetype": "Xbox/Service", "content": "{\"message\":\"Your toast is done\",\"actions\":[{\"id\":0,\"vuiGui\":\"Your Toast is Done\",\"vuiAlm\":null,\"vuiPron\":null,\"vuiConf\":null,\"launch\":\"*\",\"launchType\":\"DeepLink\"},{\"id\":1,\"vuiGui\":\"Your toast is done\",\"vuiAlm\":null,\"vuiPron\":null,\"vuiConf\":null,\"launch\":\"*\",\"launchType\":\"DeepLink\"}]}", "clientmessageid": "*", "from": "https://bn2-client-s.gateway.messenger.live.com/v1/users/xuid(*)/contacts/28:xbox_service_message", "conversationLink": "https://bn2-client-s.gateway.messenger.live.com/v1/users/xuid(*)/conversations/28:xbox_service_message", "conversationid": "28:xbox_service_message"}
```

### Xbox Edge Browser - File System Exposure

Edge’s address bar allowed access of local files on the device via the [File:\\\](File:\\) protocol. This functionality is used for viewing local PDFs, etc. However, as the Xbox file system isn’t normally accessible, this allowed for the dumping of Xbox binaries. Any valid path would trigger a download prompt, copying the file to the user's download folder accessible via the File Browser application.

**Side Note**: The File Explorer application was disabled due to ["Limited Usage"](https://twitter.com/xboxinsider/status/1202357755140546560)

![](__GHOST_URL__/content/images/2023/07/1-1.png)

### Partner Center - Sandbox Shambles

Due to an oversight with the client-side authorization system, I was able to create and take over [sandboxes](https://docs.microsoft.com/en-us/gaming/xbox-live/test-release/sandboxes/live-setup-sandbox) belonging to publishers, essentially merging them into my account, allowing access to all of their products within said sandbox.

![](__GHOST_URL__/content/images/2023/07/2-1.png)

### Microsoft Store - App Package Pulling (FE3)

By reverse engineering the Microsoft Store client, I was able to develop tools to pull packages for almost any app/game, be it internal or paid.

See my [GitHub](https://github.com/TitleOS/StoreLib) repo for a more in-depth look into the API and its capabilities.

### Kiosk Mode - Abusing MSXB\_KIOSK.xvd

Activation of kiosk mode is triggered on startup via the presence of _"MSXB\_Kiosk.xvd"_ on an external USB. The console will then check the XVD’s [content-type](https://github.com/emoose/xvdtool/blob/468a99094e1b24f9310551333ce4b7db500cdc18/LibXboxOne/XVD/XVDEnums.cs) to make sure it matches that of "Kiosk” before mounting. It is possible however to change the content-type of any [Green-encrypted](https://wiki.xosft.dev/faq/#where-and-how-do-we-get-the-keys) XVD to that of “Kiosk”. Renaming the modded XVD to MSXB\_Kiosk and placing it on a usb will cause the console to mount the XVD on next boot, exposing the contents.

### Windows Update – Ability to Pull Canary Builds

The Windows Update ( **FE3**) service failed to perform authorization checks against the Device ID requesting a [Canary](https://en.wikipedia.org/wiki/Windows_Insider#Rings) build, allowing me to download IoT Canary builds for all 4 supported arches.

### Xbox Flighting – MITM via Fiddler

By making use of [Fiddler Web Debugger](https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/uwp-fiddler) in [Dev Mode](https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/getting-started), it's possible to edit incoming flighting, including enabling internal flighting, enabling various different LiveSettings,etc.

**Registry path**: _HKLM\\Xbox\\Software\\Microsoft\\Durango\\LiveSettings_

### Network Transfer Manager – Cross Mode Installation

By reverse engineering the network transfer manager process, I was able to develop a tool to emulate a console with NT ( [Network Transfer](https://beta.support.xbox.com/help/hardware-network/console/enable-network-game-transfer-xbox-one)) enabled. Utilizing this, I'm able to install developer packages to Retail Mode, and retail games/apps to [Developer Mode](https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/getting-started).

**Service location**: _C:\\Windows\\System32\\NetworkTransferManagerService.exe_

### Live Domain – PII Disclosure

The [storage.live.com](https://storage.live.com/) profile endpoint failed to perform authentication checks on the requesting user, allowing one to request personal details for any MSA, including name, address and OneDrive contents.

### Developer Domain – App Name field vulnerable to XSS

[UDC](https://developer.microsoft.com/en-us/) was vulnerable to XSS via crafting a specific string and setting it as the application's name, resulting in a successful cross-site scripting attack.
