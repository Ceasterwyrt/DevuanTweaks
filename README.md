# Installing Devuan on a 2008 MacBook Pro A1260

## Getting the Devuan image to work

Has to be written in DD mode.

## GRUB Shenanigans

Press "e" once GRUB shows up and add `nomodeset` at the end of the kernel line, otherwise it won't boot. We'll have to change some GRUB config files for it to be persistent:

Edit `/etc/default/grub`, adding `nomodeset` to the `GRUB_CMDLINE_LINUX_DEFAULT` line, and update GRUB: `update-grub`

For Linux Mint, adding `tsc=unstable` to the kernel line makes it boot.

## Getting the fans to work

Install `macfanctld`

After installing it'll automatically be added to the startup, you can check it with `rc-update add macfanctld default`

## Getting wi-fi to work

Add `contrib non-free-firmware non-free` to `/etc/apt/sources.list`, then execute the following commands:

`apt update`

`apt install linux-image-$(uname -r|sed 's,[^-]*-[^-]*-,,') linux-headers-$(uname -r|sed 's,[^-]*-[^-]*-,,') broadcom-sta-dkms`

`modprobe -r b44 b43 b43legacy ssb brcmsmac bcma`

`modprobe wl`

Reboot and check your wi-fi. If anything goes wrong the manual can be found [here](https://wiki.debian.org/wl).

## Global Menu Packages

`xfce4-appmenu-plugin`

`appmenu-gtk-module-common`

`appmenu-gtk2-module`

`appmenu-gtk3-module`

### Enabling the global menu

`export $(dbus-launch)` May not be needed, only use if the commands below don't work.

`xfconf-query -c xsettings -p /Gtk/ShellShowsMenubar -n -t bool -s true`

`xfconf-query -c xsettings -p /Gtk/ShellShowsAppmenu -n -t bool -s true`

`xfconf-query -c xsettings -p /Gtk/Modules -n -t string -s "appmenu-gtk-module"`

## XFWM4 with actual centralised window titles

This is something that bugs me a little, and we have to patch it ourselves. Download the XFWM4 source files [here](https://archive.xfce.org/src/xfce/xfwm4/4.18/xfwm4-4.18.0.tar.bz2).

Patch `src/frame.c` like so:

```
--- src/frame.c	2016-02-24 07:42:32.000000000 -0300
+++ src/frame.c	2017-04-09 12:36:51.100556257 -0300
@@ -496,6 +496,8 @@
                 hoffset = w3 - logical_rect.width - screen_info->params->title_horizontal_offset;
                 break;
             case ALIGN_CENTER:
+                w5 += right;
+                w1 = (w5 / 2) - (w3 / 2) - w2;
                 hoffset = (w3 / 2) - (logical_rect.width / 2);
                 break;
         }
@@ -525,7 +527,7 @@
                 w1 = right - w2 - w3 - w4 - screen_info->params->title_horizontal_offset;
                 break;
             case ALIGN_CENTER:
-                w1 = left + ((right - left) / 2) - (w3 / 2) - w2;
+                w1 = (w5 / 2) - (w3 / 2) - w2;
                 break;
         }
         if (w1 < left)
```

Install the required packages to build it using `apt build-dep xfwm4`

Configure using `./configure` and `make`. Then `make install` as root or using `sudo`

TO-DO: Add gaps (preference).

# Browser with global menu support (Waterfox)

`curl -fsSL https://download.opensuse.org/repositories/home:hawkeye116477:waterfox/Debian_12/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home_hawkeye116477_waterfox.gpg > /dev/null`

`echo 'deb https://download.opensuse.org/repositories/home:/hawkeye116477:/waterfox/Debian_12/ /' | sudo tee /etc/apt/sources.list.d/home:hawkeye116477:waterfox.list`

## Clock stuff

`%a %d %b %R`

# Getting the legacy NVIDIA drivers to work **[NOT WORKING, USE NOUVEAU INSTEAD]**

Install the following dependencies for installing the legacy drivers `apt install build-essential gcc-multilib dkms`

Blacklist Nouveau by creating this file `/etc/modprobe.d/blacklist-nouveau.conf` and writing the following inside:

```
blacklist nouveau
options nouveau modeset=0
```

Clone the following repository for the patched driver files (we need these patched versions because NVIDIA blocked their drivers on the newest kernels.

``git clone -b 340.108 https://github.com/MeowIce/nvidia-legacy``

Check your kernel version and install the appropriate `run` file by making it executable ``chmod +x FILE.run`` and running it `./FILE.run`
