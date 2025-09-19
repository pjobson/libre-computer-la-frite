# Install An Operating System

Get an OS image: [https://distro.libre.computer/ci/](https://distro.libre.computer/ci/) 

Select whatever distro and version you want.

You should download images prefixed with `+amd64`.

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

Boot up using the [UART](https://github.com/pjobson/libre-computer-la-frite/blob/main/UART.md) method boot up to the boot menu holding the escape key after plugging in the unit.

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

Then you can use `dd` to copy over the disk image.

    sudo dd if=./debian-12-base-arm64+arm64.img of=/dev/sda bs=512

This will take about 5-10 minutes, after it finishes, you can reboot.

## Booting

At this point you can hook up the Le Frite to your monitor and keyboard, but if you want to you just use serial with the UART adapter and `minicom` as shown above to monitor the output.

If you're using `minicom` you can hit escape to bring up the boot menu or you can just wait and it'll automatically boot off the stick.

## Login

Default login is:

* username: root
* password: root

It will force you to change your password on first login.

## Timezone

Set your timezone so you can update properly, the default is `Etc/UTC` which is `UTC`, but caused me some problems.

    dpkg-reconfigure tzdata

## Optional Edit Hostname

You can set your hostname to whatever you want.

    vi /etc/hostname

## Apt Update / Upgrade

Plug-in your ethernet cable to your internets.

    apt update
    
At this point you'll probably get an Invalid Signatures error.

    The following signatures were invalid: EXPKEYSIG 2E5FB7FC58C58FFB deb.libre.computer <contact+deb@libre.computer>

Per [this](https://hub.libre.computer/t/signatures-were-invalid-expkeysig-2e5fb7fc58c58ffb/4166)...

    wget https://deb.libre.computer/repo/pool/main/libr/libretech-keyring/libretech-keyring_2024.05.19_all.deb
    dpkg -i libretech-keyring_2024.05.19_all.deb
    rm libretech-keyring_2024.05.19_all.deb

    apt update
    apt upgrade
    apt autoremove

This will take like 10 minutes or so.

### Warnings

You may get these warnings, they can be ignored.

    W: Possible missing firmware /lib/firmware/bnx2/bnx2-rv2p-09ax-6.0.17.fw for built-in driver bnx2
    W: Possible missing firmware /lib/firmware/bnx2/bnx2-rv2p-09-6.0.17.fw for built-in driver bnx2
    W: Possible missing firmware /lib/firmware/bnx2/bnx2-mips-09-6.2.1b.fw for built-in driver bnx2
    W: Possible missing firmware /lib/firmware/bnx2/bnx2-rv2p-06-6.0.15.fw for built-in driver bnx2
    W: Possible missing firmware /lib/firmware/bnx2/bnx2-mips-06-6.2.3.fw for built-in driver bnx2
    W: Possible missing firmware /lib/firmware/bnx2x/bnx2x-e2-7.13.15.0.fw for built-in driver bnx2x
    W: Possible missing firmware /lib/firmware/bnx2x/bnx2x-e1h-7.13.15.0.fw for built-in driver bnx2x
    W: Possible missing firmware /lib/firmware/bnx2x/bnx2x-e1-7.13.15.0.fw for built-in driver bnx2x
    W: Possible missing firmware /lib/firmware/bnx2x/bnx2x-e2-7.13.21.0.fw for built-in driver bnx2x
    W: Possible missing firmware /lib/firmware/bnx2x/bnx2x-e1h-7.13.21.0.fw for built-in driver bnx2x
    W: Possible missing firmware /lib/firmware/bnx2x/bnx2x-e1-7.13.21.0.fw for built-in driver bnx2x

## Add User

Add a non-root user for yourself and add you to the sudo group.

    adduser pjobson
    # add your user to sudo group so you can use sudo
    usermod -a -G sudo pjobson
    # netdev is for using iwctl which you need for wifi below
    usermod -a -G netdev pjobson

## Useful Tools 

    apt install \
        openssh-client openssh-server \
        git git-filter-repo           \
        python3-pip python3-venv      \
        bc lshw curl

### Generate your ssh keys.

    su - pjobson
    ssh-keygen

### Optional: Install a Local Python `venv`

    su - pjobson
    python3 -m venv venv
    echo "export PATH=~/venv/bin:\$PATH" >> .bashrc
    source .bashrc

### Optional: Install Node.js

    su - pjobson
    curl -L https://bit.ly/n-install | bash
    source .bashrc

## Wireless Config

Insert your usb wifi adapter.

    lsusb

You should see it, something like this.

    Bus 001 Device 003: ID 148f:7601 Ralink Technology, Corp. MT7601U Wireless Adapter

### Use Short Interface Names

This is optional, it'll set your interface names to `eth0` and `wlan0` instead of `end0` and `wlxa047d7105cb1`.

    sed -i 's/GRUB_CMDLINE_LINUX_DEFAULT="noquiet"/GRUB_CMDLINE_LINUX_DEFAULT="noquiet net.ifnames=0"/' /etc/default/grub
    sed -i 's/GRUB_CMDLINE_LINUX_DEFAULT=""//' /etc/default/grub
    update-grub
    reboot

### Configuration

For wifi we're going to use Connman, because why not.

    apt install connman
    connmanctl

This should drop you to the connman prompt, note empty items in `services` are networks with hidden SSIDs.

    connmanctl> enable wifi
    Enabled wifi
    connmanctl> scan wifi
    Scan completed for wifi
    connmanctl> services
        *AR Wired        ethernet_ae1589daf91e_cable
      WifiNetwork1   wifi_HASH_KEY_managed_psk
      WifiNetwork2   wifi_HASH_KEY_managed_psk
                     wifi_HASH_KEY_hidden_managed_ieee8021x
      ...

    connmanctl> agent on
    Agent registered
    connmanctl> connect wifi_HASH_KEY_managed_psk
    Agent RequestInput wifi_HASH_KEY_managed_psk
      Passphrase = [ Type=psk, Requirement=mandatory ]
    Passphrase? YOUR_PASSPHRASE_HERE
    connmanctl> [  621.809075] wlan0: authenticate with c0:56:27:b6:13:7a (local address=a0:47:d7:10:5c:b1)
    [  621.812128] wlan0: send auth to c0:56:27:b6:13:7a (try 1/3)
    [  621.819235] wlan0: authenticated
    [  621.823161] wlan0: associate with c0:56:27:b6:13:7a (try 1/3)
    [  621.845444] wlan0: RX AssocResp from c0:56:27:b6:13:7a (capab=0x431 status=0 aid=8)
    [  621.894930] wlan0: associated
    Connected wifi_HASH_KEY_managed_psk

You can setup a Static IP Address and Name Servers at this point if you'd like. The order is: `IP_ADDRESS NET_MASK GATEWAY_IP`

    connmanctl> config wifi_HASH_KEY_managed_psk --ipv4 manual 10.10.10.120 255.255.255.0 10.10.10.1
    
    connmanctl> quit

Connman configuration can be manually edited in: `/var/lib/connman`

### Add DNS

If your router provides dns you can use that or `8.8.8.8` or whatever you want.

    resolvectl dns wlan0 10.10.10.1
    resolvectl dns eth0 10.10.10.1
    systemctl restart systemd-resolved.service

### Test It

Check your DHCP.

    ip a

Should show your `eth0` and `wlan0` (assuming you set `net.ifnames=0` above) and their corresponding ip addresses. After reboot it should show the same.

You should be able to reboot and see that your configuration sticks.

    ping google.com

