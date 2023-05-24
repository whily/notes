Notes for Emacs.

### Font

A useful link: http://ergoemacs.org/emacs/emacs_list_and_set_font.html

* List available fonts

    (print (font-family-list))

* Check the character set, font etc. for the current character by typing

    M-x describe-char

### Unicode character

Type `Ctrl+x 8 Enter`, then type Unicode character name or Hex value.

### Trouble shooting

* For error "Symbol's function definition is void: ...", remove all
elc files in `~/.emacs.d/elpa`.
