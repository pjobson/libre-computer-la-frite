# Libre Computer La Frite AML-S805X-AC

## TOC

* [General](#general)
* [Required Hardware](#required-hardware)
* [Serial to USB / UART](#serial-to-usb--uart)
* [UBOOT Upgrade](https://github.com/pjobson/libre-computer-la-frite/blob/main/UBOOT.md)
* [Upgrade Firmware](#upgrade-firmware)
* [OS Install](https://github.com/pjobson/libre-computer-la-frite/blob/main/OS.md)

## General

The La Frite is a barebones SBC with either 512MB or 1GB of RAM and no built in storage. 

These units can be boot from: 

* EtherealOS
* eMMC
* USB

The unit's hardware consists of:

* 4 Core Cortex-A53 @ 1.416GHz
    * Cryptography Extensions
* 2G + 3P ARM Mali-450 @ 666MHz
    * OpenGL ES 1.1 / 2.0
    * OpenVG 1.1
* 512MB or 1GB of DDR4 RAM
* 2x USB 2.0 Ports
* eMMC Port
* [Meson IR Receiver](https://github.com/CoreELEC/remotes)
* 100 Mb Ethernet
* HDMI 2.0 with 1080P HDR Support

Outputs

* [lscpu](...)
* [lshw](outputs/lshw.txt)

## Required Hardware

* [La Frite SBC](https://www.loverpi.com/products/libre-computer-board-aml-s805x-ac)
* USB Stick and/or [eMMC Modul](https://www.loverpi.com/products/libre-computer-board-emmc-5-x-module)
* Serial to USB Cable / UART Cable
* USB-A to USB-C Cable OR USB-A to USB-A Cable
* Micro USB Power Adapter

## Serial to USB / UART

You can interact with the device over serial instead of using the HDMI with a keyboard and mouse. This is necessary for flashing this unit.

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

Cables vary, I'm using a [USB to TTL 4-pin Wire](https://www.pishop.us/product/usb-to-ttl-4-pin-wire/) from Pi Shop. Colors are:

* Red - VCC (5V) - Not Used
* Black - GND
* Green - TX
* White - RX

Plug the USB into your PC.

## UBOOT Upgrade

[UBOOT.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/UBOOT.md)

## Upgrade Firmware

After you do your UBOOT upgrade, you can flash new firmware.

* Connect to Ethernet
* Hook-up your Serial to USB
* Start Up minicom
* Hold the ESC key
* Power up the device

This will load:

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

Select `Firmware Update`, this should automatically connect to Libre Computer and upgrade your device's firmware.

## OS Install

[OS.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/OS.md)