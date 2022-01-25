Software installation in Windows

### Download from corresponding websites

    Adobe Reader DC, AutoHotKey, Chrome, ConEmu, Dism++, DriveStore Explorer,
    Everything, Foobar2000, HoneyView, IDM, Kite, Motrix, Potplayer, SpaceSniffer,
    TotalCommander, uTorrent, Wox

### Download UWP apps from Windows app store:

    Bing dictionary, MSN Weather (pre-installed), Netease music,
    Taobao

### Miscellaneous

#### Command installer `Scoop`

Install Scoop according to: https://zhuanlan.zhihu.com/p/128955118

    iwr -useb https://gitee.com/glsnames/scoop-installer/raw/master/bin/install.ps1 | iex
    scoop config SCOOP_REPO 'https://gitee.com/glsnames/Scoop-Core'
    scoop update
    scoop bucket add extras
    scoop update
    scoop install aria2 git ffmpeg ghostscript 

#### Check battery status

Run `Command Prompt (Admin)`, and then type the following:

    powercfg /batteryreport /output "c:\battery.html"

Replace `"c:\battery.html"` above to preferred file location/name.
Then view the file.

#### Chrome extension

Install `Enhanced Steam` by following instructions in github (using
Devloper Mode to install local unpacked extension).

### Noto fonts

Install OTC fonts, e.g. https://github.com/googlefonts/noto-cjk/blob/master/NotoSansCJK-Light.ttc

#### Color Management

I tried to change color setttings in `Windows Mobility Center` from `Splendid Color`
to `Generic Color` but couldn't find Dell customized setting at all. Since the program
Premier Color doen't work at all and cannot be updated (as the time of writing:
13 August 2017. Either udpate manually or via Dell Command | Update fails), I simply
uninstalled the program.

As I haven't bought any color calibration hardware yet (maybe X-Rite i1Display Pro
or Spyde5 Pro), I just use DisplayCAL and the [ICC file from notebookcheck.net]
(https://www.notebookcheck.net/uploads/tx_nbc2/Dell_XPS_15_2016_FHD_matt__1203M_LQ156M1_.icm).
