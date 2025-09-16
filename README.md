# Flashing to La Frite

Instructions for flashing to the La Frite SBC.

## Requirements

### Hardware

* La Frite SBC
* Serial to USB Cable / UART Cable
* USB-A to USB-C Cable OR USB-A to USB-A Cable
* Micro USB Power Adapter

### Software

    apt install wget python3 python3-usb minicom unar
    mkdir lafrite && cd lafrite

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

    wget https://github.com/libre-computer-project/pyamlboot/archive/gxl.zip
    unzip gxl.zip && rm gxl.zip
    cd pyamlboot-gxl
 
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

## Install Operating System

Get an OS image: [https://distro.libre.computer/ci/](https://distro.libre.computer/ci/) 

Select whatever distro and version you want.

You should download images prefixed with `+amd64`.

In this example I'm using `debian-12-base-arm64+arm64.img.xz`.

    wget https://distro.libre.computer/ci/debian/12/debian-12-base-arm64%2Barm64.img.xz
    unar debian-12-base-arm64+arm64.img.xz


