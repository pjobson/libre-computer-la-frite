# Armbian Debian Bookworm Setup

## Booting

At this point you can hook up the Le Frite to your monitor and keyboard, but if you want to you just use serial with the UART adapter and `minicom` as shown above to monitor the output.

If you're using `minicom` you can hit escape to bring up the boot menu or you can just wait and it'll automatically boot off the stick.

## Login

Armbian will automatically log you in as root and ask you to create a password.

Then it'll try to get you to create a new user.  I skipped the user creation by doing `CTRL-C`, so I could do it later.

This dropped my to `root`'s bash shell, but echo was disabled, so I just reboot.

## Timezone

Set your timezone so you can update properly, the default is `Etc/UTC` which is `UTC`, but caused me some problems.

    dpkg-reconfigure tzdata

## Update & Upgrade

Regular `apt update` will throw some errors, so use this instead the first time.

    apt-get --allow-releaseinfo-change update
    apt upgrade
    apt autoremove

## Install Some Useful Stuff

    apt install ntp vim               \
        openssh-client openssh-server \
        git git-filter-repo           \
        python3-pip python3-venv      \
        bc lshw curl

## Optional Edit Hostname

You can set your hostname to whatever you want.

    vi /etc/hostname

## Armbian Config

From here you can mess around with `armbian-config` or do some stuff yourself.

## Add User

Add a non-root user for yourself and add you to the sudo group.

    adduser pjobson
    # add your user to sudo group so you can use sudo
    usermod -a -G sudo pjobson
    # netdev is for using iwctl which you need for wifi below
    usermod -a -G netdev pjobson

## Generate your ssh keys.

    su - pjobson
    ssh-keygen
    touch ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
    # add any keys here
    vi ~/.ssh/authorized_keys

## Optional: Install a Local Python `venv`

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

## Install X Windows

If you want a GUI, you'll need to install it yourself.

    apt install xfce4 xorg xserver-xorg xinit   \
                pulseaudio pulseaudio-equalizer \
                lightdm xfce4-power-manager

I installed xfce4, other lightweight desktop environments include: openbox, icewm, lxqt, mate, tinywm, fluxbox, enlightenment, (many more I'm sure).

I'm using an unpowered Realtek RTS5411 chipset USB hub for keyboard & mouse.

    startx

## Optional: Redshift

    apt install redshift-gtk redshift

## Optional: Kiosk Mode

https://wiki.xfce.org/howto/kiosk_mode

### Disable Suspend/Sleep/Hibernate

Only really for special application uses.

    sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target

### Disable Screen Saver & Blank

    /usr/bin/xset s noblank
    /usr/bin/xset s off
    /usr/bin/xset dpms 0 0 0
    /usr/bin/xset -dpms

### Disable Power Management

    vi ~/.config/xfce4/xfconf/xfce-perchannel-xml

Make it:

    <?xml version="1.0" encoding="UTF-8"?>
    <channel name="xfce4-power-manager" version="1.0">
      <property name="xfce4-power-manager" type="empty">
        <property name="blank-on-ac" type="int" value="0"/>
        <property name="dpms-on-ac-sleep" type="uint" value="0"/>
        <property name="dpms-on-ac-off" type="uint" value="0"/>
        <property name="dpms-enabled" type="bool" value="false"/>
      </property>
    </channel>
