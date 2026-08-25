# CMPG325-2026-057 — Kgalagadi Heritage Tours

## Milestone 1: Client Design Review

**Project ID:** CMPG325-2026-057  
**Client ID:** CLI-057  
**Organisation:** Kgalagadi Heritage Tours  
**Technical Challenge:** SSH  
**Addressing Block:** `172.30.32.0/23`  
**Growth Requirement:** 40% expected user-group growth within 3 years  
**Change Request:** Guest Wi-Fi will be provided for visitors and isolated from internal resources.

---

## 1. Project Overview

This project will design and implement a secure, scalable network for Kgalagadi Heritage Tours using Cisco Packet Tracer.

The proposed network will use VLAN segmentation, router-on-a-stick inter-VLAN routing, DHCP, SSH-based device administration, departmental wireless access, and a dedicated guest wireless network.

The design will be planned to support the organisation's current requirements while providing sufficient capacity for the expected 40% growth in user groups over the next three years.

---

## 2. Client Requirements

The network design will address the following requirements:

- Provide reliable connectivity for the organisation's departments.
- Separate departments using VLANs.
- Provide wireless connectivity for Admin, Operations, Finance, and Guest users.
- Provide a dedicated Guest Wi-Fi network for visitors.
- Isolate Guest Wi-Fi users from internal organisational resources.
- Provide secure remote network-device administration using SSH.
- Use the allocated `172.30.32.0/23` address block efficiently.
- Allow for approximately 40% user-group growth over three years.
- Provide a design that can be expanded as the organisation grows.

---

## 3. Proposed Physical Topology

The physical topology will use:

- **1 × Cisco 2911 router** (`KHT-R1`)
- **1 × Cisco 2960 switch** (`KHT-SW1`)
- **Access Point-PT devices** for departmental and guest wireless access
- End devices such as PCs, laptops, printers, servers, and visitor devices

The main router-to-switch connection will use:

```text
KHT-R1 G0/0
      |
      | 802.1Q trunk
      |
KHT-SW1 Fa0/24
```

The departmental and guest access points will connect to switch access ports assigned to their corresponding VLANs.

---

## 4. Proposed Logical Topology

The network will be divided into the following VLANs:

| VLAN | Name | Purpose |
|---:|---|---|
| 5 | ADMIN | Administration users and services |
| 10 | OPERATIONS | Tour and operational staff |
| 15 | FINANCE | Finance and accounting users |
| 20 | SERVERS | Internal server resources |
| 25 | GUEST | Visitor wireless access |
| 99 | MANAGEMENT | Network-device management |

Inter-VLAN routing will be performed by the Cisco 2911 router using router-on-a-stick subinterfaces.

The Guest VLAN will be protected with an extended ACL that will deny access to internal organisational networks while allowing permitted external access.

---

## 5. IP Addressing Plan

The project will use the allocated addressing block:

```text
172.30.32.0/23
```

The proposed subnet allocation will be:

| VLAN | Purpose | Network | Subnet Mask | Gateway | Usable Hosts |
|---:|---|---|---|---|---:|
| 5 | ADMIN | `172.30.32.0/26` | `255.255.255.192` | `172.30.32.1` | 62 |
| 10 | OPERATIONS | `172.30.32.64/26` | `255.255.255.192` | `172.30.32.65` | 62 |
| 15 | FINANCE | `172.30.32.128/26` | `255.255.255.192` | `172.30.32.129` | 62 |
| 20 | SERVERS | `172.30.32.192/27` | `255.255.255.224` | `172.30.32.193` | 30 |
| 25 | GUEST | `172.30.33.0/25` | `255.255.255.128` | `172.30.33.1` | 126 |
| 99 | MANAGEMENT | `172.30.33.128/27` | `255.255.255.224` | `172.30.33.129` | 30 |

The subnet sizes will provide capacity for the projected growth requirement while retaining address space for future expansion.

---

## 6. Wireless Design

Wireless access will be provided using separate Access Point-PT devices assigned to the appropriate departmental VLANs.

| Wireless Network | VLAN | Purpose |
|---|---:|---|
| `KHT-Admin-WiFi` | 5 | Admin staff |
| `KHT-Operations-WiFi` | 10 | Operations staff |
| `KHT-Finance-WiFi` | 15 | Finance staff |
| `KHT-Guest-WiFi` | 25 | Visitors |

The wireless networks will use WPA2-PSK security.

Guest wireless traffic will use VLAN 25 and will be subject to the Guest isolation ACL.

---

## 7. SSH Security Requirement

SSH will be implemented as the primary remote management method for the router and switch.

The planned SSH configuration will include:

- SSH version 2
- Local username authentication
- RSA 2048-bit keys
- VTY lines configured to accept SSH
- Telnet access disabled

The network administrator will be able to securely connect to the network devices using SSH.

---

## 8. Guest Wi-Fi Isolation

The Guest network will be isolated from internal organisational resources.

The Guest VLAN will not be permitted to access:

- ADMIN network (`172.30.32.0/26`)
- OPERATIONS network (`172.30.32.64/26`)
- FINANCE network (`172.30.32.128/26`)
- SERVERS network (`172.30.32.192/27`)
- MANAGEMENT network (`172.30.33.128/27`)

The isolation policy will be implemented using an extended ACL on the Guest VLAN interface.

---

## 9. DHCP Plan

DHCP will be provided by the Cisco 2911 router.

The planned DHCP pools will include:

- `ADMIN`
- `OPERATIONS`
- `FINANCE`
- `GUEST`

Servers and other infrastructure devices that require predictable addresses will be assigned static addresses where appropriate.

---

## 10. Milestone 1 Deliverables

The Client Design Review will include:

1. **Client Requirements**
2. **Physical Topology**
3. **Logical Topology**
4. **IP Addressing Plan**
5. **Initial GitHub Repository**

The Packet Tracer topology and supporting documentation will be maintained as the project progresses.

---

## 11. Proposed Validation

The design will be tested during later milestones to confirm that:

- VLANs will operate correctly.
- DHCP will allocate addresses to user devices.
- Inter-VLAN routing will function correctly.
- SSH access will work on the router and switch.
- Departmental wireless clients will receive addresses from the correct VLAN.
- Guest users will be isolated from internal resources.
- The addressing design will support the expected 40% growth.

---
