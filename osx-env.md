Notes of step by step instructions of tuning OS X into a developer-friendly environment.

### Install Xcode

Xcode can be installed from App Store, or with the following fast
trick: type *gcc* at terminal, and select *Get Xcode* from the popup
dialog.

### Install Homebrew and cask.

Type following at terminal.

    $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    $ brew install caskroom/cask/brew-cask


Install following CLI packages by typing *brew install package*

    android-sdk autojump cconv curl ffmpeg gpg imagemagick nasm pbrt qemu sbcl sbt scala wget zsh
    homebrew/x11/bochs homebrew/dupes/gdb homebrew/science/maxima

Install following GUI packages by typing *brew cask install package*

    adobe-air alfred aliwangwang amethyst appcleaner android-file-transfer android-studio audacity
    blender caffeine dash disk-inventory-x emacs evernote filezilla firefox genymotion gimp goagentx google-chrome
    inkscape intellij-idea-ce iterm2 java launchrocket libreoffice
    lighttable mactex mou mpv neteasemusic racket skim sogouinput the-unarchiver
    transmission virtualbox vox xee xquartz xscreensaver youdao

To use proxy, prefix brew command with *https_proxy=192.168.xxx.yyy:abcd brew install...*

### zsh configuration

Install oh-my-zsh (which changes default shell to zsh as well):

    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

Edit *~/.zshrc* to change plugin settings.

Get font for Powerline from [here](https://github.com/supermarin/powerline-fonts/blob/bfcb152306902c09b62be6e4a5eec7763e46d62d/Monaco/Monaco%20for%20Powerline.otf), double click the font to install it. Then select the font in iIerm2.

Install powerline-shell with following commands:

    $ git clone https://github.com/milkbikis/powerline-shell
    (Change to the directory)
    $ cp config.py.dist config.py
    (In config.py, comment unused sections, change theme to washed.)
    $ ./install.py

Add following content to .zshrc. Modify path to powerline-shell.

    function powerline_precmd() {
      export PS1="$(~/powerline-shell.py $? --shell zsh 2> /dev/null)"
    }

    function install_powerline_precmd() {
      for s in "${precmd_functions[@]}"; do
        if [ "$s" = "powerline_precmd" ]; then
          return
        fi
      done
      precmd_functions+=(powerline_precmd)
    }

    install_powerline_precmd

### python

    brew install python

### LaTeX

For MacTex, the recommended approach to modify ~/.zprofile does not
work for me. So I have to add the path `/Library/TeX/texbin`
explicitly to both .zshrc and .bashrc (seems that Emacs shell still
uses bash).

### Miscellaneous configuration

As an Emacs user, exchanging *Control* and *Caps Lock* modifiers in
*System Preferences | Keyboard*.

Enable SSH server by selection *System Prferences | Sharing | Remote
Login*. Computer name can be also changed in the same dialog.

VNC settings can be manges in *System Preferences | Sharing | Screen
Sharing*.
