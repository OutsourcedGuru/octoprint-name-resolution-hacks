# octoprint-name-resolution-hacks
Working around name-resolution problems for your OctoPrint-based 3D printer

> [OctoPrint](https://github.com/foosel/OctoPrint) is the amazing web interface for controlling 3D printers.

> [Bonjour](https://en.wikipedia.org/wiki/Bonjour_(software)) is the hostname-broadcasting protocol which is used by OctoPrint so that anyone using an OSX-based computer can refer to their printer by its hostname.

> ARP is a protocol for caching network adapter addresses versus their bound IP addresses.

> DNS is the UNIX/Linux way of turning a hostname like `octopi.local` into an IP address.

> WINS is the Microsoft way of turning a hostname into an IP address, treating it as if it were a NETBIOS hostname.

## Overview
It appears that most Windows- and Linux-based users can't easily get to their OctoPrint printer and resort to using the IP address of that printer in order to connect via their browser.

### Classic Workaround
The current wisdom is to simply: _"install iTunes"_. Behind-the-scenes, this will also install the Bonjour name-resolution service and suddenly, it "just works". Unfortunately, this then inserts the Bonjour service into the Microsoft Windows setup and I can tell you from years' of experience as an I.T. Manager that this causes other problems for the user and they're too numerous to list.

### Affected Operating Systems
I can confirm that Microsoft Windows (without iTunes) and Ubuntu cannot by default see an OctoPrint installation unless you use the IP address.

### Quick Workaround
At a DOS or Terminal prompt, the standard PING command will broadcast on your local network and can place the OctoPrint's hostname into something called your ARP cache.

```
$ ping octopi.local.
PING octopi.local. (192.168.0.20) 56(84) bytes of data.
64 bytes from 192.168.0.20: icmp_seq=1 ttl=64 time=5.79 ms
...
```

Once this has succeeded, the Windows- or Linux-based user may then just open their browser and try again, using the hostname `http://octopi.local` and this should work until the next reboot of either workstation or printer.

### Update
It looks like this will stay cached for some time. Even after clearing the `arp` cache and rebooting, the browser itself also appears to be caching the DNS lookup as well so that's good news.

### Another Workaround
On a Windows-based computer and opening a `cmd` shell as Administrator, it's easy enough to edit the `c:/windows/system32/drivers/etc/hosts` file to add an entry for your computer but you would need to know its IP address first (editing for your printer's address):

```
192.168.1.20	octopi octopi.local
```

### Better Solution
The proper way to do something like this is to get the Raspberry Pi 3 (or similar hosting computer) to broadcast its NETBIOS name, making Windows computers happier.

> Samba is a royalty-free Linux package that allows computers to behave like Microsoft servers.

```
# It's important to run this next command before any attempt to update using apt-get later
$ sudo apt-get update
# Save a copy of your existing configuration file; you can always revert without ill effect
$ sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.save
$ sudo apt-get install samba samba-common-bin
```

Now it will be necessary to edit the Samba configuration file, find the **[global]** section and add these lines.

```
$ sudo nano /etc/samba/smb.conf

[global]
  workgroup = WORKGROUP
  wins support = yes
  netbios name=octopi
  server string=OctoPrint on Raspbian
```

Having saved the file and exited (Ctrl-O, Enter, Ctrl-X), now parse it to verify that SMB likes what you did.

```
$ sudo testparm /etc/samba/smb.conf
```

Assuming that it's happy, now reboot to load those changes.

```
$ sudo reboot
```

#### Testing in OSX
To verify that your OctoPrint is now broadcasting, in Finder, press Cmd-K and then the Browse button. Although it appears here, we didn't share any folders so you can drill into it. This should suffice for testing the NETBIOS broadcast, though.

#### Testing in Windows
Perhaps the best way to test this is to try visiting `http://octopi/` from your browser, noting the absence of `.local` as would normally be seen in these URLs.

|Donate||Cryptocurrency|
|:-----:|---|:--------:|
| ![eth-receive](https://user-images.githubusercontent.com/15971213/40564950-932d4d10-601f-11e8-90f0-459f8b32f01c.png) || ![btc-receive](https://user-images.githubusercontent.com/15971213/40564971-a2826002-601f-11e8-8d5e-eeb35ab53300.png) |
|Ethereum||Bitcoin|
