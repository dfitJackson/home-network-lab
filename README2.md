# Home Network Lab (Extended Notes)

This file expands on my home network setup, focusing on Home Assistant, Docker services, the tablet dashboard, and monitoring components.  
It follows the same format and tone as my main README but adds deeper technical notes.

---

## Home Assistant (Docker)

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

Config directory:
/config

---

## Home Assistant Dashboard (Tablet View)

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

---

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

Once HACS is added, full Omada Controller API integration will allow:

- Wi-Fi client counts  
- Connected devices  
- SSID load  
- AP performance  

---

## Next Improvements

- Complete HACS installation for advanced Omada integration  
- Add a network health bar to the tablet dashboard  
- Expand VLAN segmentation (LAN / IoT / Guest / Work)  
- Route Pi-hole DNS across all VLANs  
- Automate Home Assistant backups to Synology  
- Add a monitoring stack (Grafana, Loki, Prometheus, or Wazuh)  

---

## Notes

This file serves as a deeper technical companion to the main README.  
It documents what is actually running in my environment and the steps taken to expand the home lab.


