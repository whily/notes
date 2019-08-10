Notes for Blender (under Arch Linux).

### 3D LUT

* Put the LUT file in directory like `/usr/share/blender/2.80/datafiles/colormanagement/luts`.

* Modify file
  `/usr/share/blender/2.80/datafiles/colormanagement/config.ocio`, add
  following lines. Change `foo.cube` to the LUT file name. I haven't
  figured out whether it is necessary to add the inverse direction
  transform. It seems that both `best` and `linear` interpolation
  method work.


    - !<Look>
      name: Standard - Cine4 to Rec709
      process_space: sRGB
      transform: !<GroupTransform>
          children:
              - !<FileTransform> {src: foo.cube, interpolation: best}
