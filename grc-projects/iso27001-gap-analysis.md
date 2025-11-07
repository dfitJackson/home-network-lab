# 🧩 ISO 27001 Gap Analysis

This page is my working ISO 27001 gap analysis — comparing what I currently have in place in my lab to the Annex A control areas.  
It’s a learning exercise to connect real configurations (like VLANs, Pi-hole, backups, and authentication) with the intent of ISO 27001 controls.

---

## 🧱 Framework Context

| Section | Description | My Current Lab Context |
|----------|--------------|-------------------------|
| **A.5 Information Security Policies** | Management direction for information security | I’ve drafted baseline policies under `grc-projects/policy-hierarchy.md` (planned). |
| **A.6 Organization of Information Security** | Roles and responsibilities | Defined basic separation between personal, work, and IoT networks. No formal policy yet. |
| **A.7 Human Resource Security** | Before, during, and after employment | Not applicable to lab; conceptually covered through access control for accounts. |
| **A.8 Asset Management** | Identification and ownership of assets | Assets tracked manually: switches, routers, Pi-hole host, personal systems. |
| **A.9 Access Control** | Authorized user access | VLAN segmentation acts as logical boundary; Pi-hole admin interface limited to personal VLAN only. |
| **A.10 Cryptography** | Protection of information | HTTPS enforced on management interfaces where supported; no PKI implementation yet. |
| **A.11 Physical and Environmental Security** | Prevent unauthorized physical access | Lab equipment located in a locked home office; environmental controls basic. |
| **A.12 Operations Security** | Change management, capacity, malware protection | Configuration backups manual; Docker containers updated monthly. |
| **A.13 Communications Security** | Network security management | VLAN segmentation implemented; trunk ports verified; no IDS/IPS yet. |
| **A.14 System Acquisition, Development and Maintenance** | Secure development and testing | Using isolated test VLAN for Docker and Node-RED testing. |
| **A.15 Supplier Relationships** | Security in supplier agreements | N/A (personal lab), but studying third-party risk for GRC documentation. |
| **A.16 Information Security Incident Management** | Reporting and response | Currently manual; plan to automate Pi-hole log alerts using Node-RED. |
| **A.17 Information Security Aspects of Business Continuity** | Availability and recovery | No automated backups yet; plan to test config restoration scripts. |
| **A.18 Compliance** | Legal and contractual requirements | Learning stage — mapping Canadian privacy laws (PIPEDA) for documentation practice. |

---

## 🧠 Observations

- This is a **learning-by-doing gap analysis**, not a formal audit.  
- I’m treating each Annex A area as a way to think about how controls map to actual configurations.  
- VLAN separation and Pi-hole DNS filtering directly support confidentiality and availability.  
- Missing areas (like backups, logging, and automation) will become projects in this repo.

---

## 🚧 Next Steps

- Add a **policy-hierarchy.md** file to show alignment from policy → standard → control.  
- Build a **control-mapping.md** document to cross-reference ISO 27001, NIST CSF, and CIS v8.  
- Automate periodic backups of EdgeSwitch and ER605 configs.  
- Continue improving documentation maturity to reflect ISO 27001 Annex A coverage.
