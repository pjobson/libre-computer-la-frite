# Libre Computer La Frite AML-S805X-AC

## TOC

* [General](#general)
* [Links](#links)
* [Required Hardware](#required-hardware)
* [Serial to USB / UART](#serial-to-usb--uart)
* [UBOOT Upgrade](#uboot-upgrade)
* [Upgrade Firmware](#upgrade-firmware)
* [OS Install](#os-install)
* [openHAB](#openhab)

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

## Links

### Reference

* [Libre Computer Hub](https://hub.libre.computer/t/libre-computer-boards-overview-and-resources/2847)
* [Headers Reference](https://hub.libre.computer/t/gpio-pin-header-maps-and-wiring-tool-for-libre-computer-boards/28)
* [Performance Data](https://hub.libre.computer/t/libre-computer-board-performance-test-and-data/2833)
* [Schematics](https://hub.libre.computer/t/libre-computer-board-hardware-schematics-links/36)
* [Test Reports and Certifications](https://hub.libre.computer/t/libre-computer-board-hardware-certification-links/2832)

### Operating Systems

* [UEFI BIOS](https://hub.libre.computer/t/libre-computer-board-bios-firmware-update-images/3113)
* [Debian 11 & 12](https://hub.libre.computer/t/debian-12-bookworm-and-11-bullseye-for-libre-computer-boards/230)
* [Fedora](https://hub.libre.computer/t/fedora-for-libre-computer-boards/3225)
* [Ubuntu 22.04](https://hub.libre.computer/t/ubuntu-22-04-jammy-lts-for-libre-computer-boards/20)
* [OpenSUSE](https://hub.libre.computer/t/opensuse-for-libre-computer-boards/3226)
* [Raspian 11 & 12](https://hub.libre.computer/t/raspbian-12-bookworm-and-11-bullseye-for-libre-computer-boards/82)
* [Lakka](https://hub.libre.computer/t/lakka-for-libre-computer-boards/231/1)
* [CoreELEC](https://hub.libre.computer/t/coreelec-for-libre-computer-amlogic-boards/234)
* [LibreELEC](https://hub.libre.computer/t/libreelec-for-libre-computer-boards/233)
* [Android 7 & 8 & 9](https://hub.libre.computer/t/android-for-libre-computer-boards/232)
* [Armbian](https://www.armbian.com/la-frite/)

## Required Hardware

* [La Frite SBC](https://www.loverpi.com/products/libre-computer-board-aml-s805x-ac)
* USB Stick and/or [eMMC Module](https://www.loverpi.com/products/libre-computer-board-emmc-5-x-module)
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
