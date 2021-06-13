Replace SSD with the one with larger capacity. First of all, backup everything important.

The upgrade is performed on Dell XPS 15 9550. Some steps (e.g. BIOS) could be hardware specific.

## BIOS settings

For dual boot, make sure to disable "Secure Boot".

To enable USB drive booting in Type-C port (this step is optional since USB drive can boot from USB-A ports),
in BIOS, select `System Configuration | Thunderbolt Adapter Configuration`, and check
`Enable Thunderbolt Adapter Boot Support`.

## Steps performed under Windows

Search "Recovery Drive" and follow the default settings to create a recovery drive for Windows.

Download YUMI to create MultiBoot USB drive. At the time of writing (June 2018), I downloaded YUMI-UEFI-0.0.0.8.exe.

Download Ubuntu 18.04 Live CD. I use mirror of Netease, in the following link: http://mirrors.163.com/ubuntu-releases/18.04/.
Download both the image (ubuntu-18.04-desktop-amd64.iso) and SHA1 sum (SHA1SUMS).
Use [Microsoft File Checksum Integrity Verifier](https://www.microsoft.com/en-us/download/details.aspx?id=11533) to verify SHA1 sum.

Download CloneZilla alternative stable (20180329-artful) and correponding checksum, and verify.

Download GParted stable releaes (v0.31.0-1) and corresponding checksum, and verify. This step can be optional since Ubuntu Live CD already contains GParted.

Download the [64-bit Media Creation Tool](http://www.microsoft.com/en-us/software-download/windows10)
and run it. (Note that the difference between the creation tool and recovery disk is that the former installs a clean Windows from scratch while the latter restores the laptop to a previous restore point.) Choose `Create installation media for another PC`,
then click `Next` --> Choose your language, `Windows 10`, and `64-bit (x64)` Architecture;
then click `Next` --> select `ISO`; then click `Next`. The tool will then download the ISO. This will take a while.

Use YUMI to create the USB driver include Ubuntu, Clonezilla, GParted, and Windows 10 installer.

## Tasks done within Clonezilla

### Backup SSD to USB hard drive

Reboot the computer with USB drive and boot with Clonezilla. Select the default options, e.g. device-image,
local_dev, Beginner mode.

Select "savedisk" to back up the original SSD to external USB hadr drive. Only attach the USB hard drive
upon prompt.

### Restore from USB hard drive to new SSD

Then shutdown and replace the SSD.

Boot up to Clonezilla again and select the default options as before.

Select "restoredisk" to restore from external USB hadr drive. Only attach the USB hard drive
upon prompt.

For both save and restore, one may choose to check the image for reliability (peace of mind).

## Task donw with GParted.

Reboot to use GParted to move and resize the partions as you like.

## Trouble shooting

Clonezilla copies the UUIDs of partitions correctly, therefore no need
to update /etc/fstab.

1) When dropped into "normal" Grub shell as in [Archlinux Grub
guide](https://wiki.archlinux.org/title/GRUB#Using_the_command_shell),
don't panic. Just type the following to boot correctly into Grub menu:

    insmod fat
    set root=(hd0,gpt1)
    chainloader (${root})/EFI/grub/grubx64.efi
    boot

Then in Arch linux, re-install Grub:

    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
    grub-mkconfig -o /boot/grub/grub.cfg

2) Windows may do some fancy stuff to mess up the dual booting.

Firstly, turn off fast startup again. In Windows, Use `Win + S` to search *Power Options*,
select *Choose what the power buttons do* in the left panel. In the right panel, for
*Shutdown settings*, uncheck *Turn on fast startup (recommended)*.

If during boot, the GRUB boot menu is not shown, one can first check the UEFI booting status
by running efibootmgr in Ubuntu Live CD, e.g. to make sure the GRUB entry is still there (if
one can see ubuntu as the output of running `efibootmgr`. Then in Windows, run `Command Prompt`
as administrator, and run the following command,
with details in [this guide](https://itsfoss.com/no-grub-windows-linux/)

    bcdedit /set {bootmgr} path \EFI\ubuntu\grubx64.efi
