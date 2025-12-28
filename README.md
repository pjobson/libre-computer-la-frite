# Libre Computer La Frite AML-S805X-AC

## TOC

* [General](#general)
* [Required Hardware](#required-hardware)
* [Serial to USB / UART](#serial-to-usb--uart)
* [UBOOT Upgrade](#uboot-upgrade)
* [Upgrade Firmware](#upgrade-firmware)
* [OS Install](#os-install)
* [OpenHab](#openhab)

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
* 20-Pin Raspberry Pi compatible GPIO

Device Information (collected from a Debian install):

* [lscpu](outputs/lscpu.txt)
* [lshw](outputs/lshw.txt)
* [lsirq](outputs/lsirq.txt)
* [lsmod](outputs/lsmod.txt)
* [lsusb](outputs/lsusb.txt)
* [dmidecode](outputs/dmidecode.txt)
* [inxi-Fxxx](outputs/inxi-Fxxx.txt)

## Required Hardware

* [La Frite SBC](https://www.loverpi.com/products/libre-computer-board-aml-s805x-ac)
* USB Stick and/or [eMMC Modul](https://www.loverpi.com/products/libre-computer-board-emmc-5-x-module)
* A Linux PC or Laptop with the internets
* Serial to USB Cable / UART Cable
* USB-A to USB-C Cable OR USB-A to USB-A Cable
* Micro USB w/ Power Supply 5V 2.5A

## Serial to USB / UART

[UART.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/UART.md)

## UBOOT Upgrade

[UBOOT.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/UBOOT.md)

## Upgrade Firmware

[UpgradeFirmware.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/UpgradeFirmware.md)

## OS Install

[OS.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/OS.md)

## openHAB

[openHAB.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/openHAB.md)
