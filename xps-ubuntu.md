Notes of step by step instructions of installing Ubuntu 16.10 on XPS
15 9550. The steps are mainly based on
https://ubuntuforums.org/showthread.php?t=2317843, with minor
modifications. Many parts below can be find from the relevant
URLs. The motivation to duplicate these is for self-containess. XPS
specific configuration/installation in Windows and Ubuntu is also
included.

## Create bootable USB disk for Ubuntu

Download Ubuntu 16.10 desktop image. Select the mirror maximizing your download speed.
I use mirror of Netease, in the following link: http://mirrors.163.com/ubuntu-releases/16.10/

Download both the image (ubuntu-16.10-desktop-amd64.iso) and SHA1 sum (SHA1SUMS).

Use [Microsoft File Checksum Integrity Verifier](https://www.microsoft.com/en-us/download/details.aspx?id=11533) to verify SHA1 sum.

Use Rufus to create bootable USB disk according to the link: https://www.ubuntu.com/download/desktop/create-a-usb-stick-on-windows

## Tasks done within Windows

### Security

Uninstall trial version of McAfee. Make sure UAC, Windows Defender, and Windows Firewall is turned on.

As Linux assumes hardware clock is UTC, it is also necessary to change Windows to do so, by adding the following into Registry.

    Windows Registry Editor Version 5.00

    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation]
    "RealTimeIsUniversal"=dword:00000001

### Maintaineance

For Dell software, install Dell Command Update, and remove other stuff
like (Dell Product Registration, Dell Dock Update, Dell Update).

Optional install of `Dell Command | Power Manager`. For `Battery
Settings`, select `Custom`, and then customize (e.g. use the default
setting of 50% to 55% if primarily used on AC power).

### Configuration change in Windows

1. Repartition SSD, Shrink C drive by 74 GB for Linux. There is no need to create partitions.

2. Use `Win + S` to search *Power Options*, select *Choose what the
   power buttons do* in the left panel. In the right panel, for
   *Shutdown settings*, uncheck *Turn on fast startup (recommended)*,
   as well as *Hibernate*. Also type the following command (as
   Administrator) to disable hibernation thus eliminate
   `hiberfil.sys`.

    powercfg -h off

3. Create a Windows Recovery Media on USB flash drive according to
   [this](https://www.reddit.com/r/Dell/comments/3sr1jh/windows_10_clean_install_guide/).
   Download the [64-bit Media Creation Tool](http://www.microsoft.com/en-us/software-download/windows10)
   and run it. Choose `Create installation media for another PC`,
   then click `Next` --> Choose your language, `Windows 10 Home`, and `64-bit (x64)` Architecture;
   then click `Next` --> select `USB flash drive`;    then click `Next` --> select USB flash drive ;
   then click `Next`. The tool will then download and    install the program on your USB. This will take a while.
   When the tool is done, it will show you a window that says `Your USB flash drive is ready`.
   Click `Finish`.

4. Get ready for booting windows in Safe mode afterwards (** This step is very important! **).
   Press `WIN + R` and execute "msconfig".
   Go to "Boot" and select "Safe mode boot". Left "Minimal" selected.

5. Reboot and enter BIOS (Press F2 when the DELL logo shows up).

## Modify BIOS settings as below:

- "Secure Boot" | "Secure Boot Enable": Change from "Enabled" to "Disabled"
- "System Configuration" | "SATA Operation": Change from "RAID On" to "AHCI"

Apply changes and save everything, and finally "Exit", to reboot.

## Back to Windows

Windows will show up in Safe mode. Press `Win + W` to search for *Administrative Tools*,
and then select *System Configuration*.

Then go to the "Boot" tab and unselect "Safe mode boot". Accept and shutdown.

## Install Linux

1. Insert Ubuntu USB stick, reboot, Press F12 and select USB flash disk to boot.

3. Enter ubuntu and select `Install Ubuntu` in the Grub Menu. In the screen of "Installation type",
   select `Something else`. The following post give more explanation:
   https://askubuntu.com/questions/343268/how-to-use-manual-partitioning-during-installation/343370#343370

   Partitioned the unused disk space (created from windows) in root (EXT4, Primary partition)
   and 8 GB SWAP (as swap area, also primary partition).

   For `Device for boot loader installation`, select the UEFI partition
   `/dev/nvme0n1p1 Windows Boot Manager`. Not sure whether it matters.

4. Settings

   Following hardware components work out of box: Bluetooth, WiFi.

* To avoid small fonts in Grub, add `GRUB_GFXMODE=1024x768` to `/etc/default/grub`, and run the following:

    sudo update-grub

* Make hidpi looks OK

** On the system setting for Displays, modify the `Scale for menu and
title bars` to around 2.
** Run GNOME Tweak Tool, set `Fonts | Scaling Factor` to 1.3, and
`Windows | Windows Scaling` to 2.

For IntelliJ IDEA, in `File | Settings | Appearance & Behavior |
Appearance`, for `Override default fonts by`, select `Ubuntu` and 48.

For GIMP, modify gtkrc file in the theme (default themes in
`/usr/share/gimp/2.0/themes`, local themes in `~/.gimp-2.8/themes`).
For `GimpToolPalette::tool-icon-size`, change the value to `dialog`.
For `GimpEditor::button-icon-size`, change the value to `button`.

For fcitx modify fcitx_skin.conf in the skin (default skins in
`/usr/share/fcitx/skin`, local skins in
`~/config/fcitx-qimpanel/skin`). Change the value for `FontSize` to 30
(also change `MenuFontSize` to 25, but not sure whether this is useful).

Following are not used but kept here as reference.
** Run `xrandr --dpi 288` once.
** Add `QT_AUTO_SCREEN_SCALE_FACTOR=1` in `.bashrc` or `.zshrc` based
   on [QT doc](http://doc.qt.io/qt-5/highdpi.html)
   and [Arch Linux doc](https://wiki.archlinux.org/index.php/HiDPI).

* To disable touchpad while typing, follow the solution
  [here](https://ubuntuforums.org/showthread.php?t=2316240), i.e.
  adding the following entry in file
  `/usr/share/X11/xorg.conf.d/51-synaptics-quirks.conf`

    # Disable generic Synaptics device, as we're using
    # "DLL0704:01 06CB:76AE Touchpad"
    # Having multiple touchpad devices running confuses syndaemon
    Section "InputClass"
            Identifier "SynPS/2 Synaptics TouchPad"
            MatchProduct "SynPS/2 Synaptics TouchPad"
            MatchIsTouchpad "on"
            MatchOS "Linux"
            MatchDevicePath "/dev/input/event*"
            Option "Ignore" "on"
    EndSection

  Another approach is to completely disable the touchpad. Use `xinput`
  to find out the device id for the touchpad e.g. the id corresponding
  to `DLL06E4:01 06CB:7A13 Touchpad`, and suppose the id is 14. Then
  disable the touchpad by `xinput --disable 14`.

  Following is not working in Ubuntu 17.10:

  Add the following command to startup applications.
  Add `Palm Detection` with command `xinput set-prop 13 "Synaptics Palm Detection" 1`,
  and `Palm Dimensions` with command `xinput set-prop 13 "Synaptics Palm Dimensions" 5, 5`.
  Disable touchpad whiling typing can be done by adding file `/etc/modprobe.d/synaptics.conf`
  with the following line `blacklist i2c-designware-platform`.

* Display driver: from the applications menu, select "Preferences|Additional drivers",
  and select to use the "NVIDIA binary driver - version 367.57 from nvidia-367 (privative, tested)".
  Then apply changes. Install `nvidia-prime`, then run `nvidia-settings` and
  select either NVIDIA GPU (Performance Mode) or Intel (Power Saving  Mode).

## For reference only

- Windows 10 Clean install guide in:
  https://www.reddit.com/r/Dell/comments/3sr1jh/windows_10_clean_install_guide/

- Replace SSD drive (needs to confirm SSD is PCIe drive):
  http://www.laptopmag.com/articles/upgrade-dell-xps-13-ssd

- Memory upgrade: DDR4-2133 or 2400, Maximum 32GB.

- May need te replace Dell 1830 wireless card with Intel 8260 if there is issue with former.
  So far, wireless cards works fine in both Windows and Ubuntu.

- To upgrade XPS 15, screwdrivers for Philips PH0 and Torx T5 are needed.
