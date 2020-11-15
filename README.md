# Praktická část

## Poznámky
- Když je jeden router DHCP server, tak další nemusí obsahovat žádný DHCP config, žejo?

## 1. Výpisy konfigurace aktivních prvků

### ISP Router
```
Building configuration...

Current configuration : 676 bytes
!
version 12.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname ISP
!
!
!
!
!
!
!
!
ip cef
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface FastEthernet0/0
 description pripojeni do firemni site
 ip address 10.0.0.1 255.255.255.252
 duplex auto
 speed auto
 ipv6 address 2001:4492:F34B:5::1/64
 ipv6 address 2001:4492:f34b: 5::1/64
!
interface FastEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
end
```

### R1
```
Building configuration...

Current configuration : 1652 bytes
!
version 12.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
!
!
!
!
!
!
!
no ip cef
ipv6 unicast-routing
!
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface FastEthernet0/0
 description pripojeni do internetu (isp)
 ip address 10.0.0.2 255.255.255.252
 ip nat outside
 duplex auto
 speed auto
 ipv6 address 2001:4492:F34B:5::2/64
!
interface FastEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Serial0/1/0
 description propojeni s routerem R2
 ip address 31.149.194.161 255.255.255.252
 ip nat inside
 ipv6 address 2001:4492:F34B:3::1/64
 ipv6 ospf 2 area 0
 clock rate 64000
!
interface Serial0/1/1
 description propojeni s routerem R3
 ip address 31.149.194.165 255.255.255.252
 ip nat inside
 ipv6 address 2001:4492:F34B:4::1/64
 ipv6 ospf 2 area 0
 clock rate 64000
!
interface Vlan1
 no ip address
 shutdown
!
router ospf 1
 log-adjacency-changes
 passive-interface FastEthernet0/0
 network 31.149.194.160 0.0.0.3 area 0
 network 31.149.194.164 0.0.0.3 area 0
 default-information originate
!
ipv6 router ospf 2
 default-information originate
 log-adjacency-changes
 redistribute connected metric 1 
 passive-interface FastEthernet0/0
!
ip nat pool vlan_A_NAT 31.149.194.129 31.149.194.158 netmask 255.255.255.224
ip nat inside source list 1 interface FastEthernet0/0 overload
ip classless
ip route 0.0.0.0 0.0.0.0 10.0.0.1 
!
ip flow-export version 9
!
ipv6 route ::/0 2001:4492:F34B:5::1
!
access-list 1 permit 172.31.170.64 0.0.0.63
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
end
```

### R2
```
Building configuration...

Current configuration : 1494 bytes
!
version 12.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R2
!
!
!
!
ip dhcp excluded-address 172.31.170.65 172.31.170.66
!
ip dhcp pool vlanA
 network 172.31.170.64 255.255.255.192
 default-router 172.31.170.65
 dns-server 31.149.194.126
!
!
!
no ip cef
ipv6 unicast-routing
!
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface FastEthernet0/0
 description propojeni do segmentu S1
 ip address 31.149.194.65 255.255.255.192
 duplex auto
 speed auto
 ipv6 address 2001:4492:F34B:2::1/64
 ipv6 ospf 2 area 0
!
interface FastEthernet0/1
 description propojeni do VLAN A
 ip address 172.31.170.65 255.255.255.192
 duplex auto
 speed auto
 ipv6 address 2001:4492:F34B::1/64
 ipv6 ospf 2 area 0
!
interface Serial0/1/0
 description propojeni s routerem R1
 ip address 31.149.194.162 255.255.255.252
 ipv6 address 2001:4492:F34B:3::2/64
 ipv6 ospf 2 area 0
!
interface Serial0/1/1
 no ip address
 clock rate 2000000
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
router ospf 1
 log-adjacency-changes
 passive-interface FastEthernet0/0
 network 31.149.194.160 0.0.0.3 area 0
 network 31.149.194.64 0.0.0.63 area 0
 network 172.31.170.64 0.0.0.63 area 0
!
ipv6 router ospf 2
 log-adjacency-changes
 redistribute connected metric 1 
 passive-interface FastEthernet0/0
!
ip classless
!
ip flow-export version 9
!
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
end
```

### R3
```
Building configuration...

Current configuration : 1249 bytes
!
version 12.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R3
!
!
!
!
!
!
!
!
no ip cef
ipv6 unicast-routing
!
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface FastEthernet0/0
 description propojeni do VLAN A
 ip address 172.31.170.66 255.255.255.192
 duplex auto
 speed auto
 ipv6 address 2001:4492:F34B::2/64
 ipv6 ospf 2 area 0
!
interface FastEthernet0/1
 description propojeni do VLAN B
 ip address 31.149.194.1 255.255.255.192
 duplex auto
 speed auto
 ipv6 address 2001:4492:F34B:1::1/64
 ipv6 ospf 2 area 0
!
interface Serial0/1/0
 description propojeni s routerem R1
 no ip address
 clock rate 2000000
 shutdown
!
interface Serial0/1/1
 ip address 31.149.194.166 255.255.255.252
 ipv6 address 2001:4492:F34B:4::2/64
 ipv6 ospf 2 area 0
!
interface Vlan1
 no ip address
 shutdown
!
router ospf 1
 log-adjacency-changes
 network 31.149.194.164 0.0.0.3 area 0
 network 172.31.170.64 0.0.0.63 area 0
 network 31.149.194.0 0.0.0.63 area 0
!
ipv6 router ospf 2
 log-adjacency-changes
 redistribute connected metric 1 
!
ip classless
!
ip flow-export version 9
!
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
end
```

### SW1
```
Building configuration...

Current configuration : 1540 bytes
!
version 12.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW1
!
!
!
!
vtp mode transparent
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan 24
 name A
!
vlan 97
 name B
!
interface FastEthernet0/1
 description propojeni s routerem R3 na VLAN A
 switchport access vlan 24
 switchport mode access
!
interface FastEthernet0/2
 description PC1A na VLAN A
 switchport access vlan 24
 switchport mode access
!
interface FastEthernet0/3
 description propojeni s routerem R3 na VLAN B
 switchport access vlan 97
 switchport mode access
!
interface FastEthernet0/4
 description PC1B na VLAN A
 switchport access vlan 97
 switchport mode access
!
interface FastEthernet0/5
 description trunk mezi SW1 a SW2 (VLAN A a VLAN B)
 switchport trunk allowed vlan 24,97
 switchport mode trunk
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface Vlan1
 no ip address
 shutdown
!
!
!
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end
```

### SW2
```
Building configuration...

Current configuration : 1442 bytes
!
version 12.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW2
!
!
!
!
vtp mode transparent
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan 24
 name A
!
vlan 97
 name B
!
interface FastEthernet0/1
 description propojeni s routerem R2 na VLAN A
 switchport access vlan 24
 switchport mode access
!
interface FastEthernet0/2
 description PC2A na VLAN A
 switchport access vlan 24
 switchport mode access
!
interface FastEthernet0/3
 description PC2B na VLAN B
 switchport access vlan 97
 switchport mode access
!
interface FastEthernet0/4
!
interface FastEthernet0/5
 description trunk mezi SW1 a SW2 (VLAN A a VLAN B)
 switchport trunk allowed vlan 24,97
 switchport mode trunk
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface Vlan1
 no ip address
 shutdown
!
!
!
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end
```

## 2. Výpisy konfigurace VLANů na přepínačích

### SW1
```
SW1#sh vlan
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
SW2#sh vlan
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
R1#sh ip ospf neigh
Neighbor ID     Pri   State           Dead Time   Address         Interface
172.31.170.66     0   FULL/  -        00:00:36    31.149.194.166  Serial0/1/1
172.31.170.65     0   FULL/  -        00:00:30    31.149.194.162  Serial0/1/0

R1#sh ipv6 ospf neigh
Neighbor ID     Pri   State           Dead Time   Interface ID    Interface
172.31.170.66     0   FULL/  -        00:00:31    4               Serial0/1/1
172.31.170.65     0   FULL/  -        00:00:35    3               Serial0/1/0
```

### R2
```
R2#sh ip ospf neigh
Neighbor ID     Pri   State           Dead Time   Address         Interface
172.31.170.66     1   FULL/DR         00:00:37    172.31.170.66   FastEthernet0/1
31.149.194.165    0   FULL/  -        00:00:37    31.149.194.161  Serial0/1/0

R2#sh ipv6 ospf neigh
Neighbor ID     Pri   State           Dead Time   Interface ID    Interface
172.31.170.66     1   FULL/DR         00:00:31    1               FastEthernet0/1
31.149.194.165    0   FULL/  -        00:00:31    3               Serial0/1/0
```

### R3
```
R3#sh ip ospf neigh
Neighbor ID     Pri   State           Dead Time   Address         Interface
172.31.170.65     1   FULL/BDR        00:00:31    172.31.170.65   FastEthernet0/0
31.149.194.165    0   FULL/  -        00:00:31    31.149.194.165  Serial0/1/1

R3#sh ipv6 ospf neigh
Neighbor ID     Pri   State           Dead Time   Interface ID    Interface
172.31.170.65     1   FULL/BDR        00:00:34    2               FastEthernet0/0
31.149.194.165    0   FULL/  -        00:00:34    4               Serial0/1/1
```

## 4. Výsledek úspěšného překladu NAT (vnitřní na vnější rozhraní) (po pingu z PC2A)
```
R1#sh ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 10.0.0.2:57       172.31.170.67:57   10.0.0.1:57        10.0.0.1:57
icmp 10.0.0.2:58       172.31.170.67:58   10.0.0.1:58        10.0.0.1:58
icmp 10.0.0.2:59       172.31.170.67:59   10.0.0.1:59        10.0.0.1:59
icmp 10.0.0.2:60       172.31.170.67:60   10.0.0.1:60        10.0.0.1:60
icmp 10.0.0.2:61       172.31.170.67:61   10.0.0.1:61        10.0.0.1:61
icmp 10.0.0.2:62       172.31.170.67:62   10.0.0.1:62        10.0.0.1:62
icmp 10.0.0.2:63       172.31.170.67:63   10.0.0.1:63        10.0.0.1:63
```

## 5. Data směrovacího protokolu na 1 z rozhraní R2, počet naučených záznamů ve směrovacích tabulkách na jednotlivých směrovačích

### Rozhraní na R2 (se0/1/0)
```
R2#show ip ospf interface se0/1/0
Serial0/1/0 is up, line protocol is up
  Internet address is 31.149.194.162/30, Area 0
  Process ID 1, Router ID 172.31.170.65, Network Type POINT-TO-POINT, Cost: 64
  Transmit Delay is 1 sec, State POINT-TO-POINT,
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:01
  Index 3/3, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1 , Adjacent neighbor count is 1
    Adjacent with neighbor 31.149.194.165
  Suppress hello for 0 neighbor(s)
```

### Směrovací tabulka R1
```
R1#sh ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 10.0.0.1 to network 0.0.0.0

     10.0.0.0/30 is subnetted, 1 subnets
C       10.0.0.0 is directly connected, FastEthernet0/0
     31.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
O       31.149.194.0/26 [110/65] via 31.149.194.166, 00:00:23, Serial0/1/1
O       31.149.194.64/26 [110/65] via 31.149.194.162, 00:00:23, Serial0/1/0
C       31.149.194.160/30 is directly connected, Serial0/1/0
C       31.149.194.164/30 is directly connected, Serial0/1/1
     172.31.0.0/26 is subnetted, 1 subnets
O       172.31.170.64 [110/65] via 31.149.194.162, 00:00:23, Serial0/1/0
                      [110/65] via 31.149.194.166, 00:00:23, Serial0/1/1
S*   0.0.0.0/0 [1/0] via 10.0.0.1

R1#sh ipv6 route
IPv6 Routing Table - 11 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       U - Per-user Static route, M - MIPv6
       I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       D - EIGRP, EX - EIGRP external
S   ::/0 [1/0]
     via 2001:4492:F34B:5::1
O   2001:4492:F34B::/64 [110/65]
     via FE80::260:5CFF:FE73:CA01, Serial0/1/0
     via FE80::2D0:FFFF:FEE2:D401, Serial0/1/1
O   2001:4492:F34B:1::/64 [110/65]
     via FE80::2D0:FFFF:FEE2:D401, Serial0/1/1
O   2001:4492:F34B:2::/64 [110/65]
     via FE80::260:5CFF:FE73:CA01, Serial0/1/0
C   2001:4492:F34B:3::/64 [0/0]
     via ::, Serial0/1/0
L   2001:4492:F34B:3::1/128 [0/0]
     via ::, Serial0/1/0
C   2001:4492:F34B:4::/64 [0/0]
     via ::, Serial0/1/1
L   2001:4492:F34B:4::1/128 [0/0]
     via ::, Serial0/1/1
C   2001:4492:F34B:5::/64 [0/0]
     via ::, FastEthernet0/0
L   2001:4492:F34B:5::2/128 [0/0]
     via ::, FastEthernet0/0
L   FF00::/8 [0/0]
     via ::, Null0
```

### Směrovací tabulka R2
```
R2#sh ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 31.149.194.161 to network 0.0.0.0

     31.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
O       31.149.194.0/26 [110/2] via 172.31.170.66, 00:00:35, FastEthernet0/1
C       31.149.194.64/26 is directly connected, FastEthernet0/0
C       31.149.194.160/30 is directly connected, Serial0/1/0
O       31.149.194.164/30 [110/65] via 172.31.170.66, 00:00:35, FastEthernet0/1
     172.31.0.0/26 is subnetted, 1 subnets
C       172.31.170.64 is directly connected, FastEthernet0/1
O*E2 0.0.0.0/0 [110/1] via 31.149.194.161, 00:01:15, Serial0/1/0

R2#sh ipv6 route
IPv6 Routing Table - 11 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       U - Per-user Static route, M - MIPv6
       I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       D - EIGRP, EX - EIGRP external
OE2 ::/0 [110/1]
     via FE80::2E0:F9FF:FE1D:8501, Serial0/1/0
C   2001:4492:F34B::/64 [0/0]
     via ::, FastEthernet0/1
L   2001:4492:F34B::1/128 [0/0]
     via ::, FastEthernet0/1
O   2001:4492:F34B:1::/64 [110/2]
     via FE80::2D0:FFFF:FEE2:D401, FastEthernet0/1
C   2001:4492:F34B:2::/64 [0/0]
     via ::, FastEthernet0/0
L   2001:4492:F34B:2::1/128 [0/0]
     via ::, FastEthernet0/0
C   2001:4492:F34B:3::/64 [0/0]
     via ::, Serial0/1/0
L   2001:4492:F34B:3::2/128 [0/0]
     via ::, Serial0/1/0
O   2001:4492:F34B:4::/64 [110/65]
     via FE80::2D0:FFFF:FEE2:D401, FastEthernet0/1
OE2 2001:4492:F34B:5::/64 [110/1]
     via FE80::2E0:F9FF:FE1D:8501, Serial0/1/0
L   FF00::/8 [0/0]
     via ::, Null0
```

### Směrovací tabulka R3
```
R3#sh ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 31.149.194.165 to network 0.0.0.0

     31.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
C       31.149.194.0/26 is directly connected, FastEthernet0/1
O       31.149.194.64/26 [110/2] via 172.31.170.65, 00:02:54, FastEthernet0/0
O       31.149.194.160/30 [110/65] via 172.31.170.65, 00:02:54, FastEthernet0/0
C       31.149.194.164/30 is directly connected, Serial0/1/1
     172.31.0.0/26 is subnetted, 1 subnets
C       172.31.170.64 is directly connected, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 31.149.194.165, 00:03:34, Serial0/1/1

R3#sh ipv6 route
IPv6 Routing Table - 11 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       U - Per-user Static route, M - MIPv6
       I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       D - EIGRP, EX - EIGRP external
OE2 ::/0 [110/1]
     via FE80::2E0:F9FF:FE1D:8501, Serial0/1/1
C   2001:4492:F34B::/64 [0/0]
     via ::, FastEthernet0/0
L   2001:4492:F34B::2/128 [0/0]
     via ::, FastEthernet0/0
C   2001:4492:F34B:1::/64 [0/0]
     via ::, FastEthernet0/1
L   2001:4492:F34B:1::1/128 [0/0]
     via ::, FastEthernet0/1
O   2001:4492:F34B:2::/64 [110/2]
     via FE80::260:5CFF:FE73:CA02, FastEthernet0/0
O   2001:4492:F34B:3::/64 [110/65]
     via FE80::260:5CFF:FE73:CA02, FastEthernet0/0
C   2001:4492:F34B:4::/64 [0/0]
     via ::, Serial0/1/1
L   2001:4492:F34B:4::2/128 [0/0]
     via ::, Serial0/1/1
OE2 2001:4492:F34B:5::/64 [110/1]
     via FE80::2E0:F9FF:FE1D:8501, Serial0/1/1
L   FF00::/8 [0/0]
     via ::, Null0
```

## 6. Výpis cest pro IPv6 na R3 (viz minulý bod, show ipv6 route)
```
R3#sh ipv6 route
IPv6 Routing Table - 11 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       U - Per-user Static route, M - MIPv6
       I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       D - EIGRP, EX - EIGRP external
OE2 ::/0 [110/1]
     via FE80::2E0:F9FF:FE1D:8501, Serial0/1/1
C   2001:4492:F34B::/64 [0/0]
     via ::, FastEthernet0/0
L   2001:4492:F34B::2/128 [0/0]
     via ::, FastEthernet0/0
C   2001:4492:F34B:1::/64 [0/0]
     via ::, FastEthernet0/1
L   2001:4492:F34B:1::1/128 [0/0]
     via ::, FastEthernet0/1
O   2001:4492:F34B:2::/64 [110/2]
     via FE80::260:5CFF:FE73:CA02, FastEthernet0/0
O   2001:4492:F34B:3::/64 [110/65]
     via FE80::260:5CFF:FE73:CA02, FastEthernet0/0
C   2001:4492:F34B:4::/64 [0/0]
     via ::, Serial0/1/1
L   2001:4492:F34B:4::2/128 [0/0]
     via ::, Serial0/1/1
OE2 2001:4492:F34B:5::/64 [110/1]
     via FE80::2E0:F9FF:FE1D:8501, Serial0/1/1
L   FF00::/8 [0/0]
     via ::, Null0
```

## 7. Výsledek příkazu ping a traceroute na router/server poskytovatele (PC2A)
```
C:\>ping 10.0.0.1

Pinging 10.0.0.1 with 32 bytes of data:

Reply from 10.0.0.1: bytes=32 time=18ms TTL=253
Reply from 10.0.0.1: bytes=32 time=11ms TTL=253
Reply from 10.0.0.1: bytes=32 time=2ms TTL=253
Reply from 10.0.0.1: bytes=32 time=1ms TTL=253

Ping statistics for 10.0.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 1ms, Maximum = 18ms, Average = 8ms

C:\>tracert 10.0.0.1

Tracing route to 10.0.0.1 over a maximum of 30 hops: 

  1   1 ms      0 ms      0 ms      172.31.170.65
  2   12 ms     6 ms      0 ms      31.149.194.161
  3   1 ms      1 ms      1 ms      10.0.0.1

Trace complete.
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

ip route 0.0.0.0 0.0.0.0 10.0.0.1
router ospf 1
network 31.149.194.160 0.0.0.3 area 0
network 31.149.194.164 0.0.0.3 area 0
passive-interface fa0/0
default-information originate
exit

ipv6 unicast-routing

ipv6 route ::/0 2001:4492:f34b:5::1
ipv6 router ospf 2
redistribute connected metric 1
default-information originate
passive-interface fa0/0
exit

interface fa0/0
ip address 10.0.0.2 255.255.255.252
ipv6 address 2001:4492:f34b:0005::2/64
no shutdown
description pripojeni do internetu (isp)
ip nat outside
exit

interface se0/1/0
ip address 31.149.194.161 255.255.255.252
ipv6 address 2001:4492:f34b:0003::1/64
clock rate 64000
no shutdown
description propojeni s routerem R2
ip nat inside
ipv6 ospf 2 area 0
exit

interface se0/1/1
ip address 31.149.194.165 255.255.255.252
ipv6 address 2001:4492:f34b:0004::1/64
clock rate 64000
no shutdown
description propojeni s routerem R3
ip nat inside
ipv6 ospf 2 area 0
exit

ip nat pool vlan_A_NAT 31.149.194.129 31.149.194.158 netmask 255.255.255.224
ip nat inside source list 1 interface fa0/0 overload
access-list 1 permit 172.31.170.64 0.0.0.63
```

### R2
```
enable
conf t
hostname R2

router ospf 1
network 31.149.194.160 0.0.0.3 area 0
network 31.149.194.64 0.0.0.63 area 0
network 172.31.170.64 0.0.0.63 area 0
passive-interface fa0/0
exit

ipv6 unicast-routing

ipv6 router ospf 2
redistribute connected metric 1
passive-interface fa0/0
exit

interface se0/1/0
ip address 31.149.194.162 255.255.255.252
ipv6 address 2001:4492:f34b:0003::2/64
clock rate 64000
no shutdown
description propojeni s routerem R1
ipv6 ospf 2 area 0
exit

interface fa0/0
ip address 31.149.194.65 255.255.255.192
ipv6 address 2001:4492:f34b:0002::1/64
no shutdown
description propojeni do segmentu S1
ipv6 ospf 2 area 0
exit

interface fa0/1
ip address 172.31.170.65 255.255.255.192
ipv6 address 2001:4492:f34b:0000::1/64
no shutdown
description propojeni do VLAN A
ipv6 ospf 2 area 0
exit

ip dhcp pool vlanA
network 172.31.170.64 255.255.255.192
default-router 172.31.170.65
dns-server 31.149.194.126
exit

ip dhcp excluded-addr 172.31.170.65 172.31.170.66
```

### R3
```
enable
conf t
hostname R3

router ospf 1
network 31.149.194.164 0.0.0.3 area 0
network 172.31.170.64 0.0.0.63 area 0
network 31.149.194.0 0.0.0.63 area 0
exit

ipv6 unicast-routing

ipv6 router ospf 2
redistribute connected metric 1
exit

interface se0/1/1
ip address 31.149.194.166 255.255.255.252
ipv6 address 2001:4492:f34b:0004::2/64
clock rate 64000
no shutdown
description propojeni s routerem R1
ipv6 ospf 2 area 0
exit

interface fa0/0
ip address 172.31.170.66 255.255.255.192
ipv6 address 2001:4492:f34b:0000::2/64
no shutdown
description propojeni do VLAN A
ipv6 ospf 2 area 0
exit

interface fa0/1
ip address 31.149.194.1 255.255.255.192
ipv6 address 2001:4492:f34b:0001::1/64
no shutdown
description propojeni do VLAN B
ipv6 ospf 2 area 0
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
DHCP
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
DHCP
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