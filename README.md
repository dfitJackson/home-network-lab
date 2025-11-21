![Jackson's Security Lab]( "images/Jackson’s Home Lab.png" )

# 🧠 Home Network Lab

### ⭐ About This Repository
This repository showcases my hands-on cybersecurity work, including network engineering, penetration testing labs, GRC documentation, Docker deployments, Pi-hole DNS filtering, and Omada-based network segmentation. It demonstrates both technical and governance-focused security skills through real home lab projects.

This is also where I’m documenting what I’ve learned (and broken, then fixed) while building out my home network.  
Each project is hands-on and tied to something I actually run in my house.

---

## 📘 Table of Contents
- [Home Network Diagram](#home-network-map)
- [VLAN Segmentation Lab](VLAN)
- [Pi-hole Deployment](network-projects/pihole.md)
- [Docker Home Assistant Lab](home-assistant-docker.md)
- [Penetration Testing Lab](penetration-testing-labs/first-exploit.md)
- [GRC Projects](grc-projects)

---

## 🧠 Skills Demonstrated
- Network Security (VLANs, trunking, segmentation)
- Home Lab Architecture & Documentation
- Penetration Testing (Nmap, Metasploit, Kali Linux)
- Logging & Monitoring (Pi-hole, router logs)
- Docker & Containerization
- Governance, Risk & Compliance (ISO 27001, SOC 2, risk registers)
- Threat Analysis & Reporting




## 🧩 Projects So Far

### 1) Pi-hole (Ad-blocking DNS)
Running Pi-hole in Docker on my **HP All-in-One (Ubuntu)** box.  
It’s handling DNS for my personal network and letting me see which domains my devices talk to most.  
Testing with `dig` helped me confirm query flow through the **ER605**.  
Pi-hole has a static IP `192.168.10.148` and is currently limited to my main VLAN.

### 2) VLAN Segmentation
Goal: separate **personal**, **work**, and **IoT** traffic across my wired network.  
The **Omada ER605** handles routing and VLAN assignment, while my **EdgeSwitch 8-Port 150W** enforces port-level segmentation.  
The **TP-Link BE550** handles Wi-Fi and acts as a router within the Omada system.

### 3) Home Assistant (Docker)

Home Assistant is running in Docker on my HP All-in-One Ubuntu machine.  
It handles automation, integrations, dashboards, and home monitoring.

### Current Integrations
- Ecobee (climate)
- Chromecast (media control)
- Pi-hole (DNS metrics)
- Synology DSM (NAS monitoring)
- Omada device sensors (router + switch status)
- Matter Server (IoT onboarding)

### Why Docker for HA?
- Easy to update versions  
- Container separation for HA, Pi-hole, and Matter  
- No Home Assistant OS required  
- Better control of storage paths and configs  


### 4) Home Assistant Dashboard (Tablet View)

A custom Lovelace dashboard is built specifically for a **Samsung Galaxy Tab A (SM-T580)** mounted as a control console.

### Sections on the Dashboard
- Lights  
- Climate  
- Media  
- Pi-hole monitoring  
- Synology NAS stats  
- Omada quick-launch  
- Network health tiles  

Most cards were built using the UI editor (minimal YAML).  
The dashboard is designed for landscape mode and touch-friendly tiles.


## Tablet Setup (Fully Kiosk Browser)

The tablet runs **Fully Kiosk Browser** to stay in permanent fullscreen.

Key settings:
- Fullscreen mode enabled  
- Navigation bars hidden  
- Screen stays awake  
- Auto-launch URL:  http://192.168.10.148:8123/lovelace/0

- 
This gives me a clean control panel that doesn’t require interaction to unlock or navigate.

---

## Docker Stack (Summary)

These are the containers running on the HP All-in-One:

| Container | Purpose |
|----------|---------|
| homeassistant | Core automation and integrations |
| pihole | DNS filtering, network stats |
| matter-server | Matter/Thread onboarding for IoT |

All containers are managed directly via CLI:


---

## Synology NAS Monitoring

Synology DSM is integrated with Home Assistant and provides:

- CPU usage  
- RAM usage  
- Volume capacity  
- Drive health  
- Drive temperature  
- Upload/download throughput  
- DSM update availability  

This data appears in the tablet dashboard.

---

## Omada Network Monitoring

The Omada Controller (running on Windows PC) provides:

- Switch port status  
- Router WAN online/offline state  
- Internet connectivity sensor  
- Device status (APs, switches, router)
### 🗺 Network Map
Here’s the visual layout of my current setup.

[![Home Network Map](images/Home%20Network.drawio.png)](images/Home%20Network.drawio)


---

## 🔧 Verified VLAN Configuration (EdgeSwitch 8-Port 150W)

| VLAN ID | VLAN Name  | Port 1 | Port 2 | Port 3 | Port 4 | Port 5 | Port 6 | Port 7 | Port 8 | Notes |
|----------|-------------|--------|--------|--------|--------|--------|--------|--------|--------|-------|
| **1** | default | U | E | U | U | T | E | E | E | Home VLAN (untagged on 1,3,4; trunk on 5) |
| **20** | Work VLAN | T | U | E | E | T | E | E | E | Work VLAN (untagged on 2; trunk on 5) |
| **30** | IoT VLAN | T | E | E | E | T | E | E | E | IoT VLAN (tagged on 1,5) |

**Legend:**  
- **U = Untagged**  
- **T = Tagged**  
- **E = Excluded**

**Summary**
- Port 1: Uplink to router (untagged VLAN 1, tagged VLAN 20 & 30)  
- Port 2: Work laptop (untagged VLAN 20)  
- Port 3–4: Personal/home (untagged VLAN 1)  
- Port 5: Trunk between switches (tagged VLAN 20 & 30, untagged VLAN 1)  
- Ports 6–8: Not used yet

---

## 🧰 Current Stack
- **Routers:** Omada ER605 and TP-Link BE550 (Wi-Fi 7)  
- **Switches:** EdgeSwitch 8-Port 150W and EdgeSwitch 8XP  
- **Controller:** Omada Software Controller  
- **DNS:** Pi-hole (Docker on Ubuntu) – `192.168.10.148`  
- **Workstation:** Windows Surface (admin access)  
- **Work Laptop:** Connected via Lenovo USB-C Dock (40AY0090US) on VLAN 20  

---

## 🧭 Next Steps
- Add Omada APs that support VLAN tagging.  
- Extend Pi-hole DNS coverage across all VLANs.  
- Begin documenting automation and monitoring using Node-RED.  

---

## 🗒 Notes
This repo is more like a lab journal than a polished guide.  
Configs are live and evolve as I learn — everything here reflects what’s actually running in my setup.

