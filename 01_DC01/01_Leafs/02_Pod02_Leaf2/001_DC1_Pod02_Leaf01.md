hostname DC1_Pod2_Leaf1
nv overlay evpn
feature ospf
feature bgp
feature pim
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay

fabric forwarding anycast-gateway-mac 0000.0000.1234
ip pim ssm range 225.0.0.0/8
ip pim bsr forward listen
vlan 1,101-102,201-202,3001-3002
vlan 101
  vn-segment 10101
vlan 102
  vn-segment 10102
vlan 201
  vn-segment 10201
vlan 202
  vn-segment 10202
vlan 3001
  vn-segment 30001
vlan 3002
  vn-segment 30002

vrf context T1
  vni 30001
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
vrf context T2
  vni 30002
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn

interface Vlan101
  no shutdown
  vrf member T1
  ip address 202.101.1.1/24
  fabric forwarding mode anycast-gateway

interface Vlan102
  no shutdown
  vrf member T1
  ip address 202.101.2.1/24
  fabric forwarding mode anycast-gateway

interface Vlan201
  no shutdown
  vrf member T2
  ip address 202.102.1.1/24
  fabric forwarding mode anycast-gateway

interface Vlan202
  no shutdown
  vrf member T2
  ip address 202.102.2.1/24
  fabric forwarding mode anycast-gateway

interface Vlan3001
  no shutdown
  vrf member T1
  no ip redirects
  ip forward

interface Vlan3002
  no shutdown
  vrf member T2
  no ip redirects
  ip forward

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback0
  member vni 10101
    mcast-group 225.0.0.101
  member vni 10102
    mcast-group 225.0.0.102
  member vni 10201
    mcast-group 225.0.0.201
  member vni 10202
    mcast-group 225.0.0.202
  member vni 30001 associate-vrf
  member vni 30002 associate-vrf

interface Ethernet1/1
  no switchport
  medium p2p
  ip unnumbered loopback0
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  switchport mode trunk


interface loopback0
  ip address 10.1.2.1/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
line console
  exec-timeout 0
line vty
boot nxos bootflash:/nxos.9.2.2.bin 
router ospf 1
router bgp 65012
  template peer RR
    remote-as 65012
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.1.2.101
    inherit peer RR
evpn
  vni 10101 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 10102 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 10201 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 10202 l2
    rd auto
    route-target import auto
    route-target export auto
      