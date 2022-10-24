#### 初始化
```markdown
hostname DC1_Super_Spine1
no ip domain lookup
line con 0
 exec-timeout 0 0
 logging synchronous

interface Loopback0
 ip address 10.1.0.1 255.255.255.255
interface Ethernet1/0
 ip address 10.1.255.1 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet1/1
 ip address 10.1.255.5 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet1/2
 ip address 10.1.255.9 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet0/2
 ip address 10.1.255.13 255.255.255.252
 ip ospf network point-to-point
 no shutdown
interface Ethernet0/3
 ip address 10.1.255.17 255.255.255.252
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
ip pim rp-candidate loopback 0 priority 100
```















DC1_Super_Spine1#
DC1_Super_Spine1#sho run
Building configuration...

Current configuration : 1343 bytes
!
version 15.7
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname DC1_Super_Spine1
!
boot-start-marker
boot-end-marker
!
!
!
no aaa new-model
!
!
!
clock timezone EET 2 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
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
!
!
!


!
!
!
!
no ip domain lookup
ip multicast-routing 
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
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
redundancy
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
!
!
!
interface Loopback0
 ip address 10.1.0.1 255.255.255.255
 ip pim sparse-mode
!
interface Ethernet0/0
 ip address 10.1.255.1 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
interface Ethernet0/1
 ip address 10.1.255.5 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
interface Ethernet0/2
 ip address 10.1.255.129 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
interface Ethernet0/3
 ip address 10.1.255.133 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
router ospf 1
 network 0.0.0.0 255.255.255.255 area 0
!
ip forward-protocol nd
!
!

ip pim bsr-candidate Loopback0 0
ip pim rp-candidate Loopback0
!
ipv6 ioam timestamp
!
!
!         
control-plane
!
!
!
!
!
!
!
!
     