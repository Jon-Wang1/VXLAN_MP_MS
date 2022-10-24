
DC2_Super_Spine1>en
DC2_Super_Spine1#sho run
Building configuration...

Current configuration : 1327 bytes
!
version 15.7
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname DC2_Super_Spine1
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
 ip address 10.2.0.1 255.255.255.0
 ip pim sparse-mode
!
interface Ethernet0/0
 ip address 10.2.255.1 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
interface Ethernet0/1
 no ip address
 ip pim sparse-mode
 shutdown
 duplex auto
!
interface Ethernet0/2
 ip address 10.2.255.129 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
interface Ethernet0/3
 ip address 10.2.255.133 255.255.255.252
 ip pim sparse-mode
 duplex auto
!
router ospf 1
 network 0.0.0.0 255.255.255.255 area 0
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
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

DC2_Super_Spine1#            