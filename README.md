# Secure Enterprise Network

## 1. Overview

This project simulates a **mini‑university network** with **8 departments**. Each department has its own **VLAN**, so there are **8 VLANs total**. The network is structured as follows:

- **Layer‑2 switches**: `S1`, `S2`, `S3`, `S4` (each connects 2 VLANs).  
- **Core Layer‑3 switch**: `CoreSwitch` (handles inter‑VLAN routing and DHCP).  
- **Router**: `R1` (connected between `CoreSwitch` and the firewall).  
- **Firewall**: `FIREWALL` (ASA 5506‑X, protects internal network from ISP).  

Routing is done using **RIP** because the network is small and simple, making configuration easier while still being sufficient for this campus‑style design.  

---

## 2. Departments and VLANs

Each department corresponds to one VLAN with its own network and gateway. The department names are:

| VLAN | Department Name | VLAN ID | Subnet | Gateway (CoreSwitch) |
|------|------------------|---------|--------|------------------------|
| VLAN 10 | **Computer Sciences** | 10 | `192.168.1.0/24` | `192.168.1.1` |
| VLAN 20 | **Data Science** | 20 | `192.168.2.0/24` | `192.168.2.1` |
| VLAN 30 | **Cyber Security** | 30 | `192.168.3.0/24` | `192.168.3.1` |
| VLAN 40 | **Software Eng.** | 40 | `192.168.4.0/24` | `192.168.4.1` |
| VLAN 50 | **Artificial Int.** | 50 | `192.168.5.0/24` | `192.168.5.1` |
| VLAN 60 | **IoT** | 60 | `192.168.6.0/24` | `192.168.6.1` |
| VLAN 70 | **Admin** | 70 | `192.168.7.0/24` | `192.168.7.1` |
| VLAN 80 | **HR** | 80 | `192.168.8.0/24` | `192.168.8.1` |

Each VLAN has **10 PCs** using IP addresses assigned by **DHCP**, with the default gateway set to the `CoreSwitch` SVI IP for that VLAN.  

---

## 3. Core Layer‑3 Switch (CoreSwitch) Configuration

`CoreSwitch` is responsible for:

- Inter‑VLAN routing.  
- DHCP for all 8 VLANs.  
- Advertising its connected VLAN networks to `R1` via RIP.

### 3.1 VLAN creation and SVIs (gateways)

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
```

### 3.2 DHCP configuration (for all VLANs)

The following DHCP configuration provides automatic IP addressing to all PCs in the 8 departments. This is one global DHCP section applied to all VLANs.

```bash
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
```

### 3.3 RIP configuration (for all VLANs)

RIP is used for simplicity and because the network is small. The `CoreSwitch` advertises its connected VLAN networks to `R1` using RIP version 2. This is one routing section that covers all VLANs.

```bash
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
```

---

## 4. Router (R1) Configuration

`R1` sits between `CoreSwitch` and `FIREWALL`, and uses **RIP** to learn the internal VLAN networks.

```bash
enable
configure terminal
hostname R1
!
interface fastEthernet 0/0
 ip address 10.0.0.1 255.255.255.0
 no shutdown
!
interface fastEthernet 0/1
 ip address 20.0.0.100 255.255.255.0
 no shutdown
!
router rip
 version 2
 network 10.0.0.0
 network 20.0.0.0
 no auto-summary
!
ip route 0.0.0.0 0.0.0.0 20.0.0.1
end
```

- `10.0.0.1` → LAN side (toward `CoreSwitch`).  
- `20.0.0.100` → WAN side (toward `FIREWALL` WAN `20.0.0.1`).  

---

## 5. CoreSwitch Default Route

`CoreSwitch` sends all traffic not in its own VLANs to `R1`:

```bash
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

---

## 6. Firewall (FIREWALL – ASA 5506‑X)

`FIREWALL` is configured via GUI with the following IPs and security rules.

### 6.1 IP Addresses (GUI)

- **LAN interface (e.g., GigabitEthernet 1/0)**:
  - IPv4 Address: `10.0.0.2`  
  - Subnet Mask: `255.255.255.0`  
- **WAN interface (e.g., GigabitEthernet 1/1)**:
  - IPv4 Address: `20.0.0.1`  
  - Subnet Mask: `255.255.255.0`  

These are the only IPs on the firewall; no other IP addresses are used in its interfaces.

### 6.2 Firewall rules (GUI)

- **Inbound Rules**:
  - Allow traffic from `10.0.0.0/24` (internal network) to `20.0.0.0/24` and beyond (ISP).  
  - Deny `ICMP` from outside unless required.  
- **Only allowed IP ranges**:
  - Internal VLANs: `192.168.1.0` – `192.168.8.0/24`  
  - Internal gateway: `10.0.0.0/24`  
  - External WAN: `20.0.0.0/24`  

No other IP address is accepted; any packet from a different network is dropped by the firewall.  

---

## 7. ISP Connection

An **ISP‑like router** (or cloud labeled “ISP”) is connected to the **WAN side of `FIREWALL`**:

- ISP uses `20.0.0.2/24` (or another IP in the same subnet) and has a default route toward the simulated Internet.  
- `FIREWALL` only forwards traffic from the allowed internal networks to the ISP, blocking all other addresses.  

Traffic flow:

**Departments PCs → `CoreSwitch` → `R1` → `FIREWALL` → ISP → Internet**

---

## 8. Layer‑2 Switches (S1–S4) Configuration

Each Layer‑2 switch (`S1`, `S2`, `S3`, `S4`) connects **2 VLANs** from the 8 departments and links to `CoreSwitch` via trunk.

Typical pattern (port numbers can be adjusted; example for `S1`):

```bash
hostname S1
!
vlan 10
 name CS-Dept
!
vlan 20
 name DS-Dept
!
interface range fa0/1 - 10
 switchport mode access
 switchport access vlan 10
!
interface range fa0/11 - 20
 switchport mode access
 switchport access vlan 20
!
interface fa0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 spanning-tree portfast trunk
!
end
```

`S2` (for VLAN 30, 40), `S3` (VLAN 50, 60), and `S4` (VLAN 70, 80) follow the same structure, changing VLAN IDs and trunk allowed VLANs.  

---

## 9. Security and Restrictions

- **Passwords enabled on all devices**:
  - `CoreSwitch`, `S1–S4`, `R1`, and `FIREWALL` are configured with:
    - Enable secret password.  
    - Console / VTY passwords (as required by your lab).  
- **No other IP addresses allowed**:
  - The entire network only uses:
    - `192.168.1.0` – `192.168.8.0/24` (departments)  
    - `10.0.0.0/24` (internal gateway)  
    - `20.0.0.0/24` (WAN)  
  - Any packet with an IP outside these ranges is blocked by the firewall and/or ACLs.  

---

## 10. Summary

- There are **8 departments**, each with its own VLAN and IP range on `CoreSwitch`.  
- **Inter‑VLAN routing** and **DHCP** are handled by `CoreSwitch`.  
- **RIP** is used between `CoreSwitch` and `R1` for simple dynamic routing in this small campus network.  
- **`S1–S4`** provide Layer‑2 connectivity for access‑layer PCs.  
- **`R1`** and **`FIREWALL`** secure the edge and prevent access from unauthorized IP ranges.  

This design makes the **Secure Enterprise Network** project a **simple, secure, and clearly structured campus‑style network** suitable for a mini‑university scenario.  
