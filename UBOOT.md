# Upgrade U-Boot

U-Boot should be upgraded once.

## Software

You'll need this repo on your host linux machine.

    sudo apt install wget python3 python3-usb minicom unar
    cd ~/Desktop
    git clone https://github.com/pjobson/libre-computer-la-frite.git
    cd libre-computer-la-frite

## Startup

Disconnect all cables and devices and open two terminals.

## Terminal One

Follow the directions in [UART.md](https://github.com/pjobson/libre-computer-la-frite/blob/main/UART.md) to open a serial connection.

## U-Booting

Hold the UBOOT button which is opposite to the 40-pin header.

Power up the unit with the MicroUSB Power Adapter.

`minicom` should display

    GXL:BL1:9ac50e:bb16dc;FEAT:ADFC318C:0;POC:0;RCY:0;USB:0;

### Terminal Two

    # You should already be in `libre-computer-la-frite`
    cd pyamlboot-gxl
    ./flash-firmware.sh aml-s805x-ac

This should automatically download `aml-s805x-ac-boot.bin` and attempt to install it.

Mine first failed with:

    ...
    usb.core.USBTimeoutError: [Errno 110] Operation timed out
    Unable to push firmware updater to board.

I disconnected the power, pressed the UBOOT button, and powed back up.

    ./flash-firmware.sh aml-s805x-ac
    
This again downloaded the firmware and attempt to flash.

At this point, do not unplug the device, it is still flashing. In Terminal One it should eventually end with.

    Finished writing new firmware. The board can be safely powered off.

Full terminal logs from my system.

* First flash output: [flash-firmware_run1](https://github.com/pjobson/libre-computer-la-frite/blob/main/outputs/flash-firmware_run1.txt) 
* Second flash output: [flash-firmware_run2](https://github.com/pjobson/libre-computer-la-frite/blob/main/outputs/flash-firmware_run2.txt)
* Terminal output: [terminal_output](https://github.com/pjobson/libre-computer-la-frite/blob/main/outputs/terminal_output.txt)

