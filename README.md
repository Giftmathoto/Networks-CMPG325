# CMPG325-2026-057 — Kgalagadi Heritage Tours

## Milestone 2 — Client Implementation Review

**Project ID:** CMPG325-2026-057  
**Client ID:** CLI-057  
**Organisation:** Kgalagadi Heritage Tours  
**Technical Challenge:** SSH  
**Addressing Block:** `172.30.32.0/23`  
**Growth Requirement:** 40% expected user-group growth within 3 years  
**Change Request:** Guest Wi-Fi for visitors, isolated from internal resources.

---

## 1. Project Overview

This project implements a secure and scalable network for Kgalagadi Heritage Tours (KHT) using Cisco Packet Tracer.

The implemented network provides:

- VLAN-based departmental segmentation
- Router-on-a-stick inter-VLAN routing
- DHCP for user and management networks
- SSH version 2 for secure device administration
- Departmental and guest wireless connectivity
- Guest network isolation
- DNS service for internal clients
- NAT/PAT for Internet connectivity
- Management VLAN 99
- Static addressing for infrastructure/server devices

The current Packet Tracer implementation has been tested for internal connectivity, SSH management, guest isolation, DNS resolution, DHCP, NAT/PAT and Internet access.

---

## 2. Implemented Topology

The network uses an extended-star topology with KHT-SW1 as the core switch.

### Core devices

| Device | Role |
|---|---|
| KHT-R1 | Router-on-a-stick, DHCP, NAT/PAT and WAN gateway |
| KHT-SW1 | Core switch |
| ADMIN-SW | Administration access switch |
| OPERATIONS-SW | Operations access switch |
| FINANCE-SW | Finance access switch |
| SERVER-SW | Server access switch |
| GUEST-SW | Guest network access switch |
| MANAGEMENT-SW | Management network access switch |
| ISP | Simulated Internet provider |
| Server 0 | DNS/Internet test services |

The router-to-core connection uses an 802.1Q trunk:

```text
KHT-R1 G0/0
      |
      | 802.1Q trunk
      |
KHT-SW1 Fa0/24
```

The WAN connection is:

```text
KHT-R1 G0/1
203.0.113.2/30
      |
      |
ISP G0/0
203.0.113.1/30
```

---

## 3. VLAN Implementation

| VLAN | Name | Purpose | Gateway |
|---:|---|---|---|
| 5 | ADMIN | Administration | `172.30.32.1` |
| 10 | OPERATIONS | Operations | `172.30.32.65` |
| 15 | FINANCE | Finance | `172.30.32.129` |
| 20 | SERVERS | Internal servers | `172.30.32.193` |
| 25 | GUEST | Visitor network | `172.30.33.1` |
| 99 | MANAGEMENT | Network management | `172.30.33.129` |

The required VLANs are configured on the switches and carried across the appropriate trunks.

---

## 4. IP Addressing Plan

The allocated address block is:

```text
172.30.32.0/23
```

| VLAN | Network | Mask | Gateway | Usable Hosts |
|---:|---|---|---|---:|
| 5 | `172.30.32.0/26` | `255.255.255.192` | `172.30.32.1` | 62 |
| 10 | `172.30.32.64/26` | `255.255.255.192` | `172.30.32.65` | 62 |
| 15 | `172.30.32.128/26` | `255.255.255.192` | `172.30.32.129` | 62 |
| 20 | `172.30.32.192/27` | `255.255.255.224` | `172.30.32.193` | 30 |
| 25 | `172.30.33.0/25` | `255.255.255.128` | `172.30.33.1` | 126 |
| 99 | `172.30.33.128/27` | `255.255.255.224` | `172.30.33.129` | 30 |

The VLSM design provides appropriately sized networks while retaining capacity for the required growth.

---

## 5. Management Addressing

Management VLAN 99 uses the following addressing:

| Device | Management IP |
|---|---|
| KHT-R1 | `172.30.33.129` |
| KHT-SW1 | `172.30.33.130` |
| ADMIN-SW | `172.30.33.131` |
| OPERATIONS-SW | `172.30.33.132` |
| FINANCE-SW | `172.30.33.133` |
| SERVER-SW | `172.30.33.134` |
| GUEST-SW | `172.30.33.135` |
| MANAGEMENT-SW | `172.30.33.136` |

---

## 6. SSH Implementation

SSH is the assigned technical challenge and has been implemented on the router and switches.

The implementation includes:

- SSH version 2
- Local administrator authentication
- Privilege 15 administrator account
- RSA 2048-bit keys
- VTY lines configured for SSH
- Telnet disabled
- Saved startup configurations

Example configuration:

```text
username admin privilege 15 secret ADMIN123
ip domain-name kht.local
crypto key generate rsa
ip ssh version 2

line vty 0 15
 login local
 transport input ssh
```

SSH connectivity was successfully tested from KHT-SW1 to:

- ADMIN-SW
- OPERATIONS-SW
- FINANCE-SW
- SERVER-SW
- GUEST-SW
- MANAGEMENT-SW
- KHT-R1

Successful sessions reached privileged EXEC mode (`#`).

---

## 7. DHCP Implementation

DHCP is provided by KHT-R1.

Configured pools include:

- `ADMIN`
- `OPERATIONS`
- `FINANCE`
- `GUEST`
- `MANAGEMENT`

The implemented network has successfully assigned DHCP addresses to department and management devices.

Infrastructure and server devices that require predictable addresses use static addressing.

---

## 8. Guest Wi-Fi and Isolation

The visitor network uses:

- **VLAN:** 25
- **Network:** `172.30.33.0/25`
- **Gateway:** `172.30.33.1`
- **SSID:** `KHT-Guest-WiFi`

The `GUEST_ISOLATION` extended ACL prevents Guest traffic from accessing internal organisational networks.

The protected internal networks are:

- ADMIN — `172.30.32.0/26`
- OPERATIONS — `172.30.32.64/26`
- FINANCE — `172.30.32.128/26`
- SERVERS — `172.30.32.192/27`
- MANAGEMENT — `172.30.33.128/27`

The ACL also permits DNS access to the internal DNS server where required.

Guest-to-internal tests confirmed that access to protected internal networks is blocked.

---

## 9. DNS Implementation

Server 0 provides DNS service at:

```text
172.30.32.194
```

DNS records include:

| Record | Address |
|---|---|
| `google.com` | `8.8.8.8` |
| `www.google.com` | `8.8.8.8` |

The Guest DHCP pool provides Server 0 as the DNS server.

Guest DNS resolution was successfully tested using `nslookup google.com`.

---

## 10. Internet Connectivity and NAT/PAT

The WAN uses:

| Device | Interface | Address |
|---|---|---|
| KHT-R1 | G0/1 | `203.0.113.2/30` |
| ISP | G0/0 | `203.0.113.1/30` |
| Internet test server | — | `8.8.8.8` |

KHT-R1 uses the default route:

```text
0.0.0.0/0 via 203.0.113.1
```

NAT/PAT is configured on KHT-R1 for the internal `172.30.0.0/16` address space.

Internet testing confirmed:

- KHT-R1 → `8.8.8.8` — successful
- ADMIN → `8.8.8.8` — successful
- OPERATIONS → `8.8.8.8` — successful
- FINANCE → `8.8.8.8` — successful
- Guest Internet functionality was tested during implementation while maintaining Guest isolation and DNS access.

---

## 11. Printers and Department Devices

Department printers have been placed in their corresponding VLANs.

| Department | VLAN | Example IP |
|---|---:|---|
| ADMIN | 5 | `172.30.32.10` |
| OPERATIONS | 10 | `172.30.32.74` |
| FINANCE | 15 | `172.30.32.138` |
| GUEST | 25 | `172.30.33.10` |

The Guest printer was corrected from VLAN 10 to VLAN 25 so that it belongs to the Guest network.

---

## 12. Implementation Testing

The following implementation areas have been tested:

- Router interface status
- VLAN gateway reachability
- DHCP address allocation
- Inter-VLAN connectivity
- Server connectivity
- SSH connectivity to network switches
- SSH connectivity to KHT-R1
- SSH version 2
- VTY SSH configuration
- WAN connectivity
- NAT/PAT operation
- Guest-to-internal isolation
- Guest DNS resolution
- VLAN configuration
- Trunk configuration
- MAC address learning
- Guest switch configuration
- Server switch configuration
- Management switch configuration
- Department Internet connectivity

The ADMIN PC, for example, successfully reached `8.8.8.8` with 0% packet loss during final validation.

---

## 13. Firewall Note

A Cisco ASA 5505 firewall was temporarily evaluated as an optional real-world enhancement.

The ASA was **not retained in the active production path** because its Packet Tracer implementation interfered with the previously working WAN/Internet path. The original KHT-R1-to-ISP design was restored and successfully revalidated.

The firewall experiment is therefore not part of the final active network configuration.

---

## 14. Current Implementation Status

### Completed

- [x] VLAN segmentation
- [x] Router-on-a-stick
- [x] Inter-VLAN routing
- [x] DHCP
- [x] Management VLAN
- [x] SSH version 2
- [x] RSA 2048-bit authentication keys
- [x] Local administrator authentication
- [x] Guest VLAN
- [x] Guest isolation ACL
- [x] Guest DNS access
- [x] DNS service
- [x] NAT/PAT
- [x] WAN/Internet connectivity
- [x] Department Internet testing
- [x] Switch trunking
- [x] Department and guest printers

### Optional / Not part of final path

- [ ] ASA firewall inline deployment

---

## 15. Milestone 2 Evidence

Milestone 2 evidence should include authentic Packet Tracer screenshots showing:

1. Router interface status
2. VLAN gateway tests
3. End-device IP configuration
4. Inter-VLAN connectivity
5. Server connectivity
6. SSH sessions
7. SSH version 2
8. VTY configuration
9. WAN connectivity
10. NAT/PAT translations
11. Guest isolation
12. Guest DNS resolution
13. DHCP pools
14. VLAN configuration
15. Trunk configuration
16. MAC address table
17. Guest switch configuration
18. Server switch configuration
19. Management switch configuration
20. Internet connectivity

---

## 16. Project Structure

The GitHub repository is intended to contain:

```text
Networks-CMPG325/
├── README.md
├── Packet Tracer project files
├── Milestone documentation
└── Evidence/documentation
```

The Packet Tracer project file should represent the final working implementation described in this README.

---

## 17. Change Log

### Milestone 1
- Established KHT client requirements.
- Designed physical and logical topology.
- Created VLSM addressing plan.
- Planned VLAN segmentation.
- Planned SSH implementation.
- Planned Guest Wi-Fi isolation.

### Milestone 2
- Implemented VLANs and trunks.
- Implemented router-on-a-stick.
- Implemented DHCP.
- Implemented SSH version 2.
- Implemented Guest VLAN isolation.
- Implemented DNS.
- Implemented NAT/PAT and Internet connectivity.
- Added departmental and Guest printers.
- Completed connectivity and configuration testing.
- Evaluated an optional ASA firewall and restored the original working WAN design after testing.

---

## 18. Repository Purpose

This repository documents the development and implementation of the Kgalagadi Heritage Tours network for CMPG325.

It provides a record of the network design, implementation decisions, configuration requirements, testing evidence, and changes made throughout the project.
