[Home](../README.md)

# Networking Labs

> **Status: Updating**
>
> This section separates the network that is running now from projects that were previously implemented and validated.

## Current Network — July 2026

The active network uses:

- TP-Link ER605 gateway
- TP-Link BE550 as the primary Wi-Fi platform
- TP-Link RE705X providing wireless backhaul to the upstairs office
- Ethernet handoff from the RE705X to an EdgeSwitch
- A flat LAN without active VLAN segmentation

The previous physical Ethernet trunk to the office was removed. The wireless bridge restored connectivity but does not currently carry the tagged VLAN design used in the earlier segmented network.

## Completed—Historical Project

The VLAN project documented in this folder was a real implementation using the ER605 and Ubiquiti EdgeSwitch hardware. Home, Work, and IoT network segments were configured and tested before the wired backhaul was removed.

The project remains part of this portfolio because it demonstrates:

- VLAN design and implementation
- Router and managed-switch configuration
- DHCP and DNS troubleshooting
- Isolation and reachability testing
- Documentation of failures, corrections, and lessons learned
- Adaptation when physical network constraints changed

It should not be interpreted as the network’s current running configuration.

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
