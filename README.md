# Floating_routing
# Floating Static Route Lab (Cisco Packet Tracer)

A small dual-WAN redundancy lab demonstrating floating static routing for automatic failover between two internet paths, built and tested in Cisco Packet Tracer.

 Overview
in this lab LAN network 192.168.10.0 with gateway router 192.168.10.100 and to connect to the two ISPs 
(represented by `8.8.8.8`) and one is primary interface 192.1.1.1 and default routing which have administrator distance is 1. and other is backup link interface is 196.1.1.1 and set manually administrator distance is 10. and set the loopback 8.8.8.8 
here now traffic will goes to the internet on the primary link because the primary link AD distance is low than the back up when here the primary link is down due to some issues then our traffic will be automatically shift to the backup link. when the primary link is again recovered then the traffic will be back into the primary link.

 Topology

Laptop3 (192.168.10.1) ┐
                        ├── Switch5 ── Router10 ── Router11 (Primary) ── 8.8.8.8
Laptop4 (192.168.10.2) ┘        │
                                 └────── Router9 (Backup) ── 8.8.8.8

| Device | Role | Key Interfaces |
|---|---|---|
| Laptop3 / Laptop4 | End hosts | 192.168.10.1/24, 192.168.10.2/24 |
| Switch5 | L2 access switch | Fa0/1, Fa0/2, Fa0/3 |
| Router10 | Edge/gateway router | Gig0/1: 192.168.10.100, Gig0/2: 195.1.1.1, Gig0/0: 196.1.1.1 |
| Router11 | Primary WAN path (ISR4331) | Gig0/0/0: 195.1.1.2, Loopback: 8.8.8.8 |
| Router9 | Backup WAN path (ISR4331) | Gig0/0/0: 196.1.1.2, Loopback: 8.8.8.8 |

 Key Configuration

Router10 — Interfaces

interface Gig0/1
 ip address 192.168.10.100 255.255.255.0
 no shutdown

interface Gig0/2
 ip address 195.1.1.1 255.255.255.0
 no shutdown

interface Gig0/0
 ip address 196.1.1.1 255.255.255.0
 no shutdown


Router10 — Static Routes

Primary route (default AD = 1)
ip route 8.8.8.8 255.255.255.255 195.1.1.2

Floating backup route (AD = 10)
ip route 8.8.8.8 255.255.255.255 196.1.1.2 10


Router11 (Primary path)

interface Gig0/0/0
 ip address 195.1.1.2 255.255.255.0
 no shutdown

interface Loopback0
 ip address 8.8.8.8 255.255.255.255

ip route 192.168.10.0 255.255.255.0 195.1.1.1

Router9 (Backup path)

interface Gig0/0/0
 ip address 196.1.1.2 255.255.255.0
 no shutdown

interface Loopback0
 ip address 8.8.8.8 255.255.255.255

ip route 192.168.10.0 255.255.255.0 196.1.1.1

# Testing / Verification

1. From a laptop, ping `8.8.8.8` — traffic flows via **Router11** (primary route, AD 1).
2. Confirm active route on Router10:

   show ip route 8.8.8.8
   
3. Simulate a primary link failure:
   
   interface Gig0/2
    shutdown
   
4. Ping `8.8.8.8` again — traffic now routes via **Router9** (floating route, AD 10) since the primary route was withdrawn.
5. Re-enable the primary link (`no shutdown` on Gig0/2) — traffic switches back to the primary path automatically.

# Files

floatin.pkt  Packet Tracer lab file



# Topics

Cisco CCNA Packet Tracer Static Routing Floating Static Route WAN Redundancy Networking 
