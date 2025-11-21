---
title: "Networking & VLAN Segmentation Lab"
description: "Segmenting Home, Work, and IoT networks using a TP-Link ER605 router, EdgeSwitch 150W, and BE550 AP."
---

# 📡 Networking & VLAN Segmentation Lab
Segmenting Home, Work, and IoT traffic across my home network.

---

## 🎯 Objectives
- Create VLANs for Home (1), Work (20), IoT (30)
- Segment traffic between Home, Work, and IoT
- Configure untagged / access ports on EdgeSwitch 150W
- Route VLANs using TP-Link ER605
- Use BE550 AP in Access Point mode
- Validate segmentation using Windows and Ubuntu devices

---

## 🛠️ Hardware Used
- TP-Link **ER605** (Router/Gateway)
- Ubiquiti **EdgeSwitch 8-Port 150W**
- TP-Link **BE550 (Wi-Fi 7) in AP mode**
- Pi-hole DNS filter
- Windows 10, Windows 11, and Ubuntu clients

---

## 🌐 Network Topology

  diagram: |
    ISP Modem
        ↓
    ER605 Router (Gateway)
        ↓
    EdgeSwitch 150W (Core Switch)
        ├── BE550 AP (VLAN 1 – Home Wi-Fi)
        ├── Personal PC (VLAN 1 – Home LAN)
        ├── Work Laptop Dock (VLAN 20 – Work)
        └── EdgeSwitch 8XP (VLAN 30 – IoT Uplink)
                ├── Smart Plugs (VLAN 30)
                ├── Cameras (VLAN 30)
                └── Other IoT Devices (VLAN 30)

  description:
    router: "TP-Link ER605 handles routing, DHCP, and VLAN interfaces."
    core_switch: "EdgeSwitch 150W is the main wired switch, with ports mapped to Home, Work, and IoT paths."
    iot_switch: "EdgeSwitch 8XP is fed from the 150W and carries VLAN 30 for PoE-based IoT devices."
    wireless: "BE550 runs in AP mode on VLAN 1 to provide Home Wi-Fi."
    segmentation:
      - "VLAN 1 – Home devices (PCs, Wi-Fi clients) on 150W."
      - "VLAN 20 – Work laptop docked into 150W."
      - "VLAN 30 – IoT devices hanging off EdgeSwitch 8XP."
    edgeswitch_150w_ports:
      - port: 1
        connection: "Uplink to ER605"
        vlan: 1
      - port: 2
        connection: "Work laptop dock"
        vlan: 20
      - port: 3
        connection: "BE550 AP (Home Wi-Fi)"
        vlan: 1
      - port: 5
        connection: "Uplink to EdgeSwitch 8XP (IoT)"
        vlan: 1
      - port: 6
        connection: "Personal PC"
        vlan: 1
    edgeswitch_8xp:
      uplink_from: "EdgeSwitch 150W (port 5)"
      vlan: 30
      devices:
        - "Smart plugs"
        - "Cameras"
        - "Other IoT / smart devices"





---

## 🔧 VLAN Configuration (EdgeSwitch 150W)
### VLAN IDs
- **VLAN 1** → Home  
- **VLAN 20** → Work  
- **VLAN 30** → IoT  

### Port Assignments
| Port | Purpose                                | VLAN Mode            |
|------|-----------------------------------------|-----------------------|
| 1    | ER605 Router → Switch                   | Untagged VLAN 1       |
| 2    | Work Laptop (Dock)                      | Untagged VLAN 20      |
| 3–4  | Free / Future Expansion                 | Excluded              |
| 5    | Switch Uplink                           | Untagged VLAN 1       |
| 6    | Personal PC                             | Untagged VLAN 1       |
| 7–8  | BE550 AP or IoT Devices                 | VLAN dependent        |

No tagged ports required — wireless did not support 802.1Q VLAN tags previously.

---

## 🌐 Router (ER605) VLAN Interfaces
- **LAN (VLAN 1)**: 192.168.1.0/24
- **Work (VLAN 20)**: 192.168.20.0/24
- **IoT (VLAN 30)**: 192.168.30.0/24

### Firewall Rules
- Work → Home = Blocked  
- IoT → Home = Blocked  
- Home → Work = Optional (Allowed for management)  
- IoT → Internet = Allowed  

---

## 🧪 Validation & Testing
### Correct IP Assignment
- Work device: `192.168.20.x`
- Home device: `192.168.1.x`
- IoT device: `192.168.30.x`

### Segmentation Tests
- Work VLAN could *not* reach Home devices
- IoT VLAN isolated from Home and Work
- Home VLAN could reach Work when allowed via firewall
- Pi-hole filtering functional when set as DNS

### Wi-Fi Verification
BE550 was configured to broadcast **only VLAN 1 (Home)**.

---

## ⚠️ Issues Encountered & Fixes
- **Lost internet when changing DHCP DNS** — Pi-hole not reachable until routing was updated  
- **BE550 kept reverting to router mode** — resolved with full factory reset → AP Mode  
- **Old VLAN folder duplicated README content** — replaced with this clean YAML version  

---

## 📄 Summary
This lab demonstrates:
- Practical VLAN segmentation  
- Switch configuration (access/untagged/excluded)  
- Router VLAN interface setup  
- Firewall rule design  
- Network isolation testing  
- Enterprise-style home lab documentation  

A key component of my cybersecurity home lab and portfolio.
