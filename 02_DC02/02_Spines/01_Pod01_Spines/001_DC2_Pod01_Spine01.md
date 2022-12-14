#### 初始化

```markdown
hostname DC2_Pod1_Spine01
line console
  exec-timeout 0
boot nxos bootflash:///nxos.9.2.2.bin 
interface loopback0
  ip address 10.2.1.101/32
interface Ethernet1/1
  no switchport
  medium p2p
  ip unnumbered loopback0
  no shutdown
interface Ethernet1/2
  no switchport
  medium p2p
  ip unnumbered loopback0
  no shutdown

interface Ethernet1/7
  no switchport
  medium p2p
  ip address 10.2.255.10/30
  no shutdown

```

#### Underlay配置

```markdown
feature ospf
router ospf 1
  router-id 10.2.1.101
interface loopback0
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/1
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/2
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/7
  ip router ospf 1 area 0.0.0.0


```

#### PIM配置

```markdown
feature pim
ip pim bsr forward listen
interface loopback0
  ip pim sparse-mode
interface Ethernet1/1-2
  ip pim sparse-mode

interface Ethernet1/7
  ip pim sparse-mode

```

### 启用EVPN控制平面和MP-BGP

```text
nv overlay evpn
feature bgp
router bgp 65201
  address-family l2vpn evpn
    retain route-target all
  template peer VTEP
    remote-as 65201
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
      route-reflector-client
  neighbor 10.2.1.1
    inherit peer VTEP
  neighbor 10.2.1.2
    inherit peer VTEP

```
#### 配置到BGW的BGP配置
```text
route-map UN permit 10 
  set ip next-hop unchanged
router bgp 65201
  template peer BGWs
    remote-as 65200
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community
      send-community extended
      route-map UN out
      rewrite-evpn-rt-asn
  neighbor 200.0.1.1
    inherit peer BGWs
  neighbor 200.0.1.2
    inherit peer BGWs
```





















hostname DC1_Pod2_Spine1

nv overlay evpn
feature ospf
feature bgp
feature pim

ip pim ssm range 232.0.0.0/8
ip pim bsr forward listen


route-map UN permit 10
  set ip next-hop unchanged
vrf context management


interface Ethernet1/1
  no switchport
  medium p2p
  ip unnumbered loopback0
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  no switchport
  medium p2p
  ip unnumbered loopback0
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

interface Ethernet1/3
  no switchport
  ip address 10.1.255.6/30
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown


interface loopback0
  ip address 10.1.2.101/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
line console
  exec-timeout 0
line vty
boot nxos bootflash:/nxos.9.2.2.bin 
router ospf 1
router bgp 65012
  address-family l2vpn evpn
    retain route-target all
  template peer BGW
    remote-as 65100
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community
      send-community extended
      route-map UN out
      rewrite-evpn-rt-asn
  template peer VTEP
    remote-as 65012
    update-source loopback0
    address-family l2vpn evpn
      send-community
      send-community extended
      route-reflector-client
  neighbor 10.1.1.101
    remote-as 65011
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community
      send-community extended
      route-map UN out
      rewrite-evpn-rt-asn
  neighbor 10.1.2.1
    inherit peer VTEP
  neighbor 10.1.2.2
    inherit peer VTEP
  neighbor 100.1.100.1
    inherit peer BGW
  neighbor 100.1.100.2
    inherit peer BGW
   