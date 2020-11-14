# Praktická část

## Poznámky

- Je nutné nastavit NAT na R1
- OSPF očividně preferuje delší, zato ethernetovou (10/100) cestu
- Musím nastavit desc na portech ✔️

## 1. Výpisy konfigurace aktivních prvků

### ISP Router
```

```

### R1
```

```

### R2
```

```

### R3
```

```

### SW1
```

```

### SW2
```

```

## 2. Výpisy konfigurace VLANů na přepínačích

### SW1
```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/18, Fa0/19, Fa0/20, Fa0/21
                                                Fa0/22, Fa0/23, Fa0/24
24   A                                active    Fa0/1, Fa0/2
97   B                                active    Fa0/3, Fa0/4
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
24   enet  100024     1500  -      -      -        -    -        0      0
97   enet  100097     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```

### SW2
```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/4, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
24   A                                active    Fa0/1, Fa0/2
97   B                                active    Fa0/3
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
24   enet  100024     1500  -      -      -        -    -        0      0
97   enet  100097     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```

## 3. Sousedi na 2./3. vrstvě OSI-RM (show ip/ipv6 ospf neigh)

### R1
```

```

### R2
```

```

### R3
```

```

## 4. Výsledek úspěšného překladu NAT (vnitřní na vnější rozhraní)
```

```

## 5. Data směrovacího protokolu na 1 z rozhraní R2, počet naučených záznamů ve směrovacích tabulkách na jednotlivých směrovačích

### Rozhraní na R2
```

```

### Směrovací tabulka R1
```

```

### Směrovací tabulka R2
```

```

### Směrovací tabulka R3
```

```

## 6. Výpis cest pro IPv6 na R3 (viz minulý bod, show ipv6 route)
```

```

## 7. Výsledek příkazu ping a traceroute na router/server poskytovatele
```

```

## Všechny příkazy:

### ISP Router
```
enable
conf t
hostname ISP

interface fa0/0
ip address 10.0.0.1 255.255.255.252
ipv6 address 2001:4492:f34b:0005::1/64
no shutdown
description pripojeni do firemni site
exit
```

### R1
```
enable
conf t
hostname R1

interface fa0/0
ip address 10.0.0.2 255.255.255.252
ipv6 address 2001:4492:f34b:0005::2/64
no shutdown
description pripojeni do internetu (isp)
exit

interface se0/1/0
ip address 31.149.194.161 255.255.255.252
ipv6 address 2001:4492:f34b:0003::1/64
clock rate 64000
no shutdown
description propojeni s routerem R2
exit

interface se0/1/1
ip address 31.149.194.165 255.255.255.252
ipv6 address 2001:4492:f34b:0004::1/64
clock rate 64000
no shutdown
description propojeni s routerem R3
exit

ip route 0.0.0.0 0.0.0.0 10.0.0.1
router ospf 1
network 31.149.194.160 0.0.0.3 area 0
network 31.149.194.164 0.0.0.3 area 0
passive-interface fa0/0
default-information originate
exit
```

### R2
```
enable
conf t
hostname R2

interface se0/1/0
ip address 31.149.194.162 255.255.255.252
ipv6 address 2001:4492:f34b:0003::2/64
clock rate 64000
no shutdown
description propojeni s routerem R1
exit

interface fa0/0
ip address 31.149.194.65 255.255.255.192
ipv6 address 2001:4492:f34b:0002::1/64
no shutdown
description propojeni do segmentu S1
exit

interface fa0/1
ip address 172.31.170.65 255.255.255.192
ipv6 address 2001:4492:f34b:0000::1/64
no shutdown
description propojeni do VLAN A
exit

router ospf 1
network 31.149.194.160 0.0.0.3 area 0
network 31.149.194.64 0.0.0.63 area 0
network 172.31.170.64 0.0.0.63 area 0
exit
```

### R3
```
enable
conf t
hostname R3

interface se0/1/1
ip address 31.149.194.166 255.255.255.252
ipv6 address 2001:4492:f34b:0004::2/64
clock rate 64000
no shutdown
description propojeni s routerem R1
exit

interface fa0/0
ip address 172.31.170.66 255.255.255.192
ipv6 address 2001:4492:f34b:0000::2/64
no shutdown
description propojeni do VLAN A
exit

interface fa0/1
ip address 31.149.194.1 255.255.255.192
ipv6 address 2001:4492:f34b:0001::1/64
no shutdown
description propojeni do VLAN B
exit

router ospf 1
network 31.149.194.164 0.0.0.3 area 0
network 172.31.170.64 0.0.0.63 area 0
network 31.149.194.0 0.0.0.63 area 0
exit
```

### PC3 (settings)
```
IPv4 Address: 31.149.194.126
Subnet Mask: 255.255.255.252
Default Gateway: 31.149.194.65
DNS Server: 127.0.0.1 (localhost)

IPv6 Address: 2001:4492:f34b:0002:ffff:ffff:ffff:ffff/64
Default Gateway: 2001:4492:f34b:0002::1
DNS Server: ::1 (localhost)
```

### SW1
```
enable
conf t
hostname SW1

vtp mode transparent
vlan 24
name A
exit

vlan 97
name B
exit

interface fa0/1
switchport mode access
switchport access vlan 24
description propojeni s routerem R3 na VLAN A
exit

interface fa0/2
switchport mode access
switchport access vlan 24
description PC1A na VLAN A
exit

interface fa0/3
switchport mode access
switchport access vlan 97
description propojeni s routerem R3 na VLAN B
exit

interface fa0/4
switchport mode access
switchport access vlan 97
description PC1B na VLAN A
exit

interface fa0/5
switchport mode trunk
switchport trunk allowed vlan 24,97
description trunk mezi SW1 a SW2 (VLAN A a VLAN B)
exit
```

## PC1A
```
DHCP (to be done)
```

## PC1B
```
IPv4 Address: 31.149.194.61
Subnet Mask: 255.255.255.192
Default Gateway: 31.149.194.1
DNS Server: 31.149.194.126

IPv6 Address: 2001:4492:f34b:0001:ffff:ffff:ffff:fffe/64
Default Gateway: 2001:4492:f34b:0001::1
DNS Server: 2001:4492:f34b:0002:ffff:ffff:ffff:ffff
```

### SW2
```
enable
conf t
hostname SW2

vtp mode transparent
vlan 24
name A
exit

vlan 97
name B
exit

interface fa0/5
switchport mode trunk
switchport trunk allowed vlan 24,97
description trunk mezi SW1 a SW2 (VLAN A a VLAN B)
exit

interface fa0/1
switchport mode access
switchport access vlan 24
description propojeni s routerem R2 na VLAN A
exit

interface fa0/2
switchport mode access
switchport access vlan 24
description PC2A na VLAN A
exit

interface fa0/3
switchport mode access
switchport access vlan 97
description PC2B na VLAN B
exit
```

## PC2A
```
DHCP (to be done)
```

## PC2B
```
IPv4 Address: 31.149.194.62
Subnet Mask: 255.255.255.192
Default Gateway: 31.149.194.1
DNS Server: 31.149.194.126

IPv6 Address: 2001:4492:f34b:0001:ffff:ffff:ffff:ffff
Default Gateway: 2001:4492:f34b:1::1
DNS Server: 2001:4492:f34b:0002:ffff:ffff:ffff:ffff
```