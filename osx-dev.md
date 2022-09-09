Notes for development under OSX.

### gcc/g++

* Show configruation of gcc:

    echo "" | gcc -xc - -v -E

### Object file, linker etc.

* Display the names and version numbers of the shared libraries that an object file uses

    otool -L file

* Change the path to shared libraries in an executable file (with gtk+
  used in bochs as an example)

    sudo install_name_tool -change /usr/local/lib/libgtk-quartz-2.0.0.dylib /usr/local/Cellar/gtk+/2.24.29/lib/libgtk-quartz-2.0.0.dylib /usr/local/bin/bochs
