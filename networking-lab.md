
# 📡 Networking & VLAN Segmentation Lab

This lab documents how I segmented my home network into **Home**, **Work**, and **IoT** networks using VLANs, a TP-Link ER605 router, an EdgeSwitch 150W core switch, an EdgeSwitch 8XP for IoT, and a BE550 access point.

---

## 🎯 Objectives

- Create VLANs for:
  - **VLAN 1 – Home**
  - **VLAN 20 – Work**
  - **VLAN 30 – IoT**
- Map switch ports on the **EdgeSwitch 150W** to the correct VLANs.
- Use the **ER605** for inter-VLAN routing and firewall rules.
- Use the **BE550** in AP mode for Home Wi-Fi only.
- Hang IoT devices off the **EdgeSwitch 8XP** on VLAN 30.
- Validate segmentation using Windows and Ubuntu clients.

---

## 🛠 Hardware Used

- TP-Link **ER605** – router / gateway
- Ubiquiti **EdgeSwitch 8-Port 150W** – core switch
- Ubiquiti **EdgeSwitch 8XP** – IoT / PoE switch
- TP-Link **BE550** (Wi-Fi 7) – access point in AP mode
- **Pi-hole** – DNS filtering during testing
- Windows 10, Windows 11, and Ubuntu devices

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
```

---

## 🔧 VLAN Configuration (EdgeSwitch 150W)

### VLAN IDs

- **VLAN 1** → Home  
- **VLAN 20** → Work  
- **VLAN 30** → IoT  

### Port Assignments

| Port | Purpose                                | VLAN Mode       |
|------|----------------------------------------|-----------------|
| 1    | ER605 Router → Switch                  | Untagged VLAN 1 |
| 2    | Work Laptop (Dock)                     | Untagged VLAN 20|
| 3–4  | Free / Future Expansion                | Excluded        |
| 5    | Uplink to EdgeSwitch 8XP (IoT switch)  | Untagged VLAN 1 |
| 6    | Personal PC                            | Untagged VLAN 1 |
| 7–8  | BE550 AP or future devices             | Depends on use  |

> No tagged ports required at this stage because wireless gear wasn’t yet carrying multiple VLANs via 802.1Q tags.

---

## 🌐 Router (ER605) – VLAN Interfaces & Firewall

**Subnets**

- **VLAN 1 – Home:** `192.168.1.0/24`
- **VLAN 20 – Work:** `192.168.20.0/24`
- **VLAN 30 – IoT:** `192.168.30.0/24`

**Firewall behaviour (high-level)**

- Work → Home: **Blocked**
- IoT → Home: **Blocked**
- IoT → Work: **Blocked**
- Home → Work: **Optionally allowed** (for management)
- All VLANs → Internet: **Allowed**

---

## 🧪 Validation & Testing

**IP Assignment**

- Work device received an address in `192.168.20.x`.
- Home device received an address in `192.168.1.x`.
- IoT devices received addresses in `192.168.30.x`.

**Segmentation Tests**

- Work VLAN **could not** reach Home devices.
- IoT VLAN was isolated from both Home and Work.
- Home VLAN could reach Work when the firewall rule was enabled.
- When clients used Pi-hole as DNS, ad / tracking domains were blocked as expected.

**Wi-Fi Verification**

- BE550 configured in **AP mode** only.
- BE550 SSID mapped to **VLAN 1 (Home)**.

---

## ⚠️ Issues Encountered & Fixes

- Changing DHCP DNS to Pi-hole too early caused **temporary loss of internet**  
  → Fixed by restoring router DNS, then re-introducing Pi-hole carefully.

- BE550 kept wanting to act as a **router**, not an AP  
  → Fixed with a full factory reset and configuring **AP mode only** before connecting to the 150W.

- Old VLAN documentation was scattered and partially duplicated  
  → Replaced with this single, clean lab write-up.

---

## 📄 Summary

This lab demonstrates:

- Practical VLAN segmentation in a home environment  
- Port-based VLAN configuration on the **EdgeSwitch 150W**  
- Using the **ER605** for VLAN interfaces and basic firewalling  
- Isolating **IoT** devices on a separate switch and VLAN  
- Documenting a home lab in a way that’s readable for recruiters and technical reviewers

[⬅ Back to Home](README.md)

