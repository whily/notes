Replace SSD with the one with larger capacity. First of all, backup everything important.

## Steps performed under Windows

Search "Recovery Drive" and follow the default settings to create a recovery drive for Windows.

Download YUMI to create MultiBoot USB drive. At the time of writing (June 2018), I downloaded YUMI-UEFI-0.0.0.8.exe.

Download Ubuntu 18.04 Live CD. I use mirror of Netease, in the following link: http://mirrors.163.com/ubuntu-releases/18.04/.
Download both the image (ubuntu-18.04-desktop-amd64.iso) and SHA1 sum (SHA1SUMS).
Use [Microsoft File Checksum Integrity Verifier](https://www.microsoft.com/en-us/download/details.aspx?id=11533) to verify SHA1 sum.

Download CloneZilla alternative stable (20180329-artful) and correponding checksum, and verify.

Download GParted stable releaes (v0.31.0-1) and corresponding checksum, and verify.

Use YUMI to create the USB driver include Ubuntu, Clonezilla, and GParted.

## Tasks done within Clonezilla

Shutdown the computer and connect with external hard drive.

Reboot the computetr with USB drive and boot with Clonezilla.

Save to image with exact copy

Then shutdown and replace the SSD. Boot up to Clonezilla again.

Restore from image with exact copy.

## Task donw with GParted.

Reboot into GParted.
