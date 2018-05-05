Notes for Ubuntu software installation and configuration.

### Ubuntu keyboard shortcuts

https://help.ubuntu.com/community/KeyboardShortcuts
https://help.ubuntu.com/stable/ubuntu-help/shell-keyboard-shortcuts.html

### Chinese support

In `Language Support`, install languages `Chinese (simplified)` and
`Chinese (traditional)`. Click `Apply`.

Install Google pinyin package by

    sudo apt install fcitx-googlepinyin

Log out and Log in, in `Language Support`, for `Keyboard input method
system`, select `fcitx`. Log out and log in again, the fcitx penguin
icon is shown in the list of icons in the upper right corner.

Click fcitx icon, then select `ConfigureFcitx`, click `+` button, then
uncheck `Only Show Current Language`. Then select `Google Pinyin` (a
quick way is to type `pinyin` in the search box to filter the results).

### Software to install

    aria2 audacity autoconf autojump blender bochs chromium-browser cconv
    coq darktable deluge dispcalgui
    emacs exiv2 ffmpeg gap gimp gimp-help-en git git-el gnupg gthumb htop
    hugin imagemagick inkscape intltool libgimp2.0-dev liblensfun-bin
    maxima markdown nasm powerline python-pip qeum qgis racket redshift sbcl scala smplayer
    texlive-full tlp tmux virtualbox xscreensaver xscreensaver-gl zeal zsh

Some software tips in:
   http://www.tecmint.com/things-to-do-after-installing-ubuntu-15-04-desktop/

### Remove LibreOffice

Run commands in terminal (the first command should be run in bash
instead of zsh):

    sudo apt remove --purge libreoffice*
    sudo apt clean
    sudo apt autoremove

### Remove unnecessary packages

Run following commands:

    sudo apt purge ubuntu-web-launchers

#### Unity3D beta

Install from following link:
    https://forum.unity3d.com/threads/unity-on-linux-release-notes-and-known-issues.350256/

#### sbt

Install sbt according to http://www.scala-sbt.org/0.13/docs/Installing-sbt-on-Linux.html

    echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
    sudo apt-get update
    sudo apt-get install sbt

#### Android SDK and IntelliJ IDEA installation

Install Android SDK according to
http://askubuntu.com/questions/318246/complete-installation-guide-for-android-sdk-adt-bundle-on-ubuntu

    # Install 32bit compatible libraries.
    sudo apt install libgl1-mesa-dev:i386

    # Install openjdk
    sudo apt install openjdk-8-jdk openjdk-8-source

    # Make sure jdk8 is selected by checking: `file /etc/alternativiesjava`.
    # If pointing to jdk9 (Scala 2.11.8 cannot run in jdk9), then run the following:
    sudo update-alternativies --config java

    # Download the Android SDK command line tools from https://developer.android.com/studio/index.html#download
    # (they're located at the bottom of the page) and unzip it to /opt/
    tar xzvf android-sdk_r24.4.1-linux.tgz -C /opt/

    cd /opt/android-sdk-linux/tools

    # Install SDK packages.
    ./android

    # Update environment variables for ANDROID_HOME, CLASSPATH, and
    PATH (for tools, platform-tools, build-tools/25.0.1)

    # adb
    sudo apt-get install libc6:i386 libstdc++6:i386
    # aapt
    sudo apt-get install zlib1g:i386

Install IntelliJ IDEA Community edition

    # Download tar.gz (no JDK) from http://www.jetbrains.com/idea/download/

    # Extract the tar.gz file.
    tar xzvf ideaIC-2016.3.5-no-jdk.tar.gz

    # Run idea.sh
    cd bin
    ./idea.sh

    # In the installation, select both "Create Desktop Entry" and
    "Create Command-Line Launcher".

    # For default plugins, enable the following:
    # Enable all for Build tools, Test tools,
    # Disable UI designer and Plugin Devkit
    # Version control tools: Git, Github
    # Other tools: Bytecode Viewer, Terminal, YAML

    # Download and install Scala plugin.

    # Post-installation, change keymap to Emacs.

#### Numix icon theme

Follow the steps below:

    sudo add-apt-repository ppa:numix/ppa
    sudo apt update
    sudo apt install numix-gtk-theme numix-icon-theme-circle
    sudo apt install unity-tweak-tool

Launch `Unity Tweak Tool`, and then change `Theme` to `Numix` and
`Icons to `Numix-circle-light`.

### Update software and related commands.

Information can be found in: https://help.ubuntu.com/community/AptGet/Howto

Some more examples are given in: http://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-for-package-management/

Manual page for `apt` in 16.04: http://manpages.ubuntu.com/manpages/xenial/en/man8/apt.8.html

Edit the mirror/source list:

    sudo gedit /etc/apt/sources.list

Update the software list:

    sudo apt update

Update to newer version of packages:

    sudo apt upgrade

Or (which may install or remove packages)

    sudo apt dist-upgrade

Upgrade to a newer release of Ubuntu

    sudo do-release-upgrade

Other options for `apt`:

* `remove`: remove a package, but leaving configuration files.
* `purge`: remove a package as well as configuration files.
* `autoremove`: remove packages installed by other packages and are no longer needed.
* `check`: diagnostic.
* `autoclean`: removes .deb files for packages that are no longer
  installed in the system. May resolve some issues (e.g. stalling of
  apt processes).
* `clean`: remove all packages files from the cache (this might be
  dangerous since packages are not available when running dpkg to fix
  issues in recovery mode. So use with extreme caution).
* `changelog`: read the Changelog
* `search`: list packages with the searched information.
* `show`: show package information including version, size, dependency etc.
* `showpkg": check dependencies of a package.
* `list --installed`: list packages installed locally.

Install `.deb` package:

    sudo dpkg -i pkg_name


### zsh configuration

Install oh-my-zsh (which changes default shell to zsh as well):

    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

Install powerline

    sudo apt install powerline

Add following content to .zshrc. Modify path to powerline-shell.

    if [[ -r /usr/share/powerline/bindings/zsh/powerline.zsh ]]; then
        source /usr/share/powerline/bindings/zsh/powerline.zsh
    fi

### Install Albert

Follow
[Albert's wiki page](https://github.com/ManuelSchneid3r/albert/wiki/User-guide#how-to-install-albert),
perform the following:

    sudo add-apt-repository ppa:nilarimogard/webupd8
    sudo apt update
    sudo apt install albert

### Add darktable release repository

Add darktable stable releases PPA according
to [official link](http://www.darktable.org/install/#ubuntu)

    sudo add-apt-repository ppa:pmjdebruijn/darktable-release

### Configuration tweaks

* Swap Ctrl and Capslock.

  In `Startup Application`, add `Swap Ctrl and Capslock` with the following command:
  `bash -c "sleep 5 && setxkbmap -option ctrl:swapcaps"`.

  Above approach seems to fail randomly. Another approach is to
  install package `gnome-tweak-tool`, then select `Typing | Ctrl
  key position | Swap Ctrl and Caps Lock`.

* Use `Disk Usage Analyzer` to visually check the usage statistics.

* For wallpapers, try [Wallhaven Alpha](http://alpha.wallhaven.cc).

* To move Unity launcher to the bottome, type the following in
  terminal:

   gsettings set com.canonical.Unity.Launcher launcher-position Bottom

### Python package

Firstly install `virtualenv`

    pip install virtualenv

Setup a virtual python environment e.g. `fun`

    virtualenv virtualenv/fun -p /usr/bin/python3
    source virtualenv/fun/bin/activate

Using pip to install following packages:

* `autopep8` for PEP8 compliance
* `flake8` for syntax checking
* `jedi` for backend

To deactivate a virtualenv, simply run

    deactivate

### GIS

Python tool `elevation` to download DEM data.

    pip install elevation

    eio clip -o result.tif --bounds left bottom right top

Using Gdaldem Hillshading

    gdaldem hillshade input_dem.ext output_hillshade.ext -z 5 -s
    111120 -az 315 -alt 60

### Troubleshooting

Sometimes Ubuntu cannot boot. There are two ways to make remedies. 
The first approach is to log into a terminal. Although the login screen
is not avaible, sometimes it is still possible to open a terminal. This is
the preferred approach since almost all services (including network) are available.
Try to press `Ctrl+Alt+Fn` where `n` is 1 to 6 to open a terminal. If this does not work,
press `Ctrl+Alt+F7` then press `Alt+Left Arrow` or `Alt+Right Arrow` to switch terminal.

If terminals are not avaiable, another approach is to go to the Recovery mode in the Grub
menu. There are more works to do here. Typically to make changes, the partition should be mounted
as read and write. To do so, go to "Root" shell, then type `mount -o remount,rw /` to mount root partition
read/write, and then type `mount --all` to mount other partitions.

Now potential fixes to try:
* Edit the boot option in Grub, changing the part with "quiet splash" to "text" (or nothing).
* Switch between graphic cards: `sudo prime-select`
* Use the latest nVidia drivers for Ubuntu 18.04:
    
    sudo add-apt-repository ppa:graphics-drivers/ppa
    sudo apt update
    sudo apt upgrade
