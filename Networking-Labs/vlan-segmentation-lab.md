<a id="top"></a>

[Home](../README.md) · [Networking Labs](./README.md) · [Networking Overview](./networking-lab-overview.md)

# VLAN Segmentation Lab

> **Status: Completed—Historical**
>
> This document describes a controlled home-network implementation that was configured and tested previously. VLAN segmentation is not active in the current network.

## Scope

The project implemented separate logical networks for Home, Work, and IoT devices using a TP-Link ER605 gateway and Ubiquiti EdgeSwitch hardware.

The goal was to practise the complete lifecycle of a segmentation project:

1. Design the logical networks
2. Configure gateway interfaces and managed switching
3. Assign representative endpoints
4. Validate addressing, internet access, DNS, and isolation
5. Diagnose configuration failures
6. Record lessons and eventual rollback

## Historical Lab Environment

### Equipment

- TP-Link ER605 gateway
- Ubiquiti EdgeSwitch 8-Port 150W
- Ubiquiti EdgeSwitch 8XP
- TP-Link BE550 Wi-Fi platform
- Pi-hole DNS filtering
- Windows and Ubuntu test endpoints

### Logical Design

| VLAN | Purpose | Intended security outcome |
|---|---|---|
| **1 — Home** | Personal devices and administrative access | Trusted home environment |
| **20 — Work** | Work endpoint connectivity | Separation from personal and IoT devices |
| **30 — IoT** | Smart-home and IoT devices | Restrict access to trusted systems |

A wired connection between the gateway and managed switching infrastructure supported the VLAN path during the project.

## Implementation Approach

### Gateway

The ER605 was used to create the VLAN interfaces, provide DHCP service, and control communication between networks.

### Managed switching

The EdgeSwitch equipment was used to assign device-facing access ports and carry the required VLAN traffic across the wired switching path.

The configuration was revised several times during testing. Exact historical port assignments are intentionally not presented as a current or reusable configuration because the physical topology has since changed.

### DNS

Pi-hole was tested as the DNS resolver for supported clients. DNS changes were introduced incrementally because an unreachable DNS resolver could interrupt otherwise functional network access.

## Validation Method

Testing focused on observable behaviour rather than assuming that a saved configuration was working.

### Address assignment

Representative endpoints were checked to confirm that they received addressing associated with the intended segment.

### Internet and DNS

Clients were tested for:

- Internet connectivity
- DNS resolution
- Reachability of Pi-hole where it was assigned
- Expected behaviour after DHCP or DNS changes

### Isolation

Cross-network reachability tests were used to determine whether Work, Home, and IoT devices could communicate in accordance with the intended policy.

### Configuration review

Router and switch settings were reviewed after failures to compare the intended design with the configuration actually applied.

## Troubleshooting Record

### Loss of DNS resolution

**Symptom:** Clients lost normal internet usability after DHCP DNS settings were changed.

**Cause:** Pi-hole was not reachable from every network receiving it as a resolver.

**Correction:** Restored a reachable DNS path, reviewed DHCP settings, and retested name resolution before expanding the configuration.

**Lesson:** DNS reachability must be validated independently from basic IP connectivity.

### Incorrect switch membership

**Symptom:** Devices appeared on the wrong logical network or lost expected access.

**Cause:** Tagged, untagged, and excluded switch membership did not match the intended port role.

**Correction:** Rebuilt the affected membership, refreshed client addressing, and repeated reachability tests.

**Lesson:** Vendor terminology must be mapped carefully to access-port and trunk behaviour.

### Missing IoT DHCP leases

**Symptom:** IoT test devices did not receive expected addressing.

**Cause:** The gateway interface and switch path were not consistently configured at the same time.

**Correction:** Reviewed both ends of the path, corrected the configuration, refreshed leases, and retested.

**Lesson:** VLAN troubleshooting must cover the complete path rather than a single device.

### Controller/device inconsistency

**Symptom:** Intended changes were not reflected consistently across managed equipment.

**Correction:** Reconciled controller and device state, reapplied the configuration, and validated the result from client systems.

**Lesson:** A controller showing a saved setting is not proof that the forwarding path behaves as intended.

## Project Outcome

The lab demonstrated:

- Logical network design for different trust levels
- VLAN interface and DHCP configuration
- Managed-switch access and uplink concepts
- DNS troubleshooting across segmented networks
- Isolation testing and evidence-based validation
- Recovery from misconfiguration
- Documentation of both implementation and rollback

## Rollback and Current Network

The segmented architecture was rolled back after the preferred inter-floor wired configuration became impractical. The house construction has made a concealed Ethernet run from the main floor to the upstairs office difficult, while exposed cabling is not suitable while the house is being prepared for sale.

The ER605 is therefore not part of the active routing path. The BE550 currently performs the router, gateway, and primary Wi-Fi roles.

The active topology is:

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

The RE705X keeps the upstairs lab connected and provides Ethernet to the Ubiquiti switches, but the inter-floor backhaul itself is wireless. Both EdgeSwitches are operational in the current network.

VLAN segmentation is intentionally not active in this configuration. The current network operates as a flat LAN.

### Current EdgeSwitch 150W Cleanup

The historical VLAN definitions remain on the EdgeSwitch 150W, but they are not being used for active segmentation.

- VLAN 1 is the active untagged LAN for current client connectivity.
- VLAN 20 (Work) remains defined but is not active for the current work-laptop connection.
- VLAN 30 (IoT) remains defined but is not active in the current network.
- Port 2 is connected to the work laptop and now operates as an untagged VLAN 1 client/access port.
- The old trunk flag on Port 2 was removed during the rollback cleanup because the work laptop no longer requires tagged VLAN traffic.

This cleanup aligns the switch with the present flat-LAN architecture while preserving the historical VLAN definitions for reference and future redesign work.

The project remains documented because the implementation and validation were completed; the operational environment later changed.

## Security Reflection

Returning to a flat network reduces isolation between device categories. That tradeoff is documented rather than hidden.

The preferred future path is to install concealed Ethernet between the main floor and upstairs office. Once that wired backbone is available, the ER605 and VLAN design can be rebuilt, revalidated, and documented as a new current implementation rather than assuming the historical configuration can simply be reused unchanged.

---

[Home](../README.md) · [Networking Labs](./README.md) · [Networking Overview](./networking-lab-overview.md) · [Top](#top)