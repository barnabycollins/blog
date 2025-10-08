---
layout: post
title: "How to set up a computer to boot into a browser kiosk in 2024"
date: 2024-08-18 13:46:00 +0100
categories: linux
---

Note: This post was written following my experiences setting up mini-PCs to run
my shop window application in the repair shop where I volunteer. The
instructions are also applicable to any other situation where you need to boot
to a fullscreen browser though.

The outcome of this guide should be a computer that boots straight into the
Ubuntu desktop upon receiving power, hides the mouse cursor, and launches a
fullscreen Firefox window pointed at your chosen URL. The computer will be safe
to unplug thanks to a read-only file system, and will keep its screen on
indefinitely. In my case, the URL is
[window.barnabycollins.com](https://window.barnabycollins.com); more information
on this project can be found in its [project
documentation](https://github.com/barnabycollins/shop-window/blob/main/README.md).

# Step 1: Installing Ubuntu

Ubuntu is a free operating system based on Linux. It's more lightweight,
reliable (arguably) and customisable than Windows, so is an ideal option for
this use case.

To install it, you'll need three things:

- A computer to set up the Ubuntu installation media
- The computer that you want to put Ubuntu on
- A USB flash drive

You'll probably want a capacity of at least 8GB (preferably 16GB) for the
latter, and the first two items can be the same computer if necessary.

Ubuntu have their own instructions for this, so I won't repeat them here - you
can find them at
[ubuntu.com/tutorials/install-ubuntu-desktop](https://ubuntu.com/tutorials/install-ubuntu-desktop).
I'd recommend using the latest LTS version - at time of writing, this is 24.04
LTS. You only need the default selection of apps, and it is up to you whether to
install the proprietary software - for simplicity I have chosen not to.

Make sure to un-tick the 'Require my password to log in' box when setting up
your login details. I'd recommend still setting a password, though - this will
ensure that only authorised users can change the computer's settings in a
meaningful way, as it will prompt you for it in these cases.

You don't need any additional applications from App Center, or an Ubuntu Pro
subscription.

# Step 2: Installing necessary packages

The software we need on top of Ubuntu itself is:

- Firefox
- `unclutter`
- `overlayroot`

I'll go into more depth on what the latter two are for later - I'd suggest
reading this article in full before installing, as you may not **need** them for
your use case.

Firefox should come with Ubuntu - if not, follow [the official
instructions](https://support.mozilla.org/en-US/kb/install-firefox-linux#w_install-firefox-deb-package-for-debian-based-distributions).

The other two can be installed by opening a Terminal window and typing:

```sh
$ sudo apt install unclutter overlayroot
```

You'll be prompted for your password, and probably also to confirm that you want
to actually install the packages.

They won't do anything until we configure them, so that's what the next steps
are going to be about!

# Step 3: Setting up the computer

## 3a: Stopping the screen from turning off

This setting can be found in the Ubuntu settings, probably under 'Power'. Make
sure that the screen will never go blank, and the computer will never go to
sleep.

## 3b: Configuring Wi-Fi if necessary

If your computer will be pointed at a website on the Internet, you'll need to
connect it to a network on location. If it's not already configured, make sure
you're connected to the right network. If the network isn't in the same place
you're setting up the computer, you can still set it up in advance - just select
the 'Connect to a hidden network' option and fill in the settings as if the
network was there. The computer will attempt to connect for a little bit and
fail, but it will still have saved the network credentials.

## 3c: Enabling Unclutter

The `unclutter` package we installed before is a piece of software that hides
the mouse cursor when it's not being used. It's very handy for this use case, as
without it Ubuntu will show the mouse cursor all the time.

You can enable `unclutter` by launching the 'Startup Applications' app, and
adding a new entry. The command to run is:

```sh
unclutter -idle 5 -root
```

The number after `-idle` tells `unclutter` how long to wait before hiding the
mouse, in seconds. So, if the mouse isn't touched for 5 seconds, `unclutter`
will hide the mouse.

This process will launch `unclutter` when the computer starts up, but we're not
done yet. Recent versions of Ubuntu ship with the Wayland window manager enabled
by default, but `unclutter` only supports X11. This is easy to fix, as both
Wayland and X11 come installed with Ubuntu and you can easily switch back. There
is no equivalent to `unclutter` for Wayland at the moment.

To make Ubuntu use X11 instead of Wayland, edit the file `/etc/gdm3/custom.conf`
using either the Ubuntu text editor or a terminal-based file editor like
[`nano`](https://www.nano-editor.org/), which comes with Ubuntu. You should see
a line that says:

```sh
#WaylandEnable=false
```

The hash is 'commenting out' this line, effectively disabling it. Uncomment the
line by deleting the hash, leaving:

```sh
WaylandEnable=false
```

Save the file and restart the computer. Once done, you should find that your
cursor disappears, only reappearing when you move the mouse.

## 3d: Enable SSH

If your kiosk computer is going to be hard to access, you may wish to enable
SSH. This allows you to remotely control it across the network through the
command line. I chose not to do this, but you can enable it easily and there are
many guides on how to do this elsewhere. Make sure to include your Ubuntu
version when searching.

# Step 4: Actually enabling the kiosk web browser

Most modern browsers have a kiosk mode; I chose to use Firefox as it is what I
use anyway and it comes pre-installed with Ubuntu.

Enabling the browser is pretty simple - just open the "Startup Applications" app
as before, and add a new entry. The command should be:

```sh
firefox -kiosk -private-window "https://your-url.com/whatever/something?cool"
```

The `kiosk` flag fullscreens the browser and makes it impossible to
un-full-screen without using system shortcuts like Alt-F4, and the
`-private-window` means the window will open in private mode (otherwise known as
'incognito' in other browsers). You can omit these flags if they don't match
what you're after.

There's more information on kiosk mode in Firefox
[here](https://support.mozilla.org/en-US/kb/firefox-enterprise-kiosk-mode).

Restart the computer. You should now find that the browser launches as expected!


# Step 5: Setting up a read-only file system using `overlayroot`

If you plan to turn your kiosk on and off using a mains timer, or just turn it
on and off at the plug, I'd suggest setting up your computer to use a read-only
file system. What this does is:

- Duplicate the computer's file system as soon as it turns on
- Make the computer use this copy at all times once on

The copy is lost each time the computer turns off, but the original copy is
retained as-is. This means that, even if the computer is turned off at a
critical moment and something is broken, it will re-duplicate the original,
unchanged copy of the file system as soon as it turns back on again.

Essentially, it will ensure that you can connect and disconnect your kiosk
computer's power supply however you like, without any risk of corrupting
anything and causing it to fail to boot the next time.

## Step 5a: Disabling software updates

If you're using a read-only file system, chances are you will want to disable
software updates. Otherwise, your computer will apply the same updates every
time it turns on and then lose them when it's turned off again. Updates can also
cause unwanted prompts to appear on the screen, such as 'restart this computer
to update to blahblah'.

Obviously, this presents a security risk, so you may wish to have a plan to
periodically log into your machines and upgrade the software they are running.

To disable software updates, do the following:

- Launch the 'Software & Updates' application.
- Navigate to the 'Updates' tab.
- Set 'Automatically check for updates' to 'Never'. This ensures that Ubuntu's
  Software Updater programme won't run unprompted.
- Execute the command `sudo snap refresh --hold`. This ensures that applications
  installed with the `snap` system (including probably Firefox) won't be upgraded
  automatically.

## Step 5b: Disabling `grub`'s recordfail timeout

Enabling overlayroot often causes `grub`, Ubuntu's bootloader, to show itself when
booting and impose a 30-second timeout. If this isn't desirable for you, just do
the following:

- Edit `/etc/default/grub`.
- On a new line, below the line that looks like `GRUB_TIMEOUT=0`, add
  `GRUB_RECORDFAIL_TIMEOUT=$GRUB_TIMEOUT`.
- Run `sudo update-grub`.

## Step 5b: Actually enabling `overlayroot`

We installed `overlayroot` in step 2, so all that is left to do is to enable it.
There is an important caveat to understand when enabling it: both the benefit
and drawback of it is that all new data is lost when the computer is turned off.
This stops the operating system getting corrupted, but it also means that any
and all changes you make to the computer while it is powered up will be lost.
That includes things like changing the URL of your kiosk, configuring a new
Wi-Fi network, tweaking some Ubuntu settings, etc. It's always possible to
disable `overlayroot`, but this requires two reboots (one to disable and one to
re-enable) so I'd strongly suggest making sure you have everything right
**before** enabling `overlayroot`.

When you installed `overlayroot`, it created a file called
`/etc/overlayroot.conf`. Opening it as we did with `/etc/gdm3/custom.conf`,
you'll find a lot of comments explaining the different options available and how
to configure them. At the bottom, you'll find the line:

```sh
overlayroot=""
```

Change this to:

```sh
overlayroot="tmpfs:swap=1,recurse=0"
```

Save the file and reboot. Your file system should now be protected. I'd
recommend checking this by creating a file on the desktop and rebooting. If the
file vanishes, `overlayroot` is working!

### _"Okay, but how do I disable `overlayroot`?"_

In order to make changes to the original file system again, you need to access
the 'original' file system and make the necessary changes. This can be done by
executing:

```sh
$ sudo mount -o remount,rw /dev/sda3
```

Note that the `/dev/sda3` part is the name of the disk that you want to
re-mount. It may differ on your system, but you can find the name by running:

```sh
$ df -h
Filesystem              Size  Used Avail Use% Mounted on
# ...
/dev/sda3                96G   16G   76G  17% /media/root-ro
# ...
```

Look for the entry that's mounted on `/media/root-ro` - most other options will
have names like `tmpfs` so it should stick out.

Once you run this command, you will have access to the original file system
again at `/media/root-ro`. If the change you want to make is simple, you can
just edit the file(s) directly inside `/media/root-ro`, but if not then you
probably want to reboot into writable file system, so you can make the necessary
changes, re-enable `overlayroot` and restert. This can be done easily, just by
editing the `overlayroot` configuration and rebooting.

The config can now be found at `/media/root-ro/etc/overlayroot.conf` - open this
with your text editor to see the same configuration you changed to enable
`overlayroot`. You can revert to the original file system by commenting out
(=prepending with `#`) the two non-comment lines at the bottom of the file.

Once rebooted, you should find that your changes are persisted again.
Re-enabling `overlayroot` is as simple as editing the configuration (now back at
`/etc/overlayroot.conf`) and uncommenting the lines you commented out before.
You may wish to leave a note on the desktop to future maintainers so they
understand this and know how to make their changes actually save again!

### A note on updates when using `overlayroot`

One of the downsides of using `overlayroot` is that it will prevent software
from applying updates. This is not a problem for the most part, except in the
case of security updates. This is not a huge concern for me, as the repair
shop's kiosk machines aren't accessing anything particularly private or
mission-critical, but you should evaluate this risk, minimise it as far as
possible and implement an update schedule if necessary.

# Sources

I stole bits and pieces for this article from the following places:

- [https://askubuntu.com/a/968265](https://askubuntu.com/a/968265)
- [https://spin.atomicobject.com/protecting-ubuntu-root-filesystem/](https://spin.atomicobject.com/protecting-ubuntu-root-filesystem/)
- [https://rex.writeas.com/use-overlay-filesystem-on-ubuntu](https://rex.writeas.com/use-overlay-filesystem-on-ubuntu)
- [https://askubuntu.com/questions/1046979](https://askubuntu.com/questions/1046979/overlayroot-and-grub2-grub-menu-always-shows)
