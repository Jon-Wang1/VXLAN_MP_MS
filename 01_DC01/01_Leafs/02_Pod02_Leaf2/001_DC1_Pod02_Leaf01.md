#### 初始化

```markdown
hostname DC1_Pod2_Leaf1
line console
  exec-timeout 0
boot nxos bootflash:/nxos.9.2.2.bin 
interface loopback0
  ip address 10.1.2.1/32
interface Ethernet1/1
  no switchport
  medium p2p
  ip unnumbered loopback0
  no shutdown

```

#### Underlay配置
```markdown
feature ospf
router ospf 1
  router-id 10.1.2.1
interface loopback0
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/1
  ip router ospf 1 area 0.0.0.0

```

#### PIM配置
```markdown
feature pim
ip pim bsr forward listen
interface loopback0
  ip pim sparse-mode
interface Ethernet1/1
  ip pim sparse-mode

```

### EVPN VTEP 配置
#### 启用VXLAN和MP-BGP EVPN控制平面
```markdown
nv overlay evpn
feature bgp
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay

```

#### 配置EVPN租户VRF实例
```markdown
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

```

#### 为每个租户VRF实例创建第三层VNI
```markdown
vlan 3001
  vn-segment 30001
vlan 3002
  vn-segment 30002
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

```


#### 为二层网络配置EVPN二层VNI
```markdown
vlan 101
  vn-segment 10101
vlan 102
  vn-segment 10102
vlan 201
  vn-segment 10201
vlan 202
  vn-segment 10202
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
      

```


#### 为二层VNI配置SVI，并在SVI下启用任意播网关
```markdown
fabric forwarding anycast-gateway-mac 0000.0000.1234

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
```

#### 配置VXLAN隧道接口nve1，并关联VNI
```markdown
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

```


#### 在VTEP上配置MP-BGP
```text
router bgp 65102
  template peer RR
    remote-as 65102
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.1.2.101
    inherit peer RR

```


#### 配置VXLAN隧道接口nve1，并关联VNI
```markdown
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

router bgp 65102
  template peer RR
    remote-as 65102
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.1.2.101
    inherit peer RR


```



### config-all
```text

hostname DC1_Pod2_Leaf1
line console
  exec-timeout 0
boot nxos bootflash:/nxos.9.2.2.bin 
interface loopback0
  ip address 10.1.2.1/32
interface Ethernet1/1
  no switchport
  medium p2p
  ip unnumbered loopback0
  no shutdown


feature ospf
router ospf 1
  router-id 10.1.2.1
interface loopback0
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/1
  ip router ospf 1 area 0.0.0.0


feature pim
ip pim bsr forward listen
interface loopback0
  ip pim sparse-mode
interface Ethernet1/1
  ip pim sparse-mode


nv overlay evpn
feature bgp
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay


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


vlan 3001
  vn-segment 30001
vlan 3002
  vn-segment 30002
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


vlan 101
  vn-segment 10101
vlan 102
  vn-segment 10102
vlan 201
  vn-segment 10201
vlan 202
  vn-segment 10202
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
      

fabric forwarding anycast-gateway-mac 0000.0000.1234

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


router bgp 65102
  template peer RR
    remote-as 65102
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.1.2.101
    inherit peer RR


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

router bgp 65102
  template peer RR
    remote-as 65102
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.1.2.101
    inherit peer RR




```


hostname DC1_Pod2_Leaf1
nv overlay evpn
feature ospf
feature bgp
feature pim
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay

fabric forwarding anycast-gateway-mac 0000.0000.1234
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
      