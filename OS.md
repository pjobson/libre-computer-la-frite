# Install An Operating System

Get an OS image: [https://distro.libre.computer/ci/](https://distro.libre.computer/ci/) 

This is geared towards installing Debian 12, you can use other OS's at your interest.

Note: I have found that their Ubuntu Desktop related images are broken, they fail on boot.

You should download images prefixed with `+arm64`.

In this example I'm using `debian-12-base-arm64+arm64.img.xz`.

    wget https://distro.libre.computer/ci/debian/12/debian-12-base-arm64%2Barm64.img.xz
    unar debian-12-base-arm64+arm64.img.xz

The La Frite has no internal storage, so you can use a USB stick or EMMC if you bought the module.

## METHODS

### USB Stick

You can use Balena Etcher or if you're adventurous `dd`.

    sudo fdisk -l

Find your stick's path, mine is: `/dev/sda`.

**Note:** Don't copy and paste sda if your USB stick isn't that path or you could wipe your hard drive.

    sudo dd if=./debian-12-base-arm64+arm64.img of=/dev/sda bs=512

Put the stick into the Le Frite and power on.

Skip down to the [Booting](#booting) section.

### EMMC

Boot up using the [UART](https://github.com/pjobson/libre-computer-la-frite/blob/main/UART.md) method.  Load the boot menu holding the escape key after plugging in the unit.

      *** Main Menu ***
    
          Boot
          Select Boot
          UEFI Config
          EtherealOS
          USB eMMC Mode
          USB DFU Mode
          Firmware Update
          Console
          Reboot
          Shutdown
    
    Press UP/DOWN to move, ENTER to select, ESC to quit

Select `USB eMMC Mode`.

It should then show:

    switch to partitions #0, OK
    mmc0(part 0) is current device
    
      *** USB eMMC Mode ***
    
    UMS: LUN 0, dev mmc 0, hwpart 0, sector 0x0, count 0x1d1f000
    dwc3_meson_gxl_force_mode: switching to Device Mode
    /

Back to your host machine do:

    lsusb

This should show an `Amlogic` device.

    Bus 002 Device 036: ID 1b8e:fada Amlogic, Inc. La Frite

Then list your block devices.

    lslbk

This should show one device which is close to the size of your eMMC.

    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    loop0         7:0    0  51.1M  1 loop 
    sda           8:0    1  14.6G  0 disk 
    nvme1n1     259:0    0 238.5G  0 disk 
    └─nvme1n1p1 259:1    0 238.5G  0 part /home
    nvme0n1     259:2    0 223.6G  0 disk 
    ├─nvme0n1p1 259:3    0   512M  0 part /boot/efi
    └─nvme0n1p2 259:4    0 223.1G  0 part /

On my laptop it is `sda` or `/dev/sda`.

Unmount it if it got mounted.

    sudo umount -l /dev/sda*

Then you can use `dd` to copy over the disk image.

    sudo dd if=./debian-12-base-arm64+arm64.img of=/dev/sda bs=512

This will take about 5-10 minutes. After it finishes, you can reboot the la frite.

## OS Specific Setup

From here your OS should be installed, here's some instructions for OSes I've tried.

* [Debian 12](OS_debian.md)
* [Armbian 25.2.2 (Debian Bookwork)](OS_armbian_bookworm.md)
