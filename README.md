# Libre Computer La Frite

## TOC

* General
* Hardware
* [Serial Boot]
* [UBOOT Upgrade](https://github.com/pjobson/libre-computer-la-frite/blob/main/UBOOT.md)
* [OS Install](https://github.com/pjobson/libre-computer-la-frite/blob/main/OS.md) 

## General



## Hardware

* [La Frite SBC](https://www.loverpi.com/products/libre-computer-board-aml-s805x-ac)
* USB Stick and/or [EMMC Modul](https://www.loverpi.com/products/libre-computer-board-emmc-5-x-module)
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

