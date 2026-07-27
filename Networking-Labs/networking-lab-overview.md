<a id="top"></a>

[Home](../README.md) · [Networking Labs](./README.md) · [VLAN Segmentation Lab](./vlan-segmentation-lab.md)

# Networking and VLAN Segmentation Overview

> **Project status: Completed—Historical**
>
> The VLAN architecture documented here was implemented and validated previously. It is not the network's active configuration as of July 2026.

## Purpose

This project explored how to separate home, work, and IoT traffic using VLANs, managed switching, routing rules, and structured validation. It was built in a live home-lab environment using a TP-Link ER605 and Ubiquiti EdgeSwitch hardware.

The implementation is retained as a case study because the design, troubleshooting, and validation work remains relevant even though the physical network later changed.

## Historical Lab Objectives

- Create distinct Home, Work, and IoT network segments
- Configure VLAN interfaces on the ER605
- Configure managed-switch access and trunk behaviour
- Assign devices to the intended network
- Test DHCP, DNS, internet access, and cross-network reachability
- Record configuration problems and corrective actions
- Maintain an isolated, practical home environment while learning enterprise networking concepts

## Hardware Used During the Historical Project

- TP-Link ER605 gateway
- Ubiquiti EdgeSwitch 8-Port 150W
- Ubiquiti EdgeSwitch 8XP
- TP-Link BE550 Wi-Fi platform
- Pi-hole DNS filtering
- Windows and Ubuntu test systems

## Historical Architecture

During the segmented implementation, a physical Ethernet path connected the ER605 and managed switching infrastructure. This wired connection supported the network path used by the VLAN design.

The logical networks were:

| VLAN | Purpose | Historical role |
|---|---|---|
| **1** | Home | Personal systems and primary home connectivity |
| **20** | Work | Dedicated work-device segment |
| **30** | IoT | Smart-home and IoT isolation |

The detailed port configuration changed during troubleshooting. This portfolio focuses on the verified design, testing process, and lessons learned rather than presenting an old port table as a reusable production configuration.

## Validation Performed

The project included checks for:

- Correct DHCP assignment on each configured segment
- Internet access from permitted networks
- Isolation between Work, Home, and IoT segments
- DNS reachability through Pi-hole where configured
- Switch-port membership and uplink behaviour
- Recovery after incorrect VLAN or DHCP changes

Validation was performed with Windows and Ubuntu clients using IP configuration checks, ping/reachability testing, DNS queries, and direct review of router and switch settings.

## Problems Investigated

### DNS changes interrupted connectivity

Changing DHCP-provided DNS settings caused loss of name resolution when Pi-hole was not reachable from the affected network.

**Response:** Reviewed DHCP options, verified Pi-hole reachability, and retested DNS query flow before expanding coverage.

### VLAN membership caused incorrect access

Differences between tagged, untagged, and excluded terminology led to incorrect switch-port membership during configuration.

**Response:** Rebuilt the relevant assignments, verified endpoint addressing, and repeated isolation tests.

### IoT clients failed to receive addresses

IoT devices did not receive expected DHCP leases while the VLAN interface and switch path were incomplete.

**Response:** Reviewed the VLAN interface, switch configuration, and client leases, then retested after correcting the path.

### Controller and device state became inconsistent

Configuration changes were not always reflected consistently across the controller and adopted network equipment.

**Response:** Reconciled the controller/device state and reapplied the intended configuration before validating connectivity.

## Why the Network Changed

The physical network had to be simplified because the preferred wired configuration was not suitable while the house was being prepared for sale. Running exposed Ethernet between the main floor and upstairs office was not acceptable, and a concealed route has proven difficult because of the house construction.

The ER605 was therefore removed from the active routing path, the VLAN configuration was rolled back, and the BE550 became the main router and Wi-Fi provider.

The current office connectivity is:

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

The RE705X provides the upstairs office with wireless backhaul to the BE550 and an Ethernet handoff to the Ubiquiti switching environment. Both EdgeSwitches remain operational through this path.

The active network is currently a flat LAN. The previous VLAN segmentation is not active.

The VLAN project was not removed because it failed. It was rolled back because the physical connectivity and household requirements changed.

## Lessons Learned

- Logical security controls depend on the physical network path supporting the intended design
- VLAN documentation must distinguish access ports, trunks, and native/untagged traffic
- DNS availability must be tested from every network that receives a DNS server through DHCP
- A configuration is not complete until addressing, reachability, isolation, and recovery have been validated
- Historical documentation should record when a design is no longer active
- Operational requirements sometimes require a simpler design, even after a more segmented design has been proven
- Physical cable routing can become a real architectural constraint in a home environment

## Current State

As of July 2026:

- BE550 is the active router and gateway
- BE550 provides primary Wi-Fi
- ER605 is not in the active routing path
- RE705X provides Wi-Fi backhaul to the upstairs office
- Ethernet from the RE705X feeds the Ubiquiti switching environment
- EdgeSwitch 8-Port 150W and EdgeSwitch 8XP are both operational
- RE605X provides wireless connectivity in the wife's office
- VLAN tagging and segmentation are not currently active
- The network operates as a flat LAN
- Telus network equipment is used separately for the Telus Smart Security system

## Planned Recovery Path

The preferred future improvement is a concealed Ethernet run between the main floor and the upstairs office. Once that wired backbone exists, the network can be redesigned around it and the ER605/VLAN architecture can be reintroduced and validated against the new physical topology.

---

[Home](../README.md) · [Networking Labs](./README.md) · [VLAN Segmentation Lab](./vlan-segmentation-lab.md) · [Top](#top)
