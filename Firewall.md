# FIREWALL-ASA5506 - ASA 5506-X Firewall (Physical Device: Cisco ASA 5506-X)

`FIREWALL` is the security gateway in the **Secure Enterprise Network**. It is the virtual representation of a **Cisco ASA 5506‑X** firewall and sits between `R1` and the `ISP-Router`.

## Physical device details

- **Real‑world model**: Cisco ASA 5506‑X  
- **Virtual model in Packet Tracer**: "ASA 5506‑X"  
- **Location**: Between internal LAN and ISP (perimeter firewall)

## Configuration (via GUI)

- **LAN interface (e.g., GigabitEthernet 1/0)**:
  - IPv4 Address: `10.0.0.2`  
  - Subnet Mask: `255.255.255.0`  
- **WAN interface (e.g., GigabitEthernet 1/1)**:
  - IPv4 Address: `20.0.0.1`  
  - Subnet Mask: `255.255.255.0`  

Only these IPs are used; no other IP addresses are configured on the firewall interfaces.

## Firewall rules (GUI)

- **Inbound Rules**:
  - Allow `10.0.0.0/24` (internal network) to `20.0.0.0/24` and beyond.  
  - Deny `ICMP` from outside (optional, for extra security).  
- **Only allowed IP ranges**:
  - Internal VLANs: `192.168.1.0` – `192.168.8.0/24`  
  - Internal gateway: `10.0.0.0/24`  
  - External WAN: `20.0.0.0/24`  

Any packet with an IP outside these ranges is dropped by the firewall.  
