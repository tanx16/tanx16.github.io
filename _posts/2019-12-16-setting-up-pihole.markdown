---
layout: post
title:  "Setting up Pi-hole for the Winbook TW700 in 2019, Part 2"
date:   2019-12-16 14:52:57 -0800
categories: hacks
---

### Part 2: Setting Up Pi-Hole

Since the last post, I've (mostly) successfully installed Lubuntu on my tablet. The next step was to set up Pi-Hole, a DNS-based content blocker, on the tablet. Since I wasn't sure if there were any special steps for installing on non-raspian systems, I searched up a [quick tutorial](https://www.linuxincluded.com/install-pi-hole-on-ubuntu/), and began. See [the Pi-Hole site](https://pi-hole.net/) for more information about this tool.

I did the usual update, and copy and pasted `curl -sSL https://install.pi-hole.net | bash` in and let it do its thing. And immediately ran into a discrepancy. I ran through the usual prompts, and didn't end up with a "Installation complete" screen with the password for the admin console. But it seemed like things were installed, so I just manually set the password myself. I went ahead and checked if the console was accessible from the tablet, and of course it isn't working. `Connection Refused`.

Here we go again!

`pihole status: lighttpd not running`

Ok... why isn't it running?

I looked at the logs...

```
● lighttpd.service - Lighttpd Daemon
Loaded: loaded (/lib/systemd/system/lighttpd.service; enabled; vendor preset: enabled)
Active: failed (Result: exit-code) since Fri 2019-12-13...
Process: 24113 ExecStartPre=/usr/sbin/lighttpd -tt -f /etc/lighttpd/lighttpd.conf (code=exited, status=127)
```

Maybe if I restart the service it'll magically work?

`systemctl restart lighttpd`

Nope! Same error!

Luckily, with my google-fu I found [this post](https://discourse.pi-hole.net/t/lighttpd-does-not-start/6207/11).
To quote one of the posts I found, `It appears that the default lighttpd configuration tries to use fam (aka gamin), but there is no dependency on gamin. I would suggest either the package adds a gamin dependency, or the default configuration removes the fam dependency.`

So all I needed was to install `gamin`. It seems like this bug was fixed earlier this year, but perhaps the Pi-Hole installer didn't update their dependency list with the latest version.

This time I successfully got the admin console to show up locally. Whew! Ok, so now I know it works on my machine. But when I tried accessing it with my laptop, I couldn't see it. Well, let's try setting the DNS server on the router, and maybe it'll solve itself.

After changing the default DNS server... it still doesn't work!

Thankfully, my googling came in handy once again. There's actually a secret option that nobody told me about here:

![the default option](/assets/2019/pihole-settings.jpg){:class="img-responsive"}

Of course I didn't want it to only be available on the tablet itself. I changed it to be available for all other devices on the network, and it was all set up. I moved it next to the router, and it'll sit there quietly (until my next idea). Thanks for reading!
