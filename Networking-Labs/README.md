[Home](../README.md)

# Networking Labs

> **Status: Updating**
>
> This section separates the network that is running now from projects that were previously implemented and validated.

## Current Network — July 2026

The active network uses:

- TP-Link BE550 as the current router, gateway, and primary Wi-Fi platform
- TP-Link RE705X in the upstairs office using Wi-Fi backhaul to the BE550
- Ethernet from the RE705X into the Ubiquiti switching environment
- Ubiquiti EdgeSwitch 8-Port 150W and EdgeSwitch 8XP, both operational through the upstairs office connection
- TP-Link RE605X in the wife's office using Wi-Fi backhaul to the BE550
- A flat LAN without active VLAN segmentation

The TP-Link ER605 is not currently in the active routing path. It was rolled back when the preferred wired configuration became impractical while the house was being prepared for sale.

The current inter-floor path is wireless. This keeps the upstairs lab operational without exposed cabling, but VLAN segmentation has been intentionally rolled back until a suitable concealed Ethernet path can be installed from the main floor to the upstairs office.

## Current Topology

```text
Internet
   │
   ▼
TP-Link BE550
Router / Gateway / Primary Wi-Fi
   │
   ├── Wi-Fi backhaul ──> RE605X ──> Wife's office
   │
   └── Wi-Fi backhaul ──> RE705X ──> Ethernet ──> EdgeSwitch 8-Port 150W
                                                     │
                                                     └── EdgeSwitch 8XP
```

## Current EdgeSwitch 150W VLAN State

The EdgeSwitch 150W still retains the historical VLAN definitions, but the active switch configuration has been simplified for the flat LAN.

- **VLAN 1 — default:** active untagged LAN for current client connectivity
- **VLAN 20 — Work VLAN:** retained as a definition but not active for the current work-laptop connection
- **VLAN 30 — IoT VLAN:** retained as a definition but not active in the current network
- **Port 2:** connected to the work laptop; configured as an untagged VLAN 1 client/access port
- **Port 2 trunk flag:** removed as part of the rollback cleanup because the work laptop no longer requires tagged VLAN traffic

This preserves the historical VLAN definitions without presenting the switch as actively segmented. Port 2 now matches the current flat-LAN design rather than the previous VLAN topology.

## Completed—Historical Project

The VLAN project documented in this folder was a real implementation using the ER605 and Ubiquiti EdgeSwitch hardware. Home, Work, and IoT network segments were configured and tested before the current topology was simplified.

The project remains part of this portfolio because it demonstrates:

- VLAN design and implementation
- Router and managed-switch configuration
- DHCP and DNS troubleshooting
- Isolation and reachability testing
- Documentation of failures, corrections, and lessons learned
- Adaptation when physical network constraints changed

It should not be interpreted as the network's current running configuration.

## Planned Network Work

The main infrastructure goal is to create a concealed Ethernet path from the main floor to the upstairs office. Once a reliable wired backbone is available, the ER605 and VLAN segmentation can be reconsidered and rebuilt as a new current implementation rather than simply restoring old assumptions.

## Documents

- [Networking Lab Overview](./networking-lab-overview.md)
- [VLAN Segmentation Lab](./vlan-segmentation-lab.md)
- [Historical Network Diagram](../images/Home%20Network.drawio.png)

## Status Definitions

| Status | Meaning |
|---|---|
| **Current** | Running in the present environment |
| **Completed—Historical** | Previously implemented and validated, then retired or replaced |
| **Updating** | Documentation is being reviewed for accuracy and consistency |

---

[Home](../README.md) · [Top](#networking-labs)