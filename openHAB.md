# Install openHAB

This install is geared toward version 5.1.x using a Silicon Labs HubZ Smart Home Controller.

## Get Device Info

### Smart Hub Serial

    sudo dmesg | grep usb

Look for these lines and note the serial number.

    [30440.009222] usb 1-2: Product: HubZ Smart Home Controller
    [30440.013566] usb 1-2: Manufacturer: Silicon Labs
    [30440.018044] usb 1-2: SerialNumber: 1160015D
                                          ^
                                          + - Copy this down

### Device & Vendor ID

    lsusb

Get the Device and Vendor IDs

    Bus 001 Device 005: ID 10c4:8a2a Silicon Labs HubZ Smart Home Controller
                           ^    ^
                           |    + - Product ID (idProduct)
                           + ------ Vendor ID  (idVendor)

### Create udev Rule

    # delete it if it is already there, I had a corrupted one already
    sudo rm /etc/udev/rules.d/99-husbzb-1.rules
    sudo vi -w /etc/udev/rules.d/99-husbzb-1.rules

Add this, substituting SERIAL_NUMBER, PRODUCT_ID, and VENDOR_ID.

    SUBSYSTEM=="tty", ATTRS{idVendor}=="VENDOR_ID", ATTRS{idProduct}=="PRODUCT_ID", ATTRS{serial}=="SERIAL_NUMBER", ENV{ID_USB_INTERFACE_NUM}=="00", SYMLINK+="zwave", GROUP=“dialout”, MODE="0666"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="VENDOR_ID", ATTRS{idProduct}=="PRODUCT_ID", ATTRS{serial}=="SERIAL_NUMBER", ENV{ID_USB_INTERFACE_NUM}=="01", SYMLINK+="zigbee", GROUP=“dialout”, MODE="0666"

In vi you can do find and replace, mine for example:

    :%s/VENDOR_ID/10c4/g
    :%s/PRODUCT_ID/8a2a/g
    :%s/SERIAL_NUMBER/1160015D/g

Trigger the rule:

    sudo udevadm trigger

## Install JDK

Currently the latest version in apt is 17.x, you need at 21 for 5.1.x.

Archive repo is [here](https://jdk.java.net/archive/) you need the Linux/AArch64 version.

    wget https://download.java.net/java/GA/jdk22.0.1/c7ec1332f7bb44aeba2eb341ae18aca4/8/GPL/openjdk-22.0.1_linux-aarch64_bin.tar.gz
    tar xzvf openjdk-22.0.1_linux-aarch64_bin.tar.gz
    sudo mv jdk-21.0.2 /opt
    sudo chown -Rv root:root /opt/jdk-21.0.2

    sudo update-alternatives --install "/usr/bin/java" "java" "/opt/jdk-21.0.2/bin/java" 1
    sudo update-alternatives --install "/usr/bin/javac" "javac" "/opt/jdk-21.0.2/bin/javac" 1
    sudo update-alternatives --install "/usr/bin/jar" "jar" "/opt/jdk-21.0.2/bin/jar" 1
    sudo update-alternatives --set "java" "/opt/jdk-21.0.2/bin/java"
    sudo update-alternatives --set "javac" "/opt/jdk-21.0.2/bin/javac"
    sudo update-alternatives --set "jar" "/opt/jdk-21.0.2/bin/jar"

## Install openHAB

Full instructions are available [here](https://www.openhab.org/docs/installation/linux.html#package-repository-installation).

    curl -fsSL "https://openhab.jfrog.io/artifactory/api/gpg/key/public" | gpg --dearmor > openhab.gpg
    sudo mkdir /usr/share/keyrings
    sudo mv openhab.gpg /usr/share/keyrings
    sudo chmod u=rw,g=r,o=r /usr/share/keyrings/openhab.gpg

    # Stable Release, others are in the instructions above.
    echo 'deb [signed-by=/usr/share/keyrings/openhab.gpg] https://openhab.jfrog.io/artifactory/openhab-linuxpkg stable main' | sudo tee /etc/apt/sources.list.d/openhab.list

    # Apt install
    sudo apt update
    sudo apt install openhab
    # optionally
    sudo apt install openhab-addons

### Optionally Lock the Install

    sudo apt-mark hold openhab
    sudo apt-mark hold openhab-addons

### Optionally Unlock the Install

    sudo apt-mark unhold openhab
    sudo apt-mark unhold openhab-addons

## Open Browser

Navigate to: http://ip_address:8080/

If you don't know your IP, you can use:

    ip a


