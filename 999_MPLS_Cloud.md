#### 初始化
```text
hostname MPLS_Cloud
no ip domain lookup
line con 0
 exec-timeout 0 0
 logging synchronous
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
!
interface Ethernet0/0
 ip address 100.1.1.254 255.255.255.0
 no shutdown
!
interface Ethernet0/1
 ip address 100.1.2.254 255.255.255.0
 no shutdown
!
interface Ethernet0/2
 ip address 200.1.1.254 255.255.255.0
 no shutdown
!
interface Ethernet0/3
 ip address 200.1.2.254 255.255.255.0
 no shutdown
```








MPLS_Cloud>
MPLS_Cloud>
MPLS_Cloud>
MPLS_Cloud>
MPLS_Cloud>en
MPLS_Cloud#sho run
Building configuration...

Current configuration : 1395 bytes
!
version 15.7
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname MPLS_Cloud
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
 ip address 1.1.1.1 255.255.255.255
!
interface Ethernet0/0
 ip address 100.1.1.10 255.255.255.0
 no shutdown
!
interface Ethernet0/1
 ip address 100.1.2.10 255.255.255.0
 no shutdown
!
interface Ethernet0/2
 ip address 100.2.1.10 255.255.255.0
 no shutdown
!
interface Ethernet0/3
 ip address 100.2.2.10 255.255.255.0
 no shutdown
!
router bgp 10000
 bgp log-neighbor-changes
 bgp listen range 100.2.0.0/16 peer-group DC2
 bgp listen range 100.1.0.0/16 peer-group DC1
 network 1.1.1.1 mask 255.255.255.255
 redistribute connected
 neighbor DC1 peer-group
 neighbor DC1 remote-as 65100
 neighbor DC2 peer-group
 neighbor DC2 remote-as 65200
 maximum-paths eibgp 2
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
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
line con 0
 exec-timeout 0 0
 logging synchronous
line aux 0
line vty 0 4
 login
 transport input none
!
!
end

MPLS_Cloud#            