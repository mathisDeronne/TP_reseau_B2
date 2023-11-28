# TP3 INFRA : Premiers pas GNS, Cisco et VLAN

🌞 **Commençons simple**
- définissez les IPs statiques sur les deux VPCS
```
PC1> ip 10.3.1.1/24
Checking for duplicate address...
PC1 : 10.3.1.1 255.255.255.0
```
```
PC2> ip 10.3.1.2/24
Checking for duplicate address...
PC2 : 10.3.1.2 255.255.255.0
```
- `ping` un VPCS depuis l'autre
```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=1.939 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=2.554 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=2.474 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=1.998 ms
```

- afficher la CAM table du switch et vérifier les MAC qui s'y trouvent (Google pour ça, c'est pas dans le mémo)
```
IOU1#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/0
   1    0050.7966.6801    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
```

# II. VLAN

🌞 **Adressage**

- définissez les IPs statiques sur tous les VPCS
```
PC3> ip 10.3.1.3/24
Checking for duplicate address...
PC3 : 10.3.1.3 255.255.255.0
```
- vérifiez avec des `ping` que tout le monde se ping
```
PC3> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=3.347 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=2.426 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=64 time=1.330 ms
```
```
PC3> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=1.791 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=2.250 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=1.923 ms
```
- déclaration des VLANs sur le switch `sw1`
- ajout des ports du switches dans le bon VLAN (voir [le tableau d'adressage de la topo 2 juste au dessus](#2-adressage-topologie-2))
```
IOU1#conf t
IOU1(config)#vlan 10
IOU1(config-vlan)#name admins
IOU1(config-vlan)#exit
IOU1(config)#vlan 20
IOU1(config-vlan)#name guests
IOU1(config-vlan)#exit
IOU1(config)#exit
IOU1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et0/1, Et0/2, Et0/3
                                                Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   admins                           active
20   guests                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0
1003 tr    101003     1500  -      -      -        -    -        0      0
1004 fdnet 101004     1500  -      -      -        ieee -        0      0
1005 trnet 101005     1500  -      -      -        ibm  -        0      0


Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
IOU1(config)#interface Ethernet0/0
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#exit
IOU1(config)#interface Ethernet0/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#exit
IOU1(config)#interface Ethernet0/2
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 20
IOU1(config-if)#exit
```

- `pc1` et `pc2` doivent toujours pouvoir se ping


```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=1.608 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=4.431 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=2.275 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=1.892 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=1.631 ms

PC1> ping 10.3.1.3

host (10.3.1.3) not reachable
```
- `pc3` ne ping plus personne

```
PC3> ping 10.3.1.1

host (10.3.1.1) not reachable

PC3> ping 10.3.1.2

host (10.3.1.2) not reachable
```
