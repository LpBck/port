
#  ШПАРГАЛКА: Debian роутер в GNS3

## Топология

|Роль|Интерфейс|Сеть|
|---|---|---|
|Staff|ens4|10.10.0.0/26|
|Clients|ens5|10.10.0.64/26|
|Servers|ens6|10.10.0.128/26|
|Untrusted|ens7|10.10.0.192/26|
|WAN (NAT/Internet)|ens8|DHCP|

# 1. `/etc/network/interfaces`

```ini
auto lo
iface lo inet loopback


# STAFF
auto ens4
iface ens4 inet static
    address 10.10.0.1
    netmask 255.255.255.192


# CLIENTS
auto ens5
iface ens5 inet static
    address 10.10.0.65
    netmask 255.255.255.192


# SERVERS
auto ens6
iface ens6 inet static
    address 10.10.0.129
    netmask 255.255.255.192


# UNTRUSTED
auto ens7
iface ens7 inet static
    address 10.10.0.193
    netmask 255.255.255.192


# WAN
auto ens8
iface ens8 inet dhcp
```
# Включить роутинг 
```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p
```
# NAT-скрипт

файл:

```bash
nano /usr/local/bin/nat.sh
```

##  Содержимое:

```bash
#!/bin/bash

# включаем маршрутизацию
echo 1 > /proc/sys/net/ipv4/ip_forward

# чистим старые правила
iptables -F
iptables -t nat -F

# ======================
# NAT наружу
# ======================
iptables -t nat -A POSTROUTING -o ens8 -j MASQUERADE

# ======================
# LAN → WAN
# ======================
iptables -A FORWARD -i ens4 -o ens8 -j ACCEPT
iptables -A FORWARD -i ens5 -o ens8 -j ACCEPT
iptables -A FORWARD -i ens6 -o ens8 -j ACCEPT
iptables -A FORWARD -i ens7 -o ens8 -j ACCEPT

# ======================
# WAN → LAN (ответы)
# ======================
iptables -A FORWARD -i ens8 -o ens4 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i ens8 -o ens5 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i ens8 -o ens6 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i ens8 -o ens7 -m state --state ESTABLISHED,RELATED -j ACCEPT
```
## Сделать исполняемым:

```bash
chmod +x /usr/local/bin/nat.sh
```
## Запуск:

```bash
/usr/local/bin/nat.sh
```
# Проверка

```bash
ip a
ip r
iptables -t nat -L -v
```
