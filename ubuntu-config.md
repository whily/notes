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

    audacity autojump blender bochs chromium-browser cconv coq
    deluge emacs ffmpeg gap gimp git git-el gnupg gthumb imagemagick inkscape
    maxima markdown nasm powerline qeum racket sbcl scala smplayer texlive-full
    tmux virtualbox xscreensaver zeal zsh

Some software tips in:
   http://www.tecmint.com/things-to-do-after-installing-ubuntu-15-04-desktop/

#### Unity3D beta

Install from following link:
    https://forum.unity3d.com/threads/unity-on-linux-release-notes-and-known-issues.350256/

#### sbt

Install sbt according to http://www.scala-sbt.org/0.13/docs/Installing-sbt-on-Linux.html

    echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
    sudo apt-get update
    sudo apt-get install sbt

#### Android SDK installation

Install Android SDK according to
http://askubuntu.com/questions/318246/complete-installation-guide-for-android-sdk-adt-bundle-on-ubuntu

    # Install 32bit compatible libraries.
    sudo apt install libgl1-mesa-dev:i386

    # Install openjdk
    sudo apt install openjdk-8-jdk

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
* `autoclean`: removes .deb files for packages that are no longer installed in the system.
* `clean`: remove all packages files from the cache.
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

### Configuration tweaks

* Swap Ctrl and Capslock.

  In `Startup Application`, add `Swap Ctrl and Capslock` with the following command:
  `bash -c "sleep 5 && setxkbmap -option ctrl:swapcaps"`.
