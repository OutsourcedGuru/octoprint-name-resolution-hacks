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
