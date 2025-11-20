home-network-lab

Documenting my home network learning journey.

🧠 Home Network Lab

This repo is where I track what I’ve learned (and what I’ve broken, then fixed) while building out my home network.
Everything here is tied to real hardware and real configurations running in my house.

🧩 Projects So Far
1) Pi-hole (Ad-blocking DNS)

Running Pi-hole in Docker on my HP All-in-One (Ubuntu) machine.
It handles DNS for my home network and lets me see which domains devices hit the most.

Static IP: 192.168.10.148

Deployed through Docker

Integrated into Home Assistant

Logs DNS queries, ads blocked, unique clients, and status

Verified query path using dig and ER605 DHCP settings

Pi-hole currently covers my main LAN VLAN. Extending to other VLANs is planned.

2) VLAN Segmentation

Goal: separate personal, work, and IoT traffic.

Router: Omada ER605

Switch: EdgeSwitch 150W

Wi-Fi: TP-Link BE550

Controller: Omada Software Controller

The ER605 handles VLAN creation, and the EdgeSwitch enforces port-level tagging/untagging.

🗺 Network Map

Visual overview of my physical + logical network layout:

🔧 Verified VLAN Configuration (EdgeSwitch 8-Port 150W)
VLAN ID	Name	Port 1	Port 2	Port 3	Port 4	Port 5	Port 6	Port 7	Port 8	Notes
1	default	U	E	U	U	T	E	E	E	Home VLAN
20	work	T	U	E	E	T	E	E	E	Work laptop VLAN
30	IoT	T	E	E	E	T	E	E	E	IoT VLAN

U: Untagged T: Tagged E: Excluded

Port summary:

Port 1 → uplink to router

Port 2 → work laptop VLAN 20

Ports 3–4 → home devices

Port 5 → trunk between switches

🧰 Current Stack
Routers / Wi-Fi

TP-Link ER605

TP-Link BE550 (Wi-Fi 7)

TP-Link RE605X

Switching

EdgeSwitch 150W

EdgeSwitch 8XP

Servers / Controllers

HP All-in-One (Ubuntu) → Docker host

Omada Software Controller (Windows PC)

Synology NAS for monitoring + storage

Docker Containers
Container	Purpose
homeassistant	Home automation platform
pihole	DNS filtering + metrics
matter-server	Matter protocol integration
🏠 Home Assistant

Home Assistant runs in Docker and integrates:

Lights, switches & media

Ecobee thermostat

Pi-hole stats (ads blocked, DNS queries, system status)

Synology NAS monitoring (CPU, RAM, volume usage, temps, throughput)

Omada network device sensors (switch ports, WAN status)

Custom Lovelace dashboard optimized for a wall-mounted tablet

This setup lets me consolidate network health, automation, and device stats into one UI.

📱 Samsung Tab A Dashboard (SM-T580)

A dedicated Home Assistant control panel mounted/running in Fully Kiosk Browser.

Dashboard sections:

Lights

Climate

Media

Pi-hole overview

NAS health metrics

Omada access button

Network status bar (internet up/down, Pi-hole active)

Dashboard is landscape-optimized, touch-friendly, and designed for quick access.

🧭 Next Steps

Add Omada Controller API + Wi-Fi client monitoring

Integrate VLANs fully (LAN, Work, IoT, Guest)

Add Home Assistant backup automation to Synology

Document Home Assistant dashboard YAML

Add monitoring/logging stack (Grafana, Loki, or Wazuh)

Begin documenting automation in Node-RED

🗒 Notes

This repo is a living lab journal — configs evolve as the network grows.
Everything here reflects what’s actually running in my home environment.
