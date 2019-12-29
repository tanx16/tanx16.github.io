---
layout: post
title:  "Setting up Pi-hole for the Winbook TW700 in 2019, Part 1"
date:   2019-12-14 14:52:57 -0800
categories: hacks
---

### Introduction

#### What's [Pi-hole][pihole-site]?

I've had an [ancient tablet][winbook-info] from around 2015 lying around, and I had always wanted to do something with it. It's a basic 7-inch tablet that has 1GB of ram, 16GB of MMC storage, and a wimpy Intel Atom processor preloaded with Windows 8. As a bonus, it came with a full-size USB-A port that was the primary reason I picked it over the other cheap tablets at the time. At that point, I was only using it as a cheap way to load my keyboard configuration software. I had found a guide to load Ubuntu on it, but never got around to it until I had the idea to set up Pi-hole on it. I was also motivated by a friend's unused Google Home Mini, which conveniently came with a micro-usb charger that worked with the tablet.

### Installing Linux

#### Requirements:
- A USB stick (4GB or more)
- Windows
- A USB hub, mouse, and keyboard
- A USB wifi adapter/ethernet adapter

I started off setting up a bootable Ubuntu drive. While looking briefly at [the guide][infosoda-guide], I figured that I could just use [Etcher][balena-etcher], a nice open-source tool for flashing Ubuntu images. So I grabbed the latest Ubuntu Desktop iso, downloaded Etcher, and flashed my usb drive. I plugged it into the tablet, set up the boot order so it would use the USB first, and booted. The Windows splash screen greeted me.

Turns out I didn't read the instructions closely enough. There's a particularly fiendish issue about the tablet - it supports 64 bits, but the EFI is only 32 bits. This meant that I couldn't just load the 64 bit version of Ubuntu on it since it didn't have a 32 bit bootloader. If I had read the instructions closer, then I would've seen it. :( So I take my drive and grab a [boot file][32bit-loader] that someone compiled and try to put it into the EFI/BOOT/ folder in my bootable USB.

`Error: Insufficient Disk Space.`

???

I decided to just delete one of the existing files (`bootx64.efi` IIRC) to make some space and replaced it with the `bootia32.efi` file. The bootloader can't use a 64 bit file anyways, right? I go back and retry the boot process. This time, I didn't get dropped back into Windows. Instead, I got a grub2 rescue prompt. Hmm...

After fiddling with the prompt I figured that I was way over my head. I decided that the deleted file was actually important. After googling the insufficient drive space error, I realized that it was Etcher that had caused the issue. I ended up borrowing my friend's Windows computer to use [Rufus][rufus] and do the process properly. This time, I could actually copy in the file without any issues. Yes! I proceeded to plug in the drive once again and rebooted.

I was greeted by the Ubuntu install prompt this time, and I let out a sigh of relief. Would I finally be done with this obnoxious installation process? Nope. When proceeding with the installation, the tablet started lagging horribly - and then froze. It seems like Ubuntu has gotten a bit more bloated since the author had written his guide. After waiting 20 minutes to choose a language for the installation, I decided that it was a lost cause. The poor tablet was clearly too weak. Instead, I would try to install Lubuntu. Looking at the [minimum requirements][lubuntu-requirements], it seemed like a good fit. I went through the motions once again, praying to the Linux gods that I wouldn't run into any compatibility issues.

Things went well, and I landed back into the installation screen from the live Lubuntu drive. I was very impressed by the lack of lag or stutter as I moved my mouse across the screen. Following the guide, I set up 3 partitions, one for the EFI, one for swap, and one for the rest. I clicked the go button, and left the tablet alone for a while for it to complete its mission.

I came back a bit later to see a nice screen declaring success. Hmmm. I wasn't expecting that. Just to make sure that it worked, I unplugged the installation drive and rebooted the machine. It worked! It seems like in the 4 years since the guide was written, the new kernel that came with Lubuntu was able to work despite the hack we did for the 32 bit EFI file. No complaints here. The wifi seemed to work, and it looked like I was all good. But wait: the touchscreen was inverted! I gotta fix that.

### Fixing the touchscreen

When I tried moving the cursor with my finger, I realized that the mapping was the exact opposite of what I wanted - if I touched the top right, the cursor would appear at the bottom left. If I swiped to the left, the cursor would go right, and so on. I looked up how to calibrate the screen and found a utility called `xinput_calibrator`. I installed it, ran the calibration, and... nope. Didn't work. Google to the rescue? I found a [post][touchscreen-post] that seemed to be the exact same issue I had. I went to `usr/share/X11/xorg.conf.d/`, but there wasn't a `10-evdev.conf` file. However, there were files for `wacom` and `libinput`, which apparently is an alternative to `evdev`. I tried to do something similar as the post suggested by putting in the `Option "InvertX" "true"` lines in the relevant `Identifier "libinput/wacom touchscreen catchall"`, logged out/in, and it didn't work. I tried a bunch of different combinations, to no success. Eventually, I decided that it wasn't worth the fix as I would almost never use the touchscreen in the first place. If anybody can help explain what the issue is, please message me!

### Next steps

In the [next post]({% post_url 2019-12-16-setting-up-pihole %}), I'll be describing how I set up Pi-hole and the issues that I ran into along the way. See you next time!

[pihole-site]: https://pi-hole.net/
[winbook-info]: https://www.thurrott.com/hardware/1484/first-look-winbook-tw700-tablet
[infosoda-guide]: https://web.archive.org/web/20180710011952/http://infosoda.com/ubuntu-tw700-1/
[balena-etcher]: https://www.balena.io/etcher/
[32bit-loader]: https://github.com/jfwells/linux-asus-t100ta/tree/master/boot
[rufus]: https://rufus.ie/
[lubuntu-requirements]: https://lubuntu.net/lubuntu-18-04-bionic-beaver-released/
[touchscreen-post]: https://forums.linuxmint.com/viewtopic.php?t=270801

