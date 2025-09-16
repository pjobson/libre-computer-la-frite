# Flashing to La Frite

Instructions for flashing to the La Frite SBC.

## Requirements

### Hardware

* La Frite SBC
* Serial to USB Cable / UART Cable
* USB-A to USB-C Cable OR USB-A to USB-A Cable
* Micro USB Power Adapter

### Software

    sudo apt install wget python3 python3-usb minicom unar
    cd ~/Desktop
    git clone https://github.com/pjobson/libre-computer-la-frite.git

## La Fritte Cable Setup

Disconnect all cables and devices to start.

### Serial to USB

Get your Serial to USB cable and connect to the 20-pin header.

           ┏━━━ Ground
           ┃
           ▼
       • • • • • • • • • • • • • • • • • • • •
       • • • • • • • • • • • • • • • • • • • •
         ▲ ▲
      U  ┃ ┃
      S  ┃ ┗━━━ RX
      B  ┗━━━━━ TX 

Cables vary, I'm using a USB to TTL 4-pin Wire from Pi Shop. Colors are:

* Red - VCC (5V) - Not Used
* Black - GND
* Green - TX
* White - RX

Plug the USB into your PC.

### USB to USB

The instructions show use USB-A to USB-A, I used USB-A to USB-C instead.

USB-A plugs into the port closest to the 20-pin header.

USB-C plugs into your PC.

## Upgrade U-Boot

U-Boot should be upgraded once.

First, open two terminals.

#### Terminal One

The `ttyUSB0` is pretty normal, if you're doing something special it may be a different one.

    minicom -D /dev/ttyUSB0 -b 115200

If it can't connect to `ttyUSB0` it may throw:

    minicom: cannot open /dev/ttyUSB0: No such file or directory

Just leave it running so you can reference it.

It should show:

    Welcome to minicom 2.9
    
    OPTIONS: I18n 
    Port /dev/ttyUSB0, 16:56:37
    
    Press CTRL-A Z for help on special keys

## U-Booting

Hold the UBOOT button which is opposite to the 20-pin header.

Power up the unit with the MicroUSB Power Adapter.

minicom should display

    GXL:BL1:9ac50e:bb16dc;FEAT:ADFC318C:0;POC:0;RCY:0;USB:0;

#### Terminal Two

    cd ~/Desktop/libre-computer-la-frite/pyamlboot-gxl
    ./flash-firmware.sh aml-s805x-ac

This should automatically download `aml-s805x-ac-boot.bin` and attempt to install it.

Mine first failed with:

    usb.core.USBTimeoutError: [Errno 110] Operation timed out
    Unable to push firmware updater to board.

I disconnected the power, pressed the UBOOT button, and powed back up.

    ./flash-firmware.sh aml-s805x-ac
    
This again downloaded the firmware and attempt to flash.

At this point, do not unplug the device, it is still flashing. In Terminal One it should eventually end with.

    Finished writing new firmware. The board can be safely powered off.
    
* First flash output: [flash-firmware_run1](https://github.com/pjobson/libre-computer-la-frite/blob/main/outputs/flash-firmware_run1.txt) 
* Second flash output: [flash-firmware_run2](https://github.com/pjobson/libre-computer-la-frite/blob/main/outputs/flash-firmware_run2.txt)
* Terminal output: [terminal_output](https://github.com/pjobson/libre-computer-la-frite/blob/main/outputs/terminal_output.txt)

## Install Operating System

Get an OS image: [https://distro.libre.computer/ci/](https://distro.libre.computer/ci/) 

Select whatever distro and version you want.

You should download images prefixed with `+amd64`.

In this example I'm using `debian-12-base-arm64+arm64.img.xz`.

    wget https://distro.libre.computer/ci/debian/12/debian-12-base-arm64%2Barm64.img.xz
    unar debian-12-base-arm64+arm64.img.xz

The La Frite has no internal storage, so you can use a USB stick or EMMC if you bought the module.

### USB Stick

You can use Balena Etcher or if you're adventurous `dd`.

    sudo fdisk -l

Find your stick's path, mine is: `/dev/sda`.

**Note:** Don't copy and paste sda if your USB stick isn't that path or you could wipe your hard drive.

    sudo dd if=./debian-12-base-arm64+arm64.img of=/dev/sda bs=512

#### Booting

At this point you can hook up the Le Frite to your monitor and keyboard, but if you want to you just use serial with the UART adapter and `minicom` as shown above to monitor the output.

Put the stick into the Le Frite and power on. 

If you're using `minicom` you can hit escape to bring up the boot menu or you can just wait and it'll automatically boot off the stick.

### EMMC

...

### Timezone

Set your timezone so you can update properly. I think the default is something like `UTC/etc`.

    dpkg-reconfigure tzdata

### Optional Edit Hostname

You can set your hostname to whatever you want.

    vi /etc/hostname


### Apt Update / Upgrade

    apt update
    apt upgrade
    apt autoremove

#### Invalid Signatures Error

If you get this error you can fix it by adding a package.

    The following signatures were invalid: EXPKEYSIG 2E5FB7FC58C58FFB deb.libre.computer <contact+deb@libre.computer>

Per [this](https://hub.libre.computer/t/signatures-were-invalid-expkeysig-2e5fb7fc58c58ffb/4166)...

    wget https://deb.libre.computer/repo/pool/main/libr/libretech-keyring/libretech-keyring_2024.05.19_all.deb
    dpkg -i libretech-keyring_2024.05.19_all.deb
    apt update
    apt upgrade
    rm libretech-keyring_2024.05.19_all.deb

### Add User

Add a non-root user for yourself and add you to the sudo group.

    adduser pjobson
    usermod -a -G sudo pjobson
    # netdev is for using iwctl which you need for wifi below
    usermod -a -G netdev pjobson

### Wireless Config

Insert your usb wifi adapter.

    lsusb

You should see it, something like this.

    Bus 001 Device 003: ID 148f:7601 Ralink Technology, Corp. MT7601U Wireless Adapter

#### IWCtl

Per the [debian docs use IWCtl](https://wiki.debian.org/WiFi/HowToUse#IWCtl).

    apt install iwd

**Disable `wpa_supplicant`**

    systemctl --now disable wpa_supplicant

**Enable `iwd`**

    systemctl --now enable iwd.service
    systemctl status iwd.service

**Enable Network Configuration**

    sed -i 's/#EnableNetworkConfiguration/EnableNetworkConfiguration/' /etc/iwd/main.conf

**Optionally Enable IPv6**

    sed -i 's/#EnableIPv6/EnableIPv6/' /etc/iwd/main.conf

**Restart iwd**

    systemctl restart iwd.service

**Configure with `iwctl`**

Change to your user which you hopefully added above.

    su - pjobson
    iwctl

This will drop you to the `[iwd]#` prompt.

    device list

Should show something like `wlan0` may be reported as something else for you.

                        Devices
    -----------------------------------------------------
    Name      Address           Powered  Adapter  Mode
    -----------------------------------------------------
    wlan0     aa:aa:aa:aa:aa:aa on       phy0     station

Scan for networks.

    station wlan0 scan
    station wlan0 get-networks

Should show something like this.

          Available networks
    -------------------------------
    Network name   Security  Signal
    -------------------------------
    WifiNetwork0   psk       ****
    WifiNetwork1   psk       ****
    WifiNetwork2   psk       ***

Connect to a network.

    station wlan0 connect WifiNetwork0

If it has spaces, put the name in quotes.  It'll then prompt you for the network password.

    quit

Test it.

    ip a
    ping google.com

### Useful Tools 

    apt install \
        openssh-client openssh-server \
        git git-filter-repo           \
        python3-pip                   \
        bc

Generate your ssh keys.

    su - pjobson
    ssh-keygen

