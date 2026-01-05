# Debian Setup

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

You'll get an Invalid Signatures error if you just do `apt udate`.

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

You may get these warnings.

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

You can fix them with:

    sudo apt install firmware-bnx2

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
        bc lshw curl ntp

## Generate your ssh keys.

    su - pjobson
    ssh-keygen
    touch ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
    # add any keys here

## Optional: Install a Local Python `venv`

    su - pjobson
    python3 -m venv venv
    echo "export PATH=~/venv/bin:\$PATH" >> .bashrc
    source .bashrc

## Optional: Install Node.js

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

    echo "DNS=10.10.10.1" >> /etc/systemd/resolved.conf
    systemctl restart systemd-resolved.service

### Test It

Check your DHCP.

    ip a

Should show your `eth0` and `wlan0` (assuming you set `net.ifnames=0` above) and their corresponding ip addresses. After reboot it should show the same.

You should be able to reboot and see that your configuration sticks.

    ping google.com

