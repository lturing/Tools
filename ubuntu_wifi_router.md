## ref
- [Ubuntu_AP.md](https://gist.github.com/ExtremeGTX/ea1d1c12dde8261b263ab2fead983dc8)

please refer to [raspberry_wifi_router](https://github.com/lturing/Tools/blob/master/raspberry_wifi_router.md)

# Introduction

This tutorial for setting up Ubuntu Server (RPi 3B) as Wifi access point 

# 
# Overview:
The main steps can be listed as following:
1. Install required packages
1. Setup hostapd
1. Setup DNSmasq
1. Configure AP IP Address

## Install required packages
`sudo apt-get install hostapd dnsmasq`

## Hostapd
- The purpose of Hostapd is to set WiFi as an access point
- we need to write a new config file for hostapd
`sudo vi /etc/hostapd/hostapd.conf`

    ```
    interface=wlan0
    driver=nl80211
    ssid=MyWiFiNetwork
    hw_mode=g
    channel=7
    wmm_enabled=0
    macaddr_acl=0
    auth_algs=1
    ignore_broadcast_ssid=0
    wpa=2
    wpa_passphrase=12345678
    wpa_key_mgmt=WPA-PSK
    wpa_pairwise=TKIP
    rsn_pairwise=CCMP

    ```

- Then we need to tell hostapd to use our config file, edit `/etc/default/hostapd` and change the line starts with `#DAEMON_CONF`, remember to remove `#`

    ```
    DAEMON_CONF="/etc/hostapd/hostapd.conf"
    ```
- Then Let's start hostapd
    ```bash
    sudo systemctl unmask hostapd
    sudo systemctl enable hostapd
    sudo systemctl start hostapd
    ```

## dnsmasq
- The purpose of dnsmasq is to act as DHCP Server, so when a devies connects to Raspberry Pi it can get an IP assigned to it.

- make a backup of default config by:
`sudo cp /etc/dnsmasq.conf /etc/dnsmasq.conf.org`

- Create a new config file by:
`sudo vi /etc/dnsmasq.conf`

- This config file will automatically assign addresses between `192.168.4.2` and `192.168.4.20` with lease time `24` hours.

    ```
    interface=wlan0
    dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h

    ```
- Then Let's reload dnsmasq config
    ```bash
    sudo systemctl reload dnsmasq
    ```

## Solving startup Error:
- On System startup, dnsmasq will not wait for wlan0 interface to initialize and will fail with error `wlan0 not found`.

- We need to tell systemd to launch it after network get ready, so we will modify dnsmasq service file by adding `After=` and `Wants=` under `[Unit]` section.

    `sudo vi /lib/systemd/system/dnsmasq.service`

    ```
    [Unit]
    ...
    After=network-online.target
    Wants=network-online.target
    
    ```

## Config static IP
- Ubuntu uses cloud-init for initial setup, so will modify the following file to set wlan0 IP.
- `DON'T USE TABS IN THIS FILE, IT WILL NOT WORK, EVER!!`
- Modify the cloud-init file by `sudo vi /etc/netplan/50-cloud-init.yaml`
- Add the following content to the file:
    ```
            wlan0:
                dhcp4: false
                addresses:
                - 192.168.4.1/24
    ```
    
- The file will finally looks like this:
    ```
    # This file is generated from information provided by
    # the datasource.  Changes to it will not persist across an instance.
    # To disable cloud-init's network configuration capabilities, write a file
    # /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
    # network: {config: disabled}
    network:
        version: 2
        ethernets:
            eth0:
                dhcp4: true
                match:
                    macaddress: 12:34:56:78:ab:cd
                set-name: eth0
            wlan0:
                dhcp4: false
                addresses:
                - 192.168.4.1/24

    ```

## load iptable when reboot ubuntu (set once)
```
sudo apt-get install iptables-persistent
sudo dpkg-reconfigure iptables-persistent

# iptables-persistent will automatically load /etc/iptables/rules.v4 when startup
iptables -F
#ip6tables -F
iptables --list
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT

# ipv6
#ip6tables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
#ip6tables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
#ip6tables -A FORWARD -i wlan0 -o eth0 -j ACCEPT

sudo iptables-save | sudo tee /etc/iptables/rules.v4
# or
# iptables-save > /etc/iptables/rules.v4

iptables --list

```


Then I also edited sysctl.conf (/etc/sysctl.conf): Uncomment net.ipv4.ip_forward=1 This enables IPV4 packet routing across the network adaptors. Then added the following lines:         
```
#Disable IP6 entirely
net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.eth0.disable_ipv6=1
```

## Finally:
- Reboot your Raspberry Pi and check if you can connect to it over WiFi and can SSH.

## Notes:
- if you can't see Raspberry Pi Hot spot then `hostapd` is not working, you can check its logs by `sudo systemctl status hostapd`.

- if you can coonect to Raspberry Pi but can't get an IP then `dnsmasq` is not working, you can check its logs by `sudo systemctl status dnsmasq`.