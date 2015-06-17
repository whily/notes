Notes of step by step instructions of tuning OSX into a developer-friendly environment.

### Install Xcode

This can be easily done by typing *gcc* at terminal, and select *Get Xcode* from the popup dialog.

### Install Homebrew and cask.

Type following at terminal.

    $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    $ brew install caskroom/cask/brew-cask
    

Install following CLI packages by typing *brew install package*

    autojump cconv curl ffmpeg imagemagick sbcl sbt scala wget zsh

Install following GUI packages by typing *brew cask install package*

    alfred android-file-transfer android-studio audacity dash emacs
    gimp google-chrome inkscape iterm2 java launchrocket
    mou mplayerx neteasemusic sogouinput the-unarchiver virtualbox xee xquartz

### zsh configuration

Install oh-my-zsh (which changes default shell to zsh as well):

    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

Edit *~/.zshrc* to change plugin settings.

### Miscellaneous configuration

As an Emacs user, exchanging *Control* and *Caps Lock* modifiers in
the settings of Keyboard is necessary.
