Previously I use Ubuntu on my XPS 15 9550. But after I upgraded to
18.04, the system could not boot into GUI. After several weeks
fighting, I gave up and decide to switch to Arch Linux. Now the laptop
runs Arch Linux smoothly, but it did take a lot of efforts to install
and configured Arch Linux.

Some notes:
- I changed two components of the laptop, so it is not a vanilla
  XPS 9550. SSD was replaced with Intel 760p 1 TB and the WLAN/BT card
  was replaced with Intel 9260.
- Arch Linux is dual booted with Windows 10. My / and /home partitions
  are /dev/nvme0n1p5 and /dev/nvme0n1p6, respectively.

I mainly follows
[Arch Linux installation guide]( https://wiki.archlinux.org/index.php/Installation_Guide)
and the [XPS 9550 specific blog post]( https://ahxxm.com/151.moew/).

## Preparation in BIOS and Windows

The steps are similarly to what I did for Ubuntu. So I just copied
related parts below.

#### Create bootable USB disk for Arch Linux

Download Arch Linux 2018.06.01 image. Select the mirror maximizing your download speed.
I use mirror of Netease, in the following link: http://mirrors.163.com/archlinux/iso/2018.06.01/

Download both the image (archlinux-2018.06.01-x86_64.iso) and SHA1 sum (sha1sums.txt).

Use [Microsoft File Checksum Integrity Verifier](https://www.microsoft.com/en-us/download/details.aspx?id=11533) to verify SHA1 sum.

Use Rufus to create bootable USB disk according to the link: https://www.ubuntu.com/download/desktop/create-a-usb-stick-on-windows

#### Windows Configuration

1. As Linux assumes hardware clock is UTC, it is also necessary to change Windows to do so, by adding the following into Registry.

``` ini
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation]
"RealTimeIsUniversal"=dword:00000001
```

2. Use `Win + S` to search *Power Options*, select *Choose what the
   power buttons do* in the left panel. In the right panel, for
   *Shutdown settings*, uncheck *Turn on fast startup (recommended)*,
   as well as *Hibernate*. Also type the following command (as
   Administrator) to disable hibernation thus eliminate
   `hiberfil.sys`.

``` shell
powercfg -h off
```

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

#### Modify BIOS settings as below:

- "Secure Boot" | "Secure Boot Enable": Change from "Enabled" to "Disabled"
- "System Configuration" | "SATA Operation": Change from "RAID On" to "AHCI"

Apply changes and save everything, and finally "Exit", to reboot.

#### Back to Windows

Windows will show up in Safe mode. Press `Win + W` to search for *Administrative Tools*,
and then select *System Configuration*.

Then go to the "Boot" tab and unselect "Safe mode boot". Accept and shutdown.

## Installation

After booting, using following command for larger fonts:
`setfont sun12x22` or even larger font like `latarcyrheb-sun32`.

The install media provides netcl . The following will bring up
a gui to setup your wireless connection, assuming you’re using
wireless.

``` shell
wifi-menu
```

Alternatively, one can configure Internet as below:

``` shell
# Get name of wireless interface. iw dev
ip link set interface up
# In my case, wlp2s0 is the wireless interface name.

# Genarate a basic configuration file.
wpa_passphrase MYSSID passphrase > wpa.conf
wpa_supplicant -B -i interface -c wpa.conf

# Obtain an ip address manually for example:
dhcpcd interface
```

Ensure the system clock is accurate:

``` shell
timedatectl set-ntp true
```

If needed, perform partition tasks in Gparted. Now mount the partitions:

``` shell
mount /dev/nvme0n1p5 /mnt

# Create mount points for any remaining partitions and mount them accordingly:
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
mkdir /mnt/home
mnt /dev/nvme0n1p6 /mnt/home
```

Create swap file.
```shell
# Create 8 GB swap file.
fallocate -l 8G /mnt/swapfile

# Set the right permissions (a world-readable swap file is a huge local vulnerability)
chmod 600 /swapfile

# Format the swap.
mkswap /mnt/swapfile

Activate the swap file:
swapon /mnt/swapfile
```

Edit `/etc/pacman.d/mirrorlist` to select your mirror. For me the
fasted mirror is http://mirrors.163.com/archlinux/$repo/is/$arch. One
can use [mirror generator](https://www.archlinux.org/mirrorlist/) to
generate a mirror, use `rankmirrors mirrorfile > mirror.fast` to rank
the mirror according to connection speed, and then copy the generated
file to `/etc/pacman.d/mirrorlist`.

Install the base package with

``` shell
pacstrap /mnt base base-devel
```

Generate an fstab file (use -U or -L to define by UUID or labels, respectively):

``` shell
genfstab -U /mnt >> /mnt/etc/fstab
```

To avoid SSD wear down, make sure that options `relatime` or `noatime`
are in the drive mout points (this should be enabled by default).
Edit `/etc/fstab` to add an entry for the swap file:

``` ini
/swapfile none swap defaults 0 0
```

Now perform the followins:

``` shell
# Chroot
arch-chroot /mnt

# Set time zone
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc

# For locales, uncomment en_US.UTF-8 UTF-8 and other
# needed localizations (e.g. lines starting with "zh_CN")
# in /etc/locale.gen, and generate them with:
locale-gen

# Set the LANG variable in /etc/locale.conf accordingly. e.g.
# LANG=en_US.UTF-8
```

Create the hostname file:
```
/etc/hostname
myhostname
```

Add matching entries to `/etc/hosts`

```
127.0.0.1 localhost
::1 localhost
127.0.1.1 myhost.localdomain myhostname
```

If the system has a permanent IP address, it should be used instead of 127.0.1.1.

Complete the network configuration for the newly installed
environment. For me, install `networkmanager` and configure WiFi is
sufficient.

``` shell
systemctl enable NetworkManager
systemctl start NetworkManager
# Configure network
nmtui
```

Set the root password by running `passwd`.

#### Boot loader

I use Grub as boot loader.

``` shell
pacman -S grub efibootmgr
```

Choose a bootloader identifier, here named GRUB. A directory of that
name will be created to store the EFI binary bootloader in the ESP and
this is the name that will appear in the EFI boot menu to identify the
GRUB boot entry. Execute the following command to install the GRUB
UEFI application grubx64.efi to esp/EFI/GRUB/ and install its modules
to /boot/grub/x86_64-efi/.

``` shell
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```
Editing `/etc/grub.d/40_custom` The entries in this file will be automatically added after rerunning grub-mkconfig.

``` ini
"Shutdown" menu entry
menuentry "System shutdown" {echo "System shutting down..."halt}
"Restart" menu entry
menuentry "System restart" {echo "System rebooting..."reboot}
```

Install `os-prober` to detect Windows 10.

Use the grub-mkconfig tool to generate grub.cfg:

``` shell
grub-mkconfig -o /boot/grub/grub.cfg
```

#### Reboot

Exit the chroot environment by typing `exit` or pressing Ctrl+D.

Optionally manually unmount all the partitions with

``` shell
umount -R /mnt
```

Finally, restart the machine by typing `reboot`

## Configuration

#### Grub

Optionally one can change GRUB resolution by editting
`/etc/default/grub` with following example (note that it is preferred
to not add the bit depth suffix like `x32` since some bit depths might
not be supported)

``` ini
GRUB_GFXMODE=1024x768
```

And run `grub-mkconfig -o /boot/grub/grub.cfg` after making changes.

To see the supported VESA modes, install `hwinfo` and run `hwinfo
--framebuffer` or enter grub run command videoinfo information.

For me, the working solution is to change Grub fonts by follow the guide:
https://wiki.archlinux.org/index.php/HiDPI#Change_GRUB_font_size

Suppose you want to use Deja Vu font (via package `ttf-dejavu`) and
font size 48.

``` shell
# Convert the font to a format that GRUB can utilize:
$ sudo grub-mkfont -s 48 -o /boot/grub/fonts/dejavusansmono.pf2 /usr/share/fonts/TTF/DejaVuSansMono.ttf
$ sudo nano -w /etc/default/grub
# Change GRUB_FONT="/boot/grub/fonts/dejavusansmono.pf2"
$ sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### Install Intel Microcode

``` shell
$ sudo pacman -S intel-ucode
$ sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### Linux-lts kernel

This step is optional. There are impacts to `bbswtich`, nvidia
drivers, and VirtualBox.

``` shell
$ sudo pacman -S linux-lts linux-lts-headers
$ sudo grub-mkconfig -o /boot/grub/grub.cfg
```

#### Console font

The default Linux console font will be very small on hidpi displays,
the largest font present in the kbdpackage is `latarcyrheb-sun32` and
other packages like `terminus-font` contain further alternatives, such
as ter-132n(normal) and ter-132b(bold).

One can edit `/etc/vconsole.conf` and set font with `FONT=ter-132n`.

#### Add additional user

``` shell
# useradd -m -G audio,storage,video,wheel foo
# passwd foo
```

Change foo to your desired user name.

(Tip: to add user `foo` to group `grp`, use command `useradd -aG grp
 foo` or `gpasswd -a foo grp`).

Run `visudo` to edit file `/etc/sudoers`. Uncomment the 1st line
containing `wheel` to enable running root commands from wheel group.

Now login as regular user just created.

#### AUR tool: Yay

The initial installation of Yay can be done by cloning the PKGBUILD
and building with makepkg.

``` shell
# git clone https://aur.archlinux.org/yay.git
# cd yay
# makepkg -si
```

In China, one may use `GOPROXY=https://goproxy.cn makepkg -si` for the
last command.

Use `yay -Syu --aur` to update all AUR packages. Alternatively, one
can run `yay --save --combinedupgrade` once, and then run `yay -Syu`
afterwards to update both official and AUR packages.

#### Xorg

i3(wm) is a flexible tilting window manager. For details, please refer
to the excellent [guide](https://i3wm.org/docs/userguide.html)
and [reference card](https://i3wm.org/docs/refcard.html).

``` shell
# Choose nvidia-340xx-utils and evdev.
sudo pacman-S xorg xorg-xinit xterm xorg-xeyes xorg-xclock
sudo pacman -S xorg-xrandr

# After start there will be a guidance choose win as default modifier.

sudo pacman -S sddm sddm-kcm
sudo systemctl enable sddm
sudo systemctl start sddm
```

Create file `/etc/sddm.conf.d/hidpi.conf` with the following content

``` ini
[Wayland]
EnableHiDPI=true

[X11]
EnableHiDPI=true
```

Optionally, one can install `redshift`.

``` shell
sudo pacman -S redshift
# Make adjustment
nano -w ~/.config/redshift.conf
systemctl --user --now enable redshift
```

#### KDE

Install package `plasma-desktop kdeplasma-addons kscreen' for KDE and following KDE
applications `konsole dolphin okular yakuake kdeconnect'.

For KDE Connect, run the following to update ufw rules:

    sudo ufw allow 1714:1764/udp
    sudo ufw allow 1714:1764/tcp
    sudo ufw reload

For phone to receive files, one needs to select a destination folder
and grant access in KDE Connect app on the phone.

Chane to double-click behavior by setting `Workspace Behavior |
General Behavior | Clicking file or folders` to "Select them".

To set the Meta key as a shortcut to open KRunner, run

    kwriteconfig5 --file kwinrc --group ModifierOnlyShortcuts --key Meta "org.kde.krunner,/App,,toggleDisplay"

Install following PLASMA themes: `ant-dracula-kde-theme[A] arc-kde[A]
numix-kde-theme-git[A]`.

Install `plasma5-wallpapers-video-git`.

Use package `kvantum` for appearance configuration, when "Configure
Active Theme",

1. In "Hacks" tab,
check Transparent Dolphin view / Blur explicitly translucent windows / Transparent
KTitle label / Transparent menu title / Respect dark themes, uncheck
Disable translucency with non-integer scaling.

2. In "Compositing & General Look" tab,
check Blurring for menus and tooltips / Translucent windows / Blurring
for translucent windows / Animation for state change under mouse /
Ignore inactive state / Transient scrollbars, set "Reduce window
opacity by" to 20%, set "Reduce menu opacity by" to 35%.

Install `latte-dock plasma5-applets-altte-separator[A]` and add it to `System Settings | Startup and
Shutdown | Autostart`. Add two separators, `Application Dashboard`
before the left separator, and `Pager` after the right separator (use
"Configure Virtual Desktops" to set the rows of pager). Latte dock
size can be changed by right click, "Edit Dock | Apperance | Items |
Absolute size".

Install `plama5-applets-simplemenu-git`, right click "Application
Launcher", select "Show Alternatives", then switch to "Simple Menu".

Move Panel to the top. Right click on the Panel, select "Enter Edit
Mode". Then clock "Drag to move" and drag the Panel to the top.

Widgets for Panel (from left to right):
- Application launcher (built in)
- Pager (built in)
- Window AppMenu (`plasma5-applets-window-appmenu[A]`)
- Taskbar (built in)
- Disk Usage (built in)
- Network Usage (`plasma5-applets-netspeed[A]`)
- Color Picker (built in)
- Awesome Widgets (`plasma5-applet-awesome-widgets[A]`)
- Translator (`plasma5-applets-translator[A]`)
- Weather Widget 2 (`plasma5-applet-weather-widget-2[A]`)
- Battery and Brightness (built in)
- KDE Connect
- Input Method Panel (build in)
- System Tray (built in)
- Event Calendar (`plasma5-applets-eventcalendar[A]`)

Note that when installing Plasma applets from AUR, one may meet errors
like "runtime library [libQt5Xml.so.5] in /usr/lib may be hidden by
files in: /home/whily/anaconda3/lib" and/or "Unknown option
'output-dep-file'". This is due to that Anaconda shadows the system
binary / library. Remove Anaconda binary path from $PATH.

#### Wayland

Install packages `plasma-wayland-session xorg-xwayland`.


#### Sway

Install package `sway swaylock swayidle swaybg waybar mpvpaper[A]
ttf-font-awesome sway-launcher-desktop[A] alacritty`.

Some packages related to i3 and might be useful to Sway:

    awesome-terminal-fonts gnome-control-center gnome-settings-daemon
    network-manager-applet polkit ttf-font-awesome olumeicon


Copy i3 config or sway config template `/etc/sway/config` to `~/.config/sway/config`.

Modify sway config:
* Modify font  of window title by:

    font pango:Noto Sans 12

* Swap CapsLock and Ctrl key:

    input "type:keyboard" xkb_options "ctrl:nocaps,ctrl:swapcaps"

* Config monitors and clamshell mode according to:
  https://github.com/swaywm/sway/wiki#clamshell-mode

* Config sway-launcher-desktop:

    for_window [app_id="^launcher$"] floating enable, sticky enable, resize set 30 ppt 60 ppt, border pixel 10
    set $menu exec alacritty --class launcher -e /usr/bin/sway-launcher-desktop

For waybar, copy config and style.css from `/etc/xdg/waybar` to
`~/.config/waybar`, and make necessary modifications (example
configurations in https://github.com/Alexays/Waybar/wiki/Examples).
Reload waybar configuration with command `killall -SIGUSR2 waybar`.

##### Configuration inherited from i3

Install AUR package `light`.

Install AUR package `archlinux-artwork`.


Edit `~/.config/i3/config` to automatically place these
programs in specific workspaces.

``` ini
## Assign clients to workspace
assign [class="^Konsole$"] → 1
assign [class="^Emacs$"] → 2
assign [class="^Chromium$"] → 3
assign [class="^Dolphin$"] → 4
assign [class="^Steam$"] → 10
```

To find out a window class, following
the [i3 user guide](https://i3wm.org/docs/userguide.html). In
particular, use `xprop`. After clicking on the window, you will see
the following output for urxvt (as an example):

    WM_CLASS(STRING) = "irssi", "URxvt"

The first part of the WM_CLASS is the instance ("irssi" in this
example), the second part is the class ("URxvt" in this example).

Add shutdown, reboot, lock screen as
https://wiki.archlinux.org/index.php/I3#Shutdown.2C_reboot.2C_lock_screen

``` ini
set $Locker i3lock -c 000000 && sleep 1

set $mode_system System (l) lock, (e) logout, (s) suspend, (h) hibernate, (r) reboot, (Shift+s) shutdown
mode "$mode_system" {
    bindsym l exec --no-startup-id $Locker, mode "default"
    bindsym e exec --no-startup-id i3-msg exit, mode "default"
    bindsym s exec --no-startup-id $Locker && systemctl suspend, mode "default"
    bindsym h exec --no-startup-id $Locker && systemctl hibernate, mode "default"
    bindsym r exec --no-startup-id systemctl reboot, mode "default"
    bindsym Shift+s exec --no-startup-id systemctl poweroff -i, mode "default"

    # back to normal: Enter or Escape
    bindsym Return mode "default"
    bindsym Escape mode "default"
}

bindsym $mod+c mode "$mode_system"
```

One reference:
[correct handling of floating dialogs](https://wiki.archlinux.org/index.php/i3#Correct_handling_of_floating_dialogs).


##### HiDPI

Find display name with the command `swaymsg -t get_outputs`, change
`~/.config/sway/config` with the following line `output <name> scale 2`.

For GTK, add the following to `~/.config/sway/config`:

    set $gnome-schema org.gnome.desktop.interface
    set $cursor_size 24
    exec_always {
        gsettings set $gnome-schema gtk-theme 'breeze'
        gsettings set $gnome-schema icon-theme 'Numix-Square'
        gsettings set org.gnome.desktop.interface text-scaling-factor 2
        gsettings set org.gnome.desktop.interface cursor-size $cursor_size
    }

For Chromium, to enable native Wayland, add
`--ozone-platform-hint=auto` in `~/.config/chromium-flags.conf`. To
have larger scaling, use the flag `--force-device-scale-factor=2`.

#### HiDPI

For i3, add `Xft.dpi: 192` in `~/.Xresources`.

For KDE, "System Settings | Display and Monitor | Display
Configuration", change "Global Scaling" to 200%.

For Chromium, add the following line to `~/config/chromium-flags.conf`:

    --force-device-scale-factor=2

For darktable, edit `~/.config/darktable/darktablerc`, set
`panel_width=600` (original value is 350). One may also play with `screen_dpi_overwrite` and
`screen_ppd_overwrite` but these are not needed for me. Then `cp
/usr/share/darktable/darktable.css ~/.config/darktable`, and edit
`~/.config/darktable/darktable.css`. For `* {}`, change `font: 8pt
Sans;` to `font: 12pt Arimo`. For `#header_label,#darktable_label,#view_label,#view_dropdown *
{}`, change `font-size: 15pt;` to `font-size: 20pt;` (this is slight
increase and is optional).

#### Themes

Refer to the guide: https://wiki.archlinux.org/index.php/GTK%2B

Install the following packages, with breeze packages for uniform look
between GTK+ and QT applications.

    arc-gtk-theme numix-gtk-theme breeze breeze-kde4 breeze-gtk
    breeze-icons oxygen oxygen-icons la-capitaine-icon-theme-git[A]

Below we show examples of setting font to `Source Sans Pro`. We could also
use other sans serif fonts like `Arimo`.

Install the package `lxappearance` for GTK+ theme configuration.
Launch `lxapperance` and select `Breeze` for Widget, Icon Theme and
Mouse Cursor.

Edit `~/.gtkrc-2.0`, change the font as

``` ini
gtk-font-name="Source Sans Pro 13"
```

Edit `~/.config/gtk-3.0/settings.ini`, change the font as

``` ini
gtk-font-name=Source Sans 13
```

For QT5, follow the guide
https://wiki.archlinux.org/index.php/Qt#Configuration_of_Qt5_apps_under_environments_other_than_KDE_Plasma
//
First install package `qt5ct`. Then set environment variable,
QT_QPA_PLATFORMTHEME for user session, by adding the following line to `~/.xprofile`.

``` shell
[ "$XDG_CURRENT_DESKTOP" = "KDE" ] || [ "$XDG_CURRENT_DESKTOP" = "GNOME" ] || export QT_QPA_PLATFORMTHEME="qt5ct"
```

Run `qt5ct`, set Appearnce and Icon Theme to Breeze, set font to
Arimo/Source Sans Pro/Overpass Regular for General font and Source
Code Pro/Hack for fixed width font. One may also launch Qt5
application with a specific theme, e.g. `QT_STYLE_OVERRIDE=fusion krita`.

#### zsh

Install package zsh, zsh-completitons, zsh-autosuggestions,
zsh-syntax-highlighting.

To list all installed shells, run `chsh -l`. To set one as default for
your user, run `chsh -s /bin/zsh`.

Install oh-my-zsh (which changes default shell to zsh as well):

    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

Add soft link as below:

    ln -s /usr/share/zsh/plugins/zsh-autosuggestions ~/.oh-my-zsh/plugins
    ln -s /usr/share/zsh/plugins/zsh-syntax-highlighting ~/.oh-my-zsh/plugins

Optionally, To use
[oh-my-zsh cheatsheet](https://github.com/robbyrussell/oh-my-zsh/wiki/Cheatsheet),
add the following line to .zshrc

``` shell
source  ~/.oh-my-zsh/templates/zshrc.zsh-template
```

Update plugins section in `.zshrc`.

Follow the Getting Started in
https://github.com/romkatv/powerlevel10k:

1. Install Meslo Nerd Font patched for Powerlevel10k. In Konsole, Open
   `Settings → Edit Current Profile → Appearance`, click "Select
   Font" and select MesloLGS NF Regular.

2. Install `powerlevel10k` as below:

    yay -S --noconfirm zsh-theme-powerlevel10k-git
    echo 'source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

3. Restart zsh with `exec zsh`.

4. Type `p10k configure` if the configuration wizard doesn't start automatically.

#### Font and configuration

Install following packages:

    ttf-dejavu ttf-inconsolata ttf-croscore otf-overpass ttf-hack
    adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
    adobe-source-han-sans-jp-fonts adobe-source-han-serif-jp-fonts
    adobe-source-sans-pro-fonts adobe-source-serif-pro-fonts adobe-source-code-pro-fonts
    wqy-microhei wqy-zenhei wqy-bitmapfont
    ttf-arphic-ukai ttf-arphic-uming

Note that programming fonts are up to one's own taste, e.g. many
people preferred Inconsolas or Deja Vu Sans Mono. And they can beAri
configured separately in terminal, Emacs, IDEA...

Font configuration in Arch Linux is described in
https://wiki.archlinux.org/index.php/Font_configuration

The syntax of the font configuration file is specified in
https://www.freedesktop.org/software/fontconfig/fontconfig-user.html

An example for Chinese font configuration is in
https://wiki.archlinux.org/index.php/Font_Configuration/Chinese_Font_Configurations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

An example for metric compatible font configuration is in
https://wiki.archlinux.org/index.php/Metric-compatible_fonts#Example_for_prefer_method

We use the following principles for font configuration:
- Use the Source fonts for both western and Chinese fonts (region
  specific for Mainland China)
- Use Chrome OS fonts as font-compatible fonts

Following changes for font configuration are optional. Actually I
couldn't tell the difference.

Edit file `/etc/profile.d/freetype2.sh`, read the comments there, and
uncomment and set to your preferred interpreter, if needed. I tried
Infinality mode, whose version is 38.

Then go to directory `/etc/fonts/conf.d`, create the following
symbolic links if they aren't alraedy present:

``` shell
ln -s ../conf.avail/11-sub-pixel-rgb.conf
ln -s ../conf.avail/11-lcd-filter-default.conf
```

#### Firewall

Follow
https://wiki.archlinux.org/index.php/Uncomplicated_Firewall#Basic_configuration
to install and configure UFW:

``` shell
# pacman -S ufw
# ufw enable // only once, when package is enabled
# systemctl start ufw
# systemctl enable ufw
# ufw default deny
# ufw allow from 192.168.0.0/24
# ufw limit SSH
```

#### Android development

To use the multilib repository, uncomment the `[multilib]` section
in `/etc/pacman.conf` (Please be sure to uncomment both lines):

``` ini
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Then install following packages:

``` shell
sudo pacman -S jdk8-openjdk intellij-idea-community-edition
yay -S android-sdk
```

Setup environment variables ANDROID_HOME, PATH, and CLASSPATH
correctly, e.g modifying `.zshrc` or `.zshenv`.

``` ini
export ANDROID_HOME=/opt/android-sdk
export CLASSPATH=$ANDROID_HOME/platforms/android-26/android.jar
export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$ANDROID_HOME/build-tools/28.0.0:$PATH
```

For IDEA's theme, one can install plugin `Material Theme UI`, and then
select one suitable `Material Theme` under `Appearance & Behavior`
setting (e.g. `Atom One Dark`) and one `Color Scheme` under `Editor`
setting (e.g. `Atom One Dark`) and
configure the suitable `Color Scheme Font`.

Sometimes when run `adb devices` command, one gets error message `no
permissions`. To solve the problem, run the following commands:

``` shell
adb kill-server
sudo adb start-server
adb devices
```

Pay attention to `sudo` above to pass the permission check.

Available packages for Android SDK can be checked with `sdkmanager
--list`, and selected packages can be installed by e.g. `sudo sdkmanager --install 'build-tools;27.0.3`

To remove bloatware, one can run following commands

``` shell
adb shell pm list packages
adb shell pm uninstall -k --user 0 pkg.name
```

#### Sound
To easily unmute and adjust sound level, install alsa-utils:

pacman -S alsa-utils
alsamixer # arrow select mixer, M mute/unmute it.

Install `pavucontrol` to configure Pulse Audio.

To make sound-related Fn keys working, append following to ~/.config/i3/config:

``` ini
bindsym XF86AudioRaiseVolume exec amixer -q set Master 2dB+ unmute
bindsym XF86AudioLowerVolume exec amixer -q set Master 2dB- unmute
bindsym XF86AudioMute exec amixer -q set Master toggle
```
#### Keyboard remap: swap caps with ctrl
Once you accept this setting, you won’t step back.

Add following content to ~/.Xmodmap:

    remove Lock = Caps_Lock
    remove Control = Control_L
    keysym Control_L = Caps_Lock
    keysym Caps_Lock = Control_L
    add Lock = Caps_Lock
    add Control = Control_L

Make it work in current session

    xmodmap ~/.Xmodmap

#### Graphics

``` shell
$ sudo pacman -S nvidia nvidia-utils opencl-nvidia primus nvidia-settings bumblebee mesa
$ sudo gpasswd -a your-usr-name bumblebee
$ sudo systemctl enable bumblebeed
```

Reboot and test the installation

``` shell
$ optirun glxspheres64
```

Install `bbswitch` to automatically switch off NVIDIA card as XPS 9550
uses Optimus.

To check OpenCL status, one can install package `clinfo`.

#### Bluetooth

Install packages `bluez`, `bluez-utils`, and `blueman`. Make sure
`btusb` kernel module is loaded. Then start bluetooth service by `sudo
systemctl enable --now bluetooth`. Use command `bdaddr` to check
Bluetooth address.

Auto power-on bluetooth adapter after reboot by adding line
`AutoEnable=true` in file `/etc/bluetooth/main.conf`.

For pairing with Bluetooth device, see
https://wiki.archlinux.org/index.php/Bluetooth#Pairing. Basically used
command `bluetoothctl`, in the prompt, use `devices` to see Bluetooth
MAC addresses (and type `scan on` if no devices are shown). Then type
`pair ADDR` where `ADDR` is the Bluetooth MAC address you want to
connect (TAB completion works here), and then type `connect ADDR` to
connect the device (TAB completion works too).

Install AUR package `hidclient` to emulate a Bluetooth keyboard.


#### Wacom Tablet CTL-672

Follow the guide: https://wiki.archlinux.org/index.php/Wacom_tablet

Basically, install package `xf86-input-wacom` and `kcm-wacomtablet`
(for tablet configuration). Restart X (`sudo systemctl restart sddm`).

Permanent X.org configuration in file
`/etc/X11/xorg.conf.d/72-wacom-options.conf`

``` ini
Section "InputClass"
	Identifier "WACOM OPTIONS pen"
	MatchDriver "wacom"
	MatchProduct "Pen"
	NoMatchProduct "eraser"
	NoMatchProduct "cursor"
EndSection

Section "InputClass"
	Identifier "WACOM OPTIONS eraser"
	MatchDriver "wacom"
	MatchProduct "eraser"
EndSection
```
Restart X again to make the configuration effective.

#### Chinese input

``` shell
$ sudo pacman -S ibus-rime
$ mkdir ~/.config/ibus/rime
$ nano -w ~/.config/ibus/rime/default.custom.yaml
```

In the file, choose the input schema, an example is shown below. Note indentation
level is important.

    patch:
        "menu/page_size": 7
        schema_list:
            - schema: luna_pinyin

Redeploy ibus either by click Deploy (部署) menu itme in ibus gui, or run the
following command

    $ rm ~/.config/ibus/rime/default.yaml && ibus-daemon -drx

Append following to `~/.config/i3/config` to make it auto start:

    exec_always ibus-daemon -drx

Configuration according to https://www.jianshu.com/p/2624fad33fe4

In Ibus tray icon, right click and select `Preferences`,  and
configure `Keyboard Shortcuts` for `Next input method`. I prefer to
use `<Control><Super>space` to avoid conflict with commonly used
shortcuts. Remember to click `Apply` after type the combination.

When Chinese is enabled, press ``Ctrl+` `` for options, use arrow key to
select `中/半/汉/`, and then select `漢字 -> 汉字` to change from
Traditional Chinese to Simplified Chinese.

Run `ibus-setup` to configure the font and size (in my case, `Source
Han Sans CN regular 16`) as well as the candidate orientation.
(horizontal vs vertical). If you get an error "ModuleNotFoundError: No
module named 'gi'", there is a conflict between `anaconda3` and
`ibus-setup`. First in home directory, run `chmod 000 anaconda3` to
disable anaconda. Then run `ibus-setup` for configuration. Afterwards,
run `chmod 755 anaconda3` to enable anaconda again.

For additional customization of rime, see
https://github.com/rime/home/wiki/CustomizationGuide

#### Wallpaper

Install package `archlinux-wallpaper`.

## Additional configuration

#### Power management

Install both [powertop](https://wiki.archlinux.org/index.php/Powertop)
and [TLP](https://wiki.archlinux.org/index.php/TLP).
Read [TLP FAQ](https://linrunner.de/en/tlp/docs/tlp-faq.html).

Basically, using powertop for analysis/suggestions and
TLP for actually power management. To enable TLP:

``` shell
$ sudo systemctl enable tlp tlp-sleep
$ sudo systemctl start tlp tlp-sleep
$ sudo systemctl systemd-rfkill systemd-rfkill.socket
```

Following the guide to minimize power consumption:
https://www.reddit.com/r/Dell/comments/6s2e3w/optimizing_dell_xps_for_linux/

Basically, add the following to GRUB parameters.

```shell
$ sudo naon -w /etc/default/grub
# GRUB_CMDLINE_LINUX_DEFAULT="acpi_rev_override=5 enable_fbc=1 enable_psr=1 disable_power_well=0 pci=noaer pcie_aspm=force nmi_watchdog=0"
# sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Touch screen can be either disabled in BIOS, or automatically
suspended by TLP. Run `lsusb | grep Elan` to find the USB device ID.
In my case, it is 04f3:21d5. Then add the ID into the line of
`USB_WHITELIST` of tlp configuration file `/etc/default/tlp`.

[Powertop user guide](https://01.org/sites/default/files/page/powertop_users_guide_201412.pdf) is
a nice read to understand principles of power management and
powertop's usage. To run measurements of `powertop`, one may run

``` shell
for i in {1..10} do;
    powertop --html
done
```

Although the general goad is to make sure everything is *Good* in
`Tunables` tab, some *Bad* wannings can be ignore as
from [TLP FAQ](https://linrunner.de/en/tlp/docs/tlp-faq.html):

* VM writeback timeout: TLP already sets a long writeback timeout (60
  s) compared to 15 s from powertop when on battery.
* Autosuspend for USB device USB Receiver [Logitech]: generally USB
  input device should not be autosuspended. Autosuspend USB receiver
  causes frequent freezing of the mouse.

#### NVME power saving

Follow
https://wiki.archlinux.org/index.php/Solid_State_Drive/NVMe#Power_Saving_APST

Basically install AUR package `nvme-cli` and run `nvme get-feature -f
0x0c -H /dev/nvme0` for analysis.

#### CPU temperature monitoring

Install packages `lm_sensors` and `thermald`. Run `sudo
sensors-detect` to detect the sensors and enable the services. For me,
I only allow the first detection and skip the remainning ones. One may
use `sensors` to view the system temperatures etc.

Run`systemctl enable --now thermald`.


#### SSD TRIM

Follow
the
[guide](https://wiki.archlinux.org/index.php/Solid_State_Drive#TRIM).
First verify TRIM support

    $ lsblk --discard

And non-zero values for columns DISC-GRAN (discard granularity) and
DISC-MAX (discard max bytes) indicate TRIM support. Then enable the
systemd unit file `fstrim.timer` in package `util-linux` to activate
TRIM weekly.

    $ systemctl enable fstrim.timer

#### S.M.A.R.T for SSD

Follow the [guide](https://wiki.archlinux.org/index.php/S.M.A.R.T.).
Install package `smartmontools` first.

After running `sudo smartctl -i /dev/nvme0n1 | grep 'SMART support is:',
it is supposed to return that SMART is activated and/or enabled.
However, this is not the case for my NVMD SSD (Intel 760p 1TB). So I
just enable SMART by `sudo smartctl --smart=on /dev/nvme0n1`.

It seems that capability (`-c`) and test (`-t`) commands do not return
much useful information. The health check command `sudo smartctl -H
/dev/nvme0n1` returns `PASSED` and `sudo smartctl -a /dev/nvme0n1`
returns all SMART information.

#### Trackpad input

Install libinput (in package `xf86-input-libinput`) instead of
synaptics. To enable tapping and two finger scrolling, following
the
[wiki](https://wiki.archlinux.org/index.php/Dell_XPS_15_(9550)#Touchpad)
by adding `30-touchpad.conf` to `/etc/X11/xorg.conf.d`:

``` ini
 Section "InputClass"
     Identifier "MyTouchPad"
     MatchIsTouchpad "on"
     Driver "libinput"
     Option "Tapping" "on"
     Option "Natural Scrolling" "on"
 EndSection
```

#### Hibernation

Based on
https://wiki.archlinux.org/index.php/Dell_XPS_15_(9550)#Suspend_.26_Hibernate

Write the following to `/etc/systemd/sleep.conf`:

``` ini
[Sleep]
HibernateState=disk
HibernateMode=shutdown
```

#### System search

Follow the guide: https://wiki.archlinux.org/index.php/Core_utilities#locate

``` shell
$ sudo pacman -S plocate
# Create the database. Seems not possible to use systemctl to enable updatedb.timer.
$ sudo updatedb
```

#### Check disk usage

``` shell
$ sudo pacman -S ncdu
# Scan a particular directory (root directory in example below)
$ sudo ncdu -x /
```

#### Deep learning

``` shell
sudo pacman -S cuda cudnn
```

The `cuda` package is installed in `/opt/cuda`. One needs to setup
environment variables correctly, e.g. modifying `.zshrc`:

``` shell
export PATH=/opt/cuda/bin:$PATH
export LD_LIBRARY_PATH=/opt/cuda/lib64:$LD_LIBRARY_PATH
```

Copy the folder `/opt/cuda/samples` to the home directory and run
`make`. Run `deviceQuery` in folder
`samples/bin/x86_64/linux/release`. Check the output.

Follow the guide
https://wiki.archlinux.org/index.php/Bumblebee#CUDA_without_Bumblebee

Ensure that the nvidia card is on:

``` shell
$ sudo tee /proc/acpi/bbswitch <<< ON
```

Now when you start a CUDA application it is going to automatically load all the necessary modules.

To turn off the nvidia card after using CUDA do:

``` shell
$ sudo rmmod nvidia_uvm
$ sudo rmmod nvidia
$ sudo tee /proc/acpi/bbswitch <<< OFF
```

Now install anaconda for pytorch.

Mainly based on the guide from
https://docs.anaconda.com/anaconda/install/linux

In China, use the mirror in
https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/

1. Installation

Download Anaconda https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.0.0-Linux-x86_64.sh

Install by typing

    bash ~/Downloads/Anaconda3-5.0.0-Linux-x86_64.sh

Accept the license and default location.

Do not accept the PATH variable update (as it is for .bashrc). Update
~/.zshrc by adding the following line (replacing `usr` with your user name):

    export PATH=/home/usr/anaconda3/bin:$PATH

Run `anaconda-navigator` to verify the installation.

2. Post installation

Use the mirror in Tsinghua.

    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
    conda config --set show_channel_urls yes

Test the mirror by running `conda install numpy`.

3. PyTorch

It is preferable to install packages in virtual environment.

    conda install pytorch torchvision cuda91

One may also install other packages in the virtual environment (e.g.
`jupyter matplotlib pandas scipy` which are included in `base` environment but
not in newly created environment).

Note that we install from channel cloud from mirror tuna to get
reasonable speed in China
(see [this](https://github.com/pytorch/pytorch/issues/990)
and [this](https://github.com/tuna/issues/issues/363)).

And then test pytorch by running following Python code.

``` python
import torch
print(torch.cuda.is_available())

```

To update Andaconda packages, type `conda update --all`.

To upgrade cuda version, uninstall related packages (e.g. pytorch,
torchvision, cudaxx) and install the new versions.

#### Mount NTFS partitions

Install package `ntfs-3g`. Mount any NTFS partion as below (suppose
you have created directory `/mnt/windows`).

``` shell
mount /dev/your_NTFS_partition /mnt/windows
```

#### Steam

Follow the guide https://wiki.archlinux.org/index.php/Steam

For HiDPI, follow https://wiki.archlinux.org/index.php/HiDPI#Official_HiDPI_support

Basically, Steam -> Settings -> Interface -> check "Enlarge text and
icons based on monitor size" (restart required)

To run steam with primus, follow the approaches in https://support.steampowered.com/kb_article.php?ref=6316-GJKC-7437,
i.e.:

1. Select a game in the library, select Properties, in GENERAL tab,
   click the `SET LAUNCH OPTIONS...` button and specify `primusrun
   %command%` for the command line. Or

2. Run `primusrun steam`, which is inefficient compared with the 1st
   method.

To show FPS counter, Steam -> Settings -> In-Game, select in drop-down
menu for FPS counter.

#### SSH key

To add the SSH key to the SSH agent, install package `keychain`, follow
the
[SSH agent guide](https://wiki.archlinux.org/index.php/SSH_keys#Keychain),
by adding the below to `~/.zshrc` (or `~/.bashrc`):

``` shell
eval $(keychain --eval --quiet id_rsa)
```

To make keychain environent available to Emacs (especially Magit), one
may add the following to `.emacs`:

``` lisp
(use-package keychain-environment)
(keychain-refresh-environment)
```

#### Just for fun

Install `fortune-mod` and add `fortune` to the end of `~/.zshrc`.


#### Chess

Install following programs: `chessx[A] scid_vs_pc[A] stockfish-git[A] pychess`

Download Syzygy tablebases, e.g. 5 piece tablebase from http://tablebase.lichess.ovh/tables/standard/3-4-5/

To install lc0(https://github.com/LeelaChessZero/lc0), install
`lc0-cudnn-git[A]` (of course make sure CUDNN is correctly installed
for Nvidia graphics card), and install dependency `protobuf`. Download
network from http://lczero.org/networks/. Then configure lc0 as UCI
engine in programs like SCID, with parameter `--weights=/path/to/weights_xxx.pb.gz`.

#### Programs

One may refer to the [Arch Linux's list of applications](https://wiki.archlinux.org/index.php/List_of_Applications).

Install following packages (with suffix [A] denoting AUR package):

- 3D modelling: `blender povray`
- Archiver: `lzip p7zip unzip unrar zip`
- Astronomy: `celstia stellarium`
- Audio converter: `lame`
- Audio edtor: `audacity ardour`
- Browser: `chromium pepper-flash`
- CLI: `ripgrep shellcheck[A]`
- Computer algebra: `gap maxima`
- Database: `postgresql sqlite`
- Development: `ccl[A] cgasm[A] clang clojure code gdb git go go-tools gource gradle hexyl[A]
  intltool kotlin lazarus-gtk2 leiningen[A] nasm npm python-pip
  racket sbcl sbt scala scala-docs scala-sources stow subversion`
- Dictionary: `stardict`
- Documentation `cheat-git[A]`
- Download tools: `aria2 baidupcs-go-git[A] curl filezilla wget`
- Encryption: `gnupg`
- File manager: `lf[A]`
- File system: `exfat-utils`
- FPGA: `iverilog gtkwave`
- Fun: `cmatrix xscreensaver`
- Geography: `qgis`
- Image processing: `imagemagick`
- Image viewer: `gthumb imv`
- Metadata: `exiv2`
- Markdown: `markdown`
- Monitor calibration: `dispalycal`
- Network utility: `mtr net-tools`
- PDF viewer: `mupdf-gl1 evince`
- Photo editor: `darktable hugin lensfun`
- Proof assistant: `coq`
- Raster graphics editor: `gimp gimp-help-en krita`
- Screen capture: `grim slurp`
- Screencast: `peek screnkey[A] wf-recorder`
- Spell check: `aspell-en`
- System: `acpi`
- System information viewer: `neofetch`
- System monitoring: `htop`
- Terminal multiplexer: `tmux`
- The Editor and everything: `emacs` (use
  `emacs-pgtk-native-comp-git[A]` for Wayland)
- Typesetting: `texlive-most texlive-langchinese`
- Vector graphics editor: `inkscape`
- Video converter: `ffmpeg frei0r-plugins`
- Video player: `mpv` (Configuration according to
  https://wiki.archlinux.org/index.php/Mpv#High_quality_configurations),
  celluloid (mpv frontend).
- Virtualization: `bochs qemu virtualbox virtualbox-host-modules-arch`

#### Emacs keybinding

Follow https://wiki.archlinux.org/index.php/GTK%2B#Emacs_keybindings
for GTK and XFCE.

For GTK2, add `gtk-key-theme-name="Emacs"` to `~/.gtkrc-2.0`.

For GTK3 add the following to `~/.config/gtk-3.0/settings.ini`:

``` ini
[Settings]
gtk-key-theme-name=Emacs
```

Then run `gsettings set org.gnome.desktop.interface gtk-key-theme "Emacs"`

For XFCE, run `xfconf-query -c xsettings -p /Gtk/KeyThemeName -n -t string -s Emacs`.

#### Zeal

Install `zeal` package. For Emacs integration, install `zeal-at-point`
package. For IntelliJ IDEA integration, install `Dash` plugin.

#### npm

To install Node.js global packages into home directory, run the
following as a normal user:

``` shell
cd ~ && mkdir .node_modules_global && npm config set prefix=$HOME/.node_modules_global && npm config set prefix=$HOME/.node_modules_global
```

### Davinci Resolve

Mainly follow
https://finaldie.com/blog/install-davinci-resolve-16-on-arch-sound-issue-fixed/

To run Resolve on HiDPI, start resolve with `QT_DEVICE_PIXEL_RATIO=2`.
In addition, as I'm using Bumbleebee, I needs to use `primusrun` to
run `resolve`, otherwise, the playback screen will be blank.

It seems that the free version of Resolve does not support H.264
video. Transcoding to supported codecs like ProRes are needed.


One may need to check logs for troubleshooting: `~/.local/share/DaVinciResolve/logs`

## Maintenance

Follow the guide
https://wiki.archlinux.org/index.php/System_maintenance

* Check failed systemd service with `systemctl --failed`. For any
  failed service, check its status with `systemctl status
  failed-service` where `failed-service` is the problematic service
  name.
* Check errors in journal by `journalctl -p 3 -xb`.

#### Pacman usage

[Pacman wiki](https://wiki.archlinux.org/index.php/Pacman) and
[Pacman Rosetta](https://wiki.archlinux.org/index.php/Pacman/Rosetta)
provide comprehensive information regarding the usage of pacman.
Below is a list of useful pacman commands:

* `pacman -Syu`： update the system
* `pacman -S package_name`: install package
* `pacman -S --asexplicit package_name`: fake install a dependency to
  avoid it to be removed.
* `pacman -U /path/to/package/package_name-version.pkg.tar.xz`: install local package file
* `pacman -Rsn package_name`: remove a package, its dependencies not required by other packages, and its
   configuration file (except "dotfiles")
* `pacman -Ss package_name`: search remote package
* `pacman -Qs package_name`: search local package
* `pacman -Qi package_name`': display local package information
* `pacman -Si package_name`: display remote package information
* `pacman -Ql package_name`: display files provided by local package
* `pacman -Fl package_name`: display files provided by remote package
* `pacman -Fs file_name`: search a package contains a specific file
  (needs to run `pacman -Fy` to sync the database).
* `pacman -Qo /path/to/file_name`: dispaly which package a file in the file system belongs to
* `pacman -Qe` : list packages installed explicitly
* `pacman -Qdt`: list all packages no longer required as dependencies
  (orphans)

#### Troubleshooting

If cannot boot to GUI, chroot and type `systemctl disable sddm` to
fallback to CLI.

Check various logs e.g. `/var/log/pacman.log` (which records
everything from the initial installation of the system).

To shutdown properly from unresonsive system, one can use kernel
keyboard shortcuts,
i.e.
[Magic Sysreq](https://wiki.archlinux.org/index.php/Keyboard_shortcuts#Kernel).
To enable Magic Sysreq at boot, edit `/etc/sysctl.d/99-sysctl.conf`
and insert the text `kernel.sysrq = 1`. The Maigc Sysreq sequence is
`Alt+Sysreq`+`REISUB` sequence where `Sysreq` is `PrtScr` key at
laptop (and there is no need to press `Fn` key`.

To force `fsck` at boot, enter `fsck.mode=false` as GRUB kernel
parameter. For details on `fsck`, read
https://wiki.archlinux.org/index.php/Fsck . It seems that `fsck` does
not automatically run on my partitions, so I have to manually enable
it e.g. `sudo tune2fs -c 30 /dev/nvme0n1p5`.

If sometimes meeting errors of "signatures from xxx is unknown trust",
one can install latest keyring first by `pacman -S --asdeps archlinux-keyring`.

#### Analyze boot time

Use either `systemd-analyze blame` for text output or `systemd-analyze
plot > plot.svg` for graphic output.

#### General upgrade guidelines

See a related discussion in https://youtu.be/CWOELeGlwiM

* Suscribe (RSS) or check Archlinux news.
* Upgrade frequency is up to user. Some prefer to update frequently e.g. daily, while
  some recommends a update frequency between two weeks to one month.
* If there is manul intervention update, then update immediately to avoid handling multiple
  interventions.
* Never perform partial upgrade (i.e. always perform `pacman -Syu`).
* When encountering package corruption message like "signature is from
  unknown trust", try `sudo pacman -S archlinux-keyring` first.
* If one want to avoid frequent updates of critical packages, one may edit `/etc/pacman.conf`
  and enable the line `IgnorePkg = ...`. For example, one may add following packages (depending
  on which are actually installed and/or which are preferred not to be upgraded frequently) `linux
  linux-headers linux-lts llinux-lts-headers virtualbox virtualbox-guest-iso virtualbox-host-modules
  virtualbox-host-modules-lts`.
 * Avoid clearing the pacman cache, since the cache files can be used to downgrade to previous version
   with `pacman -U /var/cache/pacman/pkg/package_name-version.pkg.tar.xz`. If disk space is really critically low,
   one may try disk usage analyzer tools e.g. `ncdu`, cleanup packeage
   `bleachbit`, or follow
   the [guide for clean the package
   cache](https://wiki.archlinux.org/index.php/Pacman#Cleaning_the_package_cache).
   Or run command `sudo paccache -r` where `paccache` is in package `pacman-contrib`.

#### Frequently used pacman commands

## Backup

There are various ways to backup, e.g. refer
to [Backup guide](https://wiki.archlinux.org/index.php/System_backup).

For me, I use CloneZilla to backup the disk/partitions, and I have
used tar to backup home directory. For tar, either no compression for
speed or use gzip to save space (for a comparision of various
compression programs,
see
[this](https://www.rootusers.com/gzip-vs-bzip2-vs-xz-performance-comparison/)).

Suppose use name is `bar`. If no compression is used:

``` shell
# Backup. As root.
cd /home
tar -cvf bar.tar.gz bar/

# Restore. As user bar.
cd /home
tar -xvf bar.tar.gz
```

If using gzip, just add `z` in tar options above.

## TODO

* HiDPI SDDM
* Video wallpaper for KDE
* Sly for Emacs
* Fix IBus in KDE
* Resolve the issue the i3 status bar is too high (lots of padding in
  the bottom of the bar, might be due to some font config)
* Full disk encryption using LUKS:
  https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#Simple_partition_layout_with_LUKS
* primusrun does not work while optirun works.
