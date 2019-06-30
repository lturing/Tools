# 树莓派无线路由器(NAT)

#### 环境

* Raspberry 3b+
* stretch lite(2019-04-08)

#### 安装镜像

#### 允许ssh
```
将制作好的镜像的sd卡，插入到window下，在sd根目录下创建 ssh 文件
```

#### 更换软件源和系统源
* vim /etc/apt/source.list (软件源)
```
deb http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi

to 

#deb http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi

```
[镜像源](http://www.raspbian.org/RaspbianMirrors)
* vim /etc/apt/source.list.d/raspi.list(系统源)
```
deb http://archive.raspberrypi.org/debian/ stretch main ui
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspberrypi.org/debian/ stretch main ui

to

#deb http://archive.raspberrypi.org/debian/ stretch main ui
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ stretch main ui
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://mirrors.ustc.edu.cn/debian/ stretch main ui
deb-src http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ stretch main ui
#deb-src http://archive.raspberrypi.org/debian/ stretch main ui

```

#### 更新
```
sudo apt update
sudo apt upgrade 

可能遇到的问题
1，1, The following signatures couldn't be verified because the public key is not available: NO_PUBKEY xxxxxxxx（'公钥编码'）
解决方法
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys '公钥编码'
```

#### 安装vim
```
sudo apt install vim 

# 开启鼠标复制
vim ~/.vimrc
set mouse=r
```

#### 安装 python3 package
```
sudo apt install python3-pip
sudo apt install python3-picamera
```

#### 安装 hostapd
> hostapd 的说明

```
sudo apt install hostapd 

sudo vim /etc/hostapd/hostapd.conf # adding the below contents.
- for 2.4G wifi
interface=wlan0
ssid=on_the_fly
hw_mode=g
channel=7
ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]
wmm_enabled=0
macaddr_acl=0
auth_algs=1
wpa=2
ignore_broadcast_ssid=0
wpa_passphrase=pleasure
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP


- for 5G wifi
interface=wlan0
driver=nl80211

hw_mode=a
ieee80211n=1
ieee80211ac=1
ieee80211d=1
ieee80211h=1
require_ht=1
require_vht=1
wmm_enabled=1
country_code=US

vht_oper_chwidth=1
channel=149
vht_oper_centr_freq_seg0_idx=155
ht_capab=[HT40-][HT40+][SHORT-GI-40][DSSS_CCK-40]

wpa=2
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP

ssid=on_the_fly
wpa_passphrase=pleasure
ignore_broadcast_ssid=0 # 1 hidden wifi


sudo vim /etc/default/hostapd.conf
DAEMON_CONF=""

to 

DAEMON_CONF="/etc/hostapd/hostapd.conf"

```

#### 安装 dnsmasq
> dnsmasq 的说明

```
sudo apt install dnsmasq

sudo vim /etc/dhcpcd.conf # add the following contents.
interface wlan0
static ip_address=192.168.6.1
static routers=192.168.6.1
static domain_name_servers=8.8.8.8

sudo vim /etc/dnsmasq.conf
interface=wlan0
domain-needed
bogus-priv
dhcp-range=192.168.6.8,192.168.6.25,12h
```

#### configure ip routing 
> At this point you have a wireless access point, but it doesn’t connect to your Pi’s wired Ethernet port, which is its connection to the Internet. Making this final link is pretty easy. 

```
# First, enable routing
sudo vim /etc/sysctl.conf
net.ipv4.ip_forward=1

# at the command line, tell the Pi how to decide which packets get routed. That’s done by executing these three commands:
sudo iptables -t nat -A  POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT

sudo apt install iptables-persistent # save the current iptables settings 
```

#### 取代 iptables-persistent 
> 由于某些原因，iptables-persistent 保存的iptables rules会清空

```
mkdir ~/sh && cd sh 
vim network
#!/bin/bash

#sysctl net.ipv4.ip_forward=1
iptables -F
#ip6tables -F


iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT

# ipv6
#ip6tables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
#ip6tables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
#ip6tables -A FORWARD -i wlan0 -o eth0 -j ACCEPT


#ip6tables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
#开启IPv6透传
#ebtables -t broute -A BROUTING -p ! ipv6 -i eth0 -j DROP
#brctl addbr ipv6br
#ifconfig ipv6br up
#brctl addif ipv6br eth0
#brctl addif ipv6br wlan0

#echo 1 > /proc/sys/net/ipv6/conf/all/forwarding
#service radvd start
#brctl addbr br0
#ifconfig br0 up
#brctl addif br0 eth0
#brctl addif br0 wlan0
#ebtables -t broute -A BROUTING -p ! ipv6 -j DROP

sh -c "iptables-save > /etc/iptables.ipv4.nat"
#sh -c "ip6tables-save > /etc/iptables.ipv6.nat"

iptables -F
iptables -X
iptables -t nat -F

#ip6tables -F
#ip6tables -X
#ip6tables -t nat -F
#sleep 2

iptables-restore < /etc/iptables.ipv4.nat
#ip6tables-restore < /etc/iptables.ipv6.nat

vim wifistart 
#!/bin/bash

# Redundant stops to make sure services are not running

/home/pi/sh/network

systemctl stop hostapd.service
systemctl stop dhcpcd.service
systemctl stop dnsmasq.service

systemctl start hostapd.service
systemctl start dhcpcd.service
systemctl start dnsmasq.service
#sleep 1

sudo vim /etc/rc.local
# add the following lines above exit 0;

/bin/bash /home/pi/sh/wifistart

```

#### 重启
```
sudo reboot now 
```

#### 参考文献
- [Using Raspberry Pi 3 running Stretch for a WiFi router](https://somesquares.org/blog/2017/10/Raspberry-Pi-router/)

