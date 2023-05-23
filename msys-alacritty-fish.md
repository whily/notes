A note to install MSYS2 and use Alacritty as terminal with fish as
shell.

## MSYS2 and fish

Install MSYS2(https://www.msys2.org/#installation), use environment
UCRT64. Install fish with command `pacman -S fish`.


## Alacritty

Install Alacritty(https://alacritty.org/). 

Configure Alacritty by create file
%USERPFOFILE%\AppData\Roaming\alacritty\alacritty.yml.

Use the official template: https://kgithub.com/alacritty/alacritty/blob/master/alacritty.yml

Modify as follows:

``` yaml
window:
  columns: 100
  lines: 20

decorations: full

opacity: 0.95

font:
  normal:
    family: Inconsolata
    style: Regular

  bold:
    family: Inconsolata
    style: Bold

 size: 13.0


shell:
  program: c:\bin\msys64\msys2_shell.cmd -defterm -here -no-start -ucrt64 -shell fish
  args:
      - --login
```
