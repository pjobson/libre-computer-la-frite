# Serial to USB / UART

You can interact with the device over serial instead of using the HDMI with a keyboard and mouse. This is necessary for flashing this unit.

## Cable Connection

Get your Serial to USB cable and connect to the 20-pin header.

           ┏━━━ Ground
           ┃
           ▼
       • • • • • • • • • • • • • • • • • • • •
       • • • • • • • • • • • • • • • • • • • •
         ▲ ▲                                   H
      U  ┃ ┃                                   D
      S  ┃ ┗━━━ RX                             M
      B  ┗━━━━━ TX                             I

Cables vary, I'm using a [USB to TTL 4-pin Wire](https://www.pishop.us/product/usb-to-ttl-4-pin-wire/) from Pi Shop. Colors are:

* Red - VCC (5V) - Not Used
* Black - GND
* Green - TX
* White - RX

## PC Connection

Plug the USB into your PC.

Find your device.

    ls -la /dev/serial/by-path/

Should show something like...

    controller.info.here -> ../../ttyUSB0

The `ttyUSB0` is pretty normal.

    minicom -D /dev/ttyUSB0 -b 115200

If it can't connect to `ttyUSB0` it may throw:

    minicom: cannot open /dev/ttyUSB0: No such file or directory

If it does connect it'll show:

    Welcome to minicom 2.9

    OPTIONS: I18n
    Port /dev/ttyUSB0, 16:56:37

    Press CTRL-A Z for help on special keys
