Notes for photography post-processing in Ubuntu Linux.

### Software packages to install

    darktable exiv2 gimp gimp-help-en hugin libgimp2.0-dev liblensfun-bin

### Miscellaneous

Update lensfun database

    sudo lensfun-update-data

Check bit depth of RAW file

    exiv2 -pv foo.ARW | grep BitsPerSample
