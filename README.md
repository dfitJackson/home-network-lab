![Jackson's Security Lab](images/jacksons_security_lab_banner.png)

# Home Network & Cybersecurity Lab

This repository documents hands-on cybersecurity, networking, systems administration, and governance work completed in my home lab and through sanitized professional experience.

The environment has changed over time. To keep the portfolio accurate, projects are identified as **Current**, **In Progress**, **Completed—Historical**, or **Planned**. Historical projects remain documented because they represent systems that were implemented and validated, even when they are no longer part of the active network.

## Project Status

| Status | Meaning |
|---|---|
| **Current** | Running in the present environment |
| **In Progress** | Operational or actively being developed, but not yet complete |
| **Completed—Historical** | Implemented and validated previously, then retired or replaced |
| **Planned** | Identified for future work but not yet implemented |

## Current Environment

*Verified July 2026*

### Network

- **Current router / gateway:** TP-Link BE550
- **Primary Wi-Fi:** TP-Link BE550
- **Upstairs office:** TP-Link RE705X using Wi-Fi backhaul to the BE550, with Ethernet from the RE705X into the Ubiquiti switching environment
- **Wife's office:** TP-Link RE605X using Wi-Fi backhaul to the BE550
- **Managed switching:** Ubiquiti EdgeSwitch 8-Port 150W and EdgeSwitch 8XP are both operational through the upstairs office connection
- **Current network state:** Flat LAN; VLAN segmentation is rolled back and not currently active
- **ER605 status:** Removed from the active routing path after the network was simplified because the required wired configuration was not practical while the house is being prepared for sale
- **Telus equipment:** Used separately for the Telus Smart Security system

The current design prioritizes reliable connectivity without exposed inter-floor Ethernet. The BE550 now performs routing and primary Wi-Fi duties. Upstairs connectivity reaches the RE705X over Wi-Fi, then transitions to Ethernet for the office switching environment. This keeps the lab and both Ubiquiti switches online, but the inter-floor backhaul remains wireless.

A future infrastructure goal is to install a concealed Ethernet run from the main floor to the upstairs office. That would provide a wired backbone for the office and create a better foundation for restoring the ER605 and VLAN segmentation later.

### Server and Storage

- **Primary host:** Dell OptiPlex 7050 running Ubuntu and Docker
- **Current NAS:** Synology DS920+ with two 8 TB hard drives and a 1 TB SSD cache
- **Previous NAS:** Synology DS218play, retired from the primary-storage role after the completed July 2026 migration to the DS920+
- **Previous host:** HP All-in-One Ubuntu server, now decommissioned after services were migrated to the Dell

### Active Security and Administration Services

| Service | Purpose | Status |
|---|---|---|
| **Wazuh** | SIEM platform using Wazuh Manager, Indexer/OpenSearch, and Dashboard | **In Progress** |
| **Pi-hole** | Network DNS filtering and query visibility | **Current** |
| **Authentik** | Identity and access management platform for self-hosted services | **In Progress** |
| **Docker / Portainer** | Container deployment and administration | **Current** |
| **Home Assistant / Matter Server** | Home automation, integrations, and device management | **Current** |

The Dell also hosts personal productivity, AI, media, and request-management services. The public portfolio emphasizes the security, infrastructure, administration, and troubleshooting work involved rather than exposing unnecessary operational detail.

## Featured Work

### VLAN Segmentation Lab — Completed—Historical

Designed, implemented, and validated separate Home, Work, and IoT network segments using the ER605 and Ubiquiti EdgeSwitch hardware. Testing included DHCP assignment, routing, isolation, DNS reachability, and troubleshooting across router and switch configurations.

The segmented design was later rolled back when the physical Ethernet path required for the preferred topology was no longer practical. The active network now uses the BE550 as the router, wireless backhaul to the RE705X and RE605X, and a flat LAN. The VLAN work remains documented as a completed historical lab and is planned for future reimplementation once a suitable wired backbone can be installed.

- [Networking Labs](./Networking-Labs/README.md)
- [VLAN Segmentation Lab](./Networking-Labs/vlan-segmentation-lab.md)
- [Networking Lab Overview](./Networking-Labs/networking-lab-overview.md)
- [Historical Network Diagram](./images/Home%20Network.drawio.png)

### Wazuh SIEM — In Progress

Runs a Docker-based Wazuh single-node environment consisting of the Manager, Indexer/OpenSearch, and Dashboard. The platform is operational; agent onboarding, log-source expansion, detection validation, and portfolio documentation remain in progress.

- [Wazuh IAM Administration Runbook](./WAZUH-SIEM/Runbooks/iam-admin-user.md)

### Pi-hole DNS Filtering — Current

Pi-hole runs in Docker on the Dell server and provides DNS filtering and network-query visibility. The deployment has included static addressing, DHCP/DNS troubleshooting, allowlisting for self-hosted services, and validation of DNS query flow.

### Authentik Identity and Access Management — In Progress

Authentik is deployed on the Dell OptiPlex 7050 as a Docker-based identity and access management platform. The server, worker, and PostgreSQL components are operational. Application integration, SSO, and MFA use cases remain in progress and should not yet be treated as a fully implemented identity layer for the self-hosted environment.

### Penetration Testing Labs

Controlled lab exercises use Kali Linux and intentionally vulnerable targets to practise reconnaissance, enumeration, exploitation, validation, evidence collection, and remediation analysis.

- [Penetration Testing Portfolio](./penetration-testing-labs/README.md)
- [First Exploit: vsftpd 2.3.4 in Metasploitable2](./penetration-testing-labs/lab-projects/first-exploit.md)

All lab testing is performed in authorized, isolated environments.

### Sanitized Professional Experience

This repository also contains sanitized summaries of authorized contract security work. Client identities, URLs, credentials, proprietary data, and raw evidence are intentionally excluded.

- [Web Application Portfolio Assessment](./professional-experience/web-app-portfolio-assessment.md)

### Governance, Risk, and Compliance

The GRC section applies risk-management and security-control concepts to lab infrastructure. It is being reviewed and modernized to distinguish completed evidence from planned work and to align future documentation with current framework versions.

- [GRC Projects](./grc-projects/README.md)

## Skills Demonstrated

- Network architecture, troubleshooting, and segmentation
- Linux and Docker administration
- DNS filtering and validation
- SIEM deployment with Wazuh and OpenSearch
- Identity and access management with Authentik
- Web application security testing with Burp Suite, Nmap, curl, and OWASP-aligned methods
- Controlled exploitation with Kali Linux, Metasploit, and Metasploitable2
- Evidence collection, technical reporting, and remediation guidance
- Risk analysis and security-control mapping

## Repository Guide

| Area | Description | Status |
|---|---|---|
| [Networking Labs](./Networking-Labs/README.md) | Current network architecture plus historical VLAN implementation, validation, and troubleshooting | **Current / Historical** |
| [Systems Labs](./Systems-Labs/README.md) | Storage, server, migration, and systems administration projects | **Active** |
| [Penetration Testing](./penetration-testing-labs/README.md) | Controlled labs and sanitized professional summaries | **Active** |
| [Wazuh SIEM](./WAZUH-SIEM/Runbooks/iam-admin-user.md) | SIEM deployment and operational runbooks | **In Progress** |
| [GRC Projects](./grc-projects/README.md) | Risk, controls, policies, and framework exercises | **Updating** |
| [Professional Experience](./professional-experience/web-app-portfolio-assessment.md) | Sanitized authorized-security engagement summaries | **Active** |
| [Images](./images/README.md) | Diagrams and supporting visual assets | **Updating** |

## Documentation Principles

- Clearly distinguish current systems from historical projects
- Document only work that was actually performed
- Separate lab exercises from professional engagements
- Sanitize client and operational information
- Include validation, problems encountered, corrective actions, and lessons learned
- Treat this repository as an evolving technical record rather than a claim that every documented system remains active

## Current Documentation Priorities

1. Add a current network diagram showing the BE550, RE705X, RE605X, wireless backhaul, and upstairs EdgeSwitches
2. Plan a concealed Ethernet path from the main floor to the upstairs office
3. Reintroduce the ER605 and rebuild VLAN segmentation when a suitable wired backbone is available
4. Expand the Wazuh section with architecture, agent onboarding, and validation
5. Complete Authentik application integration, SSO/MFA testing, and documentation
6. Modernize the GRC material and framework references
