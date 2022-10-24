#### 初始化
```markdown
hostname DC1_Super_Spine2
no ip domain lookup
line con 0
 exec-timeout 0 0
 logging synchronous

interface Loopback0
 ip address 10.1.0.2 255.255.255.255
interface Ethernet1/0
 ip address 10.1.255.21 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet1/1
 ip address 10.1.255.25 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet1/2
 ip address 10.1.255.29 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet0/0
 ip address 10.1.255.33 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet0/1
 ip address 10.1.255.37 255.255.255.252
 ip ospf network point-to-point
 no shutdown

```

#### 配置OSPF
```markdown
router ospf 1
 network 0.0.0.0 255.255.255.255 area 0
```

#### 配置PIM
```markdown
ip multicast-routing 
interface loopback0
 ip pim sparse-mode
interface range ethernet 0/0-3 , ethernet 1/0-3
 ip pim sparse-mode
ip pim bsr-candidate Loopback0 0
ip pim rp-candidate loopback 0 priority 200

```





