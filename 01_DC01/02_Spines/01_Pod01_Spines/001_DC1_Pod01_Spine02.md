#### 初始化

```markdown
hostname DC1_Pod1_Spine02
line console
  exec-timeout 0
boot nxos bootflash:/nxos.9.2.2.bin 
interface loopback0
  ip address 10.1.1.102/32
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
interface Ethernet1/3
  no switchport
  medium p2p
  ip unnumbered loopback0
  no shutdown
interface Ethernet1/4
  no switchport
  medium p2p
  ip unnumbered loopback0
  no shutdown

interface Ethernet1/5
  no switchport
  medium p2p
  ip address 10.1.255.6/30
  no shutdown
interface Ethernet1/6
  no switchport
  medium p2p
  ip address 10.1.255.26/30
  no shutdown

```


#### Underlay配置
```markdown
feature ospf
router ospf 1
  router-id 10.1.1.102
interface loopback0
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/1
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/2
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/3
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/4
  ip router ospf 1 area 0.0.0.0

interface Ethernet1/5
  ip router ospf 1 area 0.0.0.0
interface Ethernet1/6
  ip router ospf 1 area 0.0.0.0

```
#### PIM配置
```markdown
feature pim
ip pim ssm range 225.0.0.0/8
ip pim bsr forward listen
interface loopback0
  ip pim sparse-mode
interface Ethernet1/1-6
  ip pim sparse-mode

```



