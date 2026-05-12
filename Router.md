# R1 - Edge Router (Physical Device: Cisco 1841)

`R1` is the central router in the **Secure Enterprise Network**. It is the virtual representation of a **Cisco 1841 ISR router** and connects `CoreSwitch` with the `FIREWALL`.

## Physical device details

- **Real‑world model**: Cisco 1841 ISR  
- **Virtual model in Packet Tracer**: "Router 1841"  
- **Location**: Between core and firewall (edge)

## Configuration

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

- `10.0.0.1` → toward `CoreSwitch` (LAN).  
- `20.0.0.100` → toward `FIREWALL` (WAN).  
- Uses **RIP** and a default route to forward traffic to the firewall.  
