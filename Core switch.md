# CoreSwitch - Core Layer-3 Switch (Physical Device: Cisco 3560)

`CoreSwitch` is the core Layer‑3 switch in the **Secure Enterprise Network**. It is the virtual representation of a **Cisco 3560‑style L3 switch** and serves as the main inter‑VLAN router, DHCP server, and routing hub.

## Physical device details

- **Real‑world model**: Cisco Catalyst 3560  
- **Virtual model in Packet Tracer**: "Switch 3560‑24PS" / 3560‑style  
- **Location**: Core layer, network backbone

## Configuration

```bash
vlan 10
 name CS-Dept
!
vlan 20
 name DS-Dept
!
vlan 30
 name CSec-Dept
!
vlan 40
 name SWE-Dept
!
vlan 50
 name AI-Dept
!
vlan 60
 name IoT-Dept
!
vlan 70
 name Admin-Dept
!
vlan 80
 name HR-Dept
!
interface vlan 10
 ip address 192.168.1.1 255.255.255.0
!
interface vlan 20
 ip address 192.168.2.1 255.255.255.0
!
interface vlan 30
 ip address 192.168.3.1 255.255.255.0
!
interface vlan 40
 ip address 192.168.4.1 255.255.255.0
!
interface vlan 50
 ip address 192.168.5.1 255.255.255.0
!
interface vlan 60
 ip address 192.168.6.1 255.255.255.0
!
interface vlan 70
 ip address 192.168.7.1 255.255.255.0
!
interface vlan 80
 ip address 192.168.8.1 255.255.255.0
!
ip routing
!
ip dhcp excluded-address 192.168.1.1
ip dhcp excluded-address 192.168.2.1
ip dhcp excluded-address 192.168.3.1
ip dhcp excluded-address 192.168.4.1
ip dhcp excluded-address 192.168.5.1
ip dhcp excluded-address 192.168.6.1
ip dhcp excluded-address 192.168.7.1
ip dhcp excluded-address 192.168.8.1

ip dhcp pool VLAN10
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN20
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN30
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN40
 network 192.168.4.0 255.255.255.0
 default-router 192.168.4.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN50
 network 192.168.5.0 255.255.255.0
 default-router 192.168.5.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN60
 network 192.168.6.0 255.255.255.0
 default-router 192.168.6.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN70
 network 192.168.7.0 255.255.255.0
 default-router 192.168.7.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN80
 network 192.168.8.0 255.255.255.0
 default-router 192.168.8.1
 dns-server 8.8.8.8
!
router rip
 version 2
 network 192.168.1.0
 network 192.168.2.0
 network 192.168.3.0
 network 192.168.4.0
 network 192.168.5.0
 network 192.168.6.0
 network 192.168.7.0
 network 192.168.8.0
 no auto-summary
!
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

- Handles **inter‑VLAN routing** and **DHCP** for all 8 departments.  
- Uses **RIP** to advertise VLAN networks to `R1`.  
