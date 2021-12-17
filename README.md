# script_Dynamic_NAT_Overload


INTERNET(config)#interface fa0/0
INTERNET(config­if)#no shutdown 
INTERNET(config­if)#ip address 11.11.11.2 255.255.255.0
INTERNET(config­if)#exit
INTERNET(config)#interface loopback 1
INTERNET(config­if)#ip address 8.8.8.8 255.255.255.255
INTERNET(config­if)#


RIZ-R1(config)#interface fa0/0
RIZ-R1(config­if)#no shutdown 
RIZ-R1(config­if)#ip address 11.11.11.1 255.255.255.0
RIZ-R1(config­if)#exit
RIZ-R1(config)#interface fa0/1
RIZ-R1(config­if)#no shutdown 
RIZ-R1(config­if)#ip address 192.168.2.1 255.255.255.0
RIZ-R1(config­if)#exit
RIZ-R1(config)#


Config IP via desktop di PC-1
ip address : 192.168.2.100
subnet mark : 255.255.255.0
default gateway : 192.168.2.1


INTERNET(config)#router ospf 1
INTERNET(config­router)#network 8.8.8.0 0.0.0.255 area 0
INTERNET(config­router)#network 11.11.11.0 0.0.0.255 area 0
INTERNET(config­router)#exit
INTERNET(config)#

cek tabel routing :
INTERNET#show ip route 

RIZ-R1(config)#router ospf 1
RIZ-R1(config­router)#network 11.11.11.0 0.0.0.255 area 0
RIZ-R1(config­router)#
02:31:10: %OSPF­5­ADJCHG: Process 1, Nbr 8.8.8.8 on 
FastEthernet0/0 from LOADING to FULL, Loading Done
RIZ-R1(config­router)#exit
RIZ-R1(config)#


RIZ-R1#show ip route


Packet Tracer PC Command Line 1.0
C:>pinG 8.8.8.8
Pinging 10.10.10.2 with 32 bytes of data:
Request timed out.
Request timed out.
Request timed out.
Request timed out.
Ping statistics for 10.10.10.2:
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
C:>

tentukan interface Outside dan Inside NAT :

RIZ-R1(config)#interface fa0/0
RIZ-R1(config­if)#ip nat outside 
RIZ-R1(config­if)#exit
RIZ-R1(config)#interface fa0/1
RIZ-R1(config­if)#ip nat inside 
RIZ-R1(config­if)#exit
RIZ-R1(config)#


rule acess-list dengan permit network 192.168.2.0/24 . Dan buat konfigurasi NAT Overload :

RIZ-R1(config)#access­list 1 permit 192.168.2.0 0.0.0.255
RIZ-R1(config)#ip nat inside source list 1 interface fa0/0 
overload 
RIZ-R1(config)#


C:>ping 8.8.8.8
Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=14ms TTL=254
Reply from 8.8.8.8: bytes=32 time=49ms TTL=254
Reply from 8.8.8.8: bytes=32 time=25ms TTL=254
Reply from 8.8.8.8: bytes=32 time=22ms TTL=254
Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli­seconds:
    Minimum = 14ms, Maximum = 49ms, Average = 27ms
C:>


RIZ-R1#show ip nat translations 
Pro  Inside global     Inside local       Outside local      
Outside global
icmp 11.11.11.1:37     192.168.2.100:37   8.8.8.8:37 8.8.8.8:37
icmp 11.11.11.1:38     192.168.2.100:38   8.8.8.8:38 8.8.8.8:38
icmp 11.11.11.1:39     192.168.2.100:39   8.8.8.8:39 8.8.8.8:39
icmp 11.11.11.1:40     192.168.2.100:40   8.8.8.8:40 8.8.8.8:40
RIZ-R1#
