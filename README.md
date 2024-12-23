# Assumptions

* A clean installation of Fedora Workstation.

# System

## Enable RPMFusion

For hardware acceleration and running proprietary video codecs.

Install free and non-free repos:  
<https://rpmfusion.org/Configuration#Command_Line_Setup_using_rpm>

Afterwards, set up multimedia:  
<https://rpmfusion.org/Howto/Multimedia>

You might want to update your system at this point.

```text
sudo dnf offline-upgrade download
sudo dnf offline-upgrade reboot
```

## NVIDIA How-to

Refer to <https://rpmfusion.org/Howto/NVIDIA>.

## Disable Chrome and PyCharm repos

```text
sudo dnf config-manager setopt google-chrome.enabled=0 copr:copr.fedorainfracloud.org:phracek:PyCharm.enabled=0
```

## Enable Intel GuC and HuC and Framebuffer compression (Intel only)

Add kernel parameters to load GuC and HuC (contrary to popular belief, they are not enabled by default except on Intel gen12+ platforms in the kernel) by running the following:

```text
sudo nano /etc/modprobe.d/i915.conf
```

and paste the following into it:

```text
options i915 enable_guc=3
options i915 enable_fbc=1
```

Save and close.  
Then rebuild your `initramfs` with:

```text
sudo dracut --force
```

Then reboot (important).

### Verify if it's working

Running

```text
sudo dmesg | grep guc 
```

Should return something like `GuC firmware i915/tgl_guc_62.0.0.bin version 62.0 submission:enabled`

```text
sudo dmesg | grep huc 
```

Should return something like `HuC firmware i915/tgl_huc_7.9.3.bin version 7.9 authenticated:yes`

Sources:

1. <https://discussion.fedoraproject.org/t/intel-graphics-best-practices-and-settings-for-hardware-acceleration/69944#h-4-enable-intel-guc-and-huc-and-framebuffer-compression-5>  
2. <https://wiki.archlinux.org/title/Intel_graphics#Enable_GuC_/_HuC_firmware_loading>

## Enable Flathub

```text
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

## Educate yourself

Extensively read [why privacy matters](https://www.privacyguides.org/en/basics/why-privacy-matters/) and always utilize some
[privacy tools](https://www.privacyguides.org/en/tools/).

Ideally, get a secure encrypted DNS resolver. I use NextDNS. [Set it up.](https://github.com/yokoffing/NextDNS-Config)

## Theme and/or change your shell

Pick between `fish`, `zsh`, `bash`. I'd personally go with `fish` for the user-friendliness and other stuff.

Pick your theme. I personally use [Tide](https://github.com/IlanCosman/tide). Some people like [Starship](https://starship.rs/). Do some research.

## Replace tuned with auto-cpufreq

`tuned` provides the Balanced, Performance, Powersave power profiles in GNOME. I don't like it.

```text
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq && sudo ./auto-cpufreq-installer
```

Afterwards, install the daemon with the graphical app.

## "No more blurry fonts"

<https://blog.aktsbot.in/no-more-blurry-fonts.html>

## Install micro

If you're too stupid to use `vim`, but also too smug to use `nano`, use [micro](https://micro-editor.github.io/).

## Speed up DNF

You'll preferably want the maximum amount of parallel downloads (20) if you have a good bandwidth, and if Fedora can't pick out actually decent mirrors, then enable `fastestmirror` (though I don't guarantee the same won't happen).

```text
sudo $EDITOR /etc/dnf/dnf.conf
```

Have the file look a bit like this:

```text
[main]
max_parallel_downloads=20
fastestmirror=True
```

## Install a custom kernel (completely optional)

Install a more optimized kernel, for the sake of it. For example, the [CachyOS Kernel](https://copr.fedorainfracloud.org/coprs/bieszczaders/kernel-cachyos).

Also check out the [add-ons repository](https://copr.fedorainfracloud.org/coprs/bieszczaders/kernel-cachyos-addons/) where you can install `uksmd`, sched-ext schedulers, `ananicy-cpp`, as well the settings used in the CachyOS distribution.

## Some cools apps

* [Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal)
* [Warehouse](https://flathub.org/apps/io.github.flattool.Warehouse)
* [Main Menu](https://flathub.org/apps/page.codeberg.libre_menu_editor.LibreMenuEditor)

All from the [Flathub](https://flathub.org/).

# GNOME

## Replace GNOME Totem with mpv

```text
sudo dnf swap totem mpv
```

Check out [configuring MPV](https://wiki.archlinux.org/title/Mpv#Configuration). Also make sure you're getting hardware acceleration properly.

## Replace GNOME System Monitor

### htop

[`htop`](https://htop.dev/) is a lightweight system monitor for the terminal.

```text
sudo dnf swap gnome-system-monitor htop
```

### Resources

If you want something graphical, you can use something like [Resources](https://github.com/nokyan/resources).

```text
sudo dnf rm gnome-system-monitor
flatpak install flathub net.nokyan.Resources
```

No matter what system monitor you go with, just remove GNOME's.

## Remove unused GNOME apps

These apps remain unused for me, at least. Feel free to keep whichever ones you need (though you'll need to figure out which does what).

```text
sudo dnf rm rhythmbox gnome-connections gnome-contacts evince gnome-tour simple-scan
```

Chances are you won't need LibreOffice, so you could remove it too:

```text
sudo dnf rm "libreoffice-*"
```

## Remove GNOME Software

This is probably not a good idea since it allows discovering new apps, and installing stuff is much easier with it, but I simply don't like how it auto-starts and runs in the background, and uses a decent amount of resources. This is only specific to my setup, though.

```text
sudo dnf rm gnome-software
```

## Install GNOME Tweaks

GNOME Tweaks allows (proper) configuring of system fonts, themes, mouse, touchpad and keyboards, enabling titlebar buttons, etc. It's essential in any GNOME system.

```text
sudo dnf install gnome-tweaks
```

## Extensions

Install [Extension Manager](https://flathub.org/apps/com.mattjakeman.ExtensionManager):

```text
flatpak install flathub com.mattjakeman.ExtensionManager
```

Pick some extensions: <https://extensions.gnome.org/#sort=downloads> (you can also browse in the Extension Manager app)

## Theming

I think GNOME is quite limited in regard to theming. That said, I don't find it that much of a necessity since the desktop already looks decent. If you still want to spice some things up, then...

As a prerequisite, every GTK theme you pick should support GNOME Shell / libadwaita / GTK4 theming. You need this in order to have a consistent looking system. Make sure you install the extension [User Themes](https://extensions.gnome.org/extension/19/user-themes/) to apply your changes.

Find some themes here ([Pling](https://www.pling.com/browse?cat=134&ord=latest)), though I personally recommend [vinceliuice](https://github.com/vinceliuice)'s themes. They mostly fit the criteria and are decent looking.

I don't use an icon theme because they never really provide enough icons and it feels like an unnecessary change. Adwaita doesn't even look that bad.

A custom system font may be a good idea. I use JetBrains Mono (install using `sudo dnf install jetbrains-mono-fonts`).

## Triple-buffering

Install a version of `Mutter` with the triple-buffering patch:

```text
sudo dnf copr enable trixieua/mutter-patched -y
sudo dnf update --refresh
```

Sources:

* <https://gitlab.gnome.org/GNOME/mutter/-/merge_requests/1441>
* <https://copr.fedorainfracloud.org/coprs/trixieua/mutter-patched/>

# Firefox

If you're not using Firefox already, what are you doing.

## Set up your user.js

If you don't want to run Mullvad Browser or Tor Browser, but still want the most privacy, use [arkenfox/user.js](https://github.com/arkenfox/user.js).

Otherwise, you'll probably want [yokoffing/Betterfox](https://github.com/yokoffing/Betterfox).

## Add-ons

Read: <https://github.com/arkenfox/user.js/wiki/4.1-Extensions>

Always make sure to install safe and privacy-respecting extensions. Look for alternatives otherwise.

# FAQ

Q: Flatpak or RPM or AppImage?  
A: I typically go for the most up-to-date and functioning release, and this varies from app to app.  
One thing to keep in mind is that Flatpaks are sandboxed and thus isolated from the host system. This may result in you having to jump through hoops in order to get the intended behavior.  
As a more concrete answer, your order of preference should typically be *RPM >> Flatpak >> AppImage* ([I hate AppImages](https://github.com/TheAssassin/AppImageLauncher))

Q: Atomic distributions??  
A: Maybe. If you want the ease of use more than the stability, you'd probably want the default. Take this with a grain of salt though, since I never tested them out (and likely never will).

Q: Where to find the answers?  
A: [ArchWiki](https://wiki.archlinux.org/title/Main_page), [Fedora Quick Docs](https://docs.fedoraproject.org/en-US/quick-docs/), [RHEL Docs](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/), [Google](https://www.google.com/) and/or [ChatGPT](https://chatgpt.com/)
