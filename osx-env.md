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

    alfred amethyst android-file-transfer android-studio audacity blender dash emacs
    evernote gimp google-chrome inkscape iterm2 java launchrocket
    mou mplayerx neteasemusic racket skim sogouinput the-unarchiver thunder
    virtualbox vox xee xquartz

### zsh configuration

Install oh-my-zsh (which changes default shell to zsh as well):

    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

Edit *~/.zshrc* to change plugin settings.

Get font for Powerline from [here](https://github.com/supermarin/powerline-fonts/blob/bfcb152306902c09b62be6e4a5eec7763e46d62d/Monaco/Monaco%20for%20Powerline.otf)

Install the fonts to *Macintosh HD | Library | Fonts* and select the font in iIerm2.

### python

    brew install python

### Miscellaneous configuration

As an Emacs user, exchanging *Control* and *Caps Lock* modifiers in
the settings of Keyboard is necessary.

Enable SSH server by selection *System Prferences | Sharing | Remote
Login*. Computer name can be also changed in the same dialog.
