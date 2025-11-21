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
- EdgeSwitch 8XP (IoT switch)
- TP-Link **BE550 (Wi-Fi 7) in AP mode**
- Pi-hole DNS filter
- Windows 10, Windows 11, and Ubuntu clients

---

## 🌐 Network Topology (YAML)

```yaml
network_topology:
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
    - "VLAN 1 – Home devices (PCs, Wi-Fi clients) on the 150W."
    - "VLAN 20 – Work laptop docked into the 150W."
    - "VLAN 30 – IoT devices hanging off the EdgeSwitch 8XP."

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
vlan_configuration:
  vlans:
    - id: 1
      name: "Home"
    - id: 20
      name: "Work"
    - id: 30
      name: "IoT"

  port_assignments:
    - port: 1
      purpose: "ER605 Router → Switch"
      vlan_mode: "untagged"
      vlan: 1
    - port: 2
      purpose: "Work Laptop (Dock)"
      vlan_mode: "untagged"
      vlan: 20
    - port: "3-4"
      purpose: "Free / Future Expansion"
      vlan_mode: "excluded"
    - port: 5
      purpose: "Switch Uplink"
      vlan_mode: "untagged"
      vlan: 1
    - port: 6
      purpose: "Personal PC"
      vlan_mode: "untagged"
      vlan: 1
    - port: "7-8"
      purpose: "BE550 AP or IoT Devices"
      vlan_mode: "vlan-dependent"

  notes:
    - "No tagged ports required — wireless did not support 802.1Q VLAN tags previously."
router_interfaces:
  vlan_1:
    name: "LAN (Home)"
    subnet: "192.168.1.0/24"
  vlan_20:
    name: "Work"
    subnet: "192.168.20.0/24"
  vlan_30:
    name: "IoT"
    subnet: "192.168.30.0/24"

firewall_rules:
  rules:
    - from: "Work"
      to: "Home"
      action: "blocked"
    - from: "IoT"
      to: "Home"
      action: "blocked"
    - from: "Home"
      to: "Work"
      action: "allowed_optional"
    - from: "IoT"
      to: "Internet"
      action: "allowed"
validation_testing:
  ip_assignment:
    work: "192.168.20.x"
    home: "192.168.1.x"
    iot: "192.168.30.x"

  segmentation_tests:
    - "Work VLAN cannot reach Home devices"
    - "IoT VLAN isolated from Home and Work"
    - "Home VLAN can reach Work when firewall allows"
    - "Pi-hole filtering operational when set as DNS"

  wifi_verification:
    be550_configuration: "Broadcasts only VLAN 1 (Home)"
issues_encountered:
  - issue: "Lost internet when changing DHCP DNS"
    fix: "Pi-hole unreachable until routing updated"
  - issue: "BE550 kept reverting to router mode"
    fix: "Factory reset and reconfigured in AP mode"
  - issue: "Old VLAN folder duplicated README content"
    fix: "Rebuilt using clean YAML structure"
summary:
  highlights:
    - "Practical VLAN segmentation"
    - "Switch configuration (access, untagged, excluded modes)"
    - "Router VLAN interface setup"
    - "Firewall rule design"
    - "Network isolation testing"
    - "Professional documentation practices"
  purpose: "Core component of my cybersecurity home lab portfolio."
