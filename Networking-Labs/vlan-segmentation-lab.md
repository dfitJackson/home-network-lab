<a id="top"></a>

[⬅ Back to Overview](networking-lab-overview.md) • [⬆ Home](../README.md)

 # 🔧 VLAN Segmentation Lab  
Hands-on VLAN segmentation across Home, Work, and IoT networks using the TP-Link ER605, EdgeSwitch 150W, and EdgeSwitch 8XP.

---

## 🎯 Objectives
- Create three isolated VLANs: **Home (1)**, **Work (20)**, and **IoT (30)**
- Enforce segmentation across router + switches
- Configure VLAN access ports (untagged) for each device type
- Build an IoT-only subnet behind the EdgeSwitch 8XP
- Validate segmentation using multiple client devices
- Document configuration and issues for portfolio use

---

## 🛠 Hardware Used
- **TP-Link ER605** (Router + VLAN Gateway)
- **Ubiquiti EdgeSwitch 150W** (Core switch)
- **Ubiquiti EdgeSwitch 8XP** (IoT / PoE switch)
- **TP-Link BE550 (Wi-Fi 7)** in AP mode (VLAN 1)
- **Pi-hole** DNS filtering (running on Ubuntu box)
- Windows / Ubuntu endpoints

---

## 🌐 VLAN Design

| VLAN | Purpose | Subnet | Notes |
|------|---------|--------|-------|
| **1** | Home LAN | `192.168.1.0/24` | Personal PC, BE550 Wi-Fi |
| **20** | Work | `192.168.20.0/24` | Laptop dock connected to 150W |
| **30** | IoT | `192.168.30.0/24` | Smart plugs, cameras, IoT sensors |

---

## 🗺 Network Topology
```text
ISP Modem
   ↓
ER605 Router (Gateway)
   ├── BE550 AP (VLAN 1 – Home Wi-Fi)
   ↓
EdgeSwitch 150W (Core Switch)
   ├── Personal PC (VLAN 1)
   ├── Work Dock (VLAN 20)
   └── EdgeSwitch 8XP (VLAN 30 Uplink)
           ├── Smart Plugs (VLAN 30)
           ├── Cameras (VLAN 30)
           └── IoT Sensors (VLAN 30)

```
## 🧩 EdgeSwitch 150W — VLAN Port Mapping

| Port | Connected Device          | VLAN Mode    | VLAN ID |
|------|----------------------------|--------------|---------|
| 1    | ER605 Router → Switch     | Untagged     | 1       |
| 2    | Work Laptop Dock          | Untagged     | 20      |
| 3    | Open / future             | Excluded     | —       |
| 4    | Open / future             | Excluded     | —       |
| 5    | Uplink to EdgeSwitch 8XP  | Untagged     | 1       |
| 6    | Personal PC               | Untagged     | 1       |
| 7–8  | Optional / AP / IoT       | VLAN dependent | —     |

> **Note:** No tagged ports were required because the BE550 AP didn’t support 802.1Q VLAN tagging.

---

## 🧩 ER605 Router — VLAN Interface Setup

| Interface     | Purpose       | Subnet            | DHCP     |
|---------------|---------------|--------------------|----------|
| LAN (VLAN 1)  | Home devices  | 192.168.1.0/24     | Enabled  |
| VLAN 20       | Work devices  | 192.168.20.0/24    | Enabled  |
| VLAN 30       | IoT devices   | 192.168.30.0/24    | Enabled  |

### 🔥 Firewall Rules
- **Work → Home** = ❌ Blocked  
- **IoT → Home** = ❌ Blocked  
- **Home → Work** = ⚠️ Allowed (optional)  
- **IoT → Internet** = ✅ Allowed  
- **Home → Internet** = ✅ Allowed  

---

## 🧪 Validation & Testing

### ✔ IP Assignments
- Home devices received: **192.168.1.x**
- Work devices received: **192.168.20.x**
- IoT devices received: **192.168.30.x**

### ✔ Segmentation Results
- Work → Home = **Blocked**
- IoT → Home & Work = **Blocked**
- Home → Work = **Allowed** (optional)
- IoT → Internet = **Works**
- Pi-hole = **Filtering correctly once routing fixed**

---

## ⚠️ Issues Encountered & Fixes

### **1. Lost Internet After Changing DHCP DNS**
- **Issue:** Pi-hole DNS caused loss of internet  
- **Fix:** Updated ER605 DHCP + ensured Pi-hole reachable  
- **Result:** Stable internet + Pi-hole filtering  

---

### **2. BE550 Kept Reverting to Router Mode**
- **Cause:** Auto-detection + leftover mesh config  
- **Fix:** Full factory reset → fresh configuration → AP Mode only  
- **Result:** BE550 stable as Wi-Fi 7 AP on VLAN 1  

---

### **3. Incorrect VLANs on EdgeSwitch 150W**
- **Cause:** UI terminology differences (Excluded vs Untagged)  
- **Fix:** Rebuilt VLAN table using “Untagged only” method  
- **Result:** Perfect segmentation across VLAN 1 / 20 / 30  

---

### **4. IoT Devices Not Receiving DHCP**
- **Cause:** ER605 VLAN30 interface added *after* switch uplink  
- **Fix:** Rebooted 8XP + refreshed DHCP leases  
- **Result:** IoT devices now get `192.168.30.x`  

---

### **5. ER605 Controller Sync Issues**
- **Issue:** Router failed to adopt configuration from controller  
- **Fix:** Multiple factory resets → removed old config → re-adopted  
- **Result:** Router + controller finally synced reliably  

---

### **6. Old VLAN Documentation Was Conflicting**
- **Fix:** Replaced with this clean, organized VLAN lab  
- **Result:** Professional, portfolio-quality documentation  

---

## 📄 Summary

This VLAN lab demonstrates:

- Real-world VLAN architecture  
- Segmentation using enterprise-style hardware  
- DHCP isolation per subnet  
- Correct switch port configuration  
- Firewall rule enforcement  
- IoT isolation using EdgeSwitch 8XP  
- Professional documentation & troubleshooting  

A fully segmented, production-style VLAN setup — built inside a home lab.

---

---

---

### 🧭 Navigation
⬅️ [Back to Networking Labs](../Networking-Labs/README.md)  
🏠 [Home](../README.md)  
➡️ [Next: VLAN Segmentation Lab](./vlan-segmentation-lab.md)
