# 🧾 Risk Register

This page is where I track and document risks as part of my GRC learning and lab work.  
Each entry helps me connect theory (like ISO 27005 and NIST RMF) with how risk management actually looks in practice.

---

## 📊 Risk Table

| ID | Risk Description | Likelihood | Impact | Risk Level | Control(s) | Status | Notes |
|----|------------------|-------------|---------|-------------|-------------|---------|--------|
| R-001 | Unpatched IoT devices could be exploited from VLAN 30. | Medium | High | **High** | Network segmentation, VLAN isolation | In Progress | Will move IoT VLAN to separate SSID once Omada APs are added. |
| R-002 | Work VLAN (20) traffic may route through same DNS as personal VLAN (1). | Low | Medium | **Medium** | Configure VLAN-based DNS via ER605. | Open | To apply Pi-hole filtering per VLAN later. |
| R-003 | No automated log collection from switches or Pi-hole container. | Medium | Medium | **Medium** | Enable Syslog forwarding to local collector. | Planned | Node-RED integration test planned. |
| R-004 | No backup of EdgeSwitch configuration. | Medium | High | **High** | Manual backup after each change. | Open | Add scheduled backup routine once SSH automation is set up. |

---

## 🧠 Notes

- I’m using a simple **likelihood × impact** model for now — qualitative, not quantitative.  
- **High = needs attention now**, **Medium = monitor or plan controls**, **Low = accepted**.  
- Eventually I’ll apply FAIR-style quantitative scoring once I’m more comfortable with metrics.  
- The point here isn’t to be formal — it’s to build the habit of identifying and tracking risks.

---

## 🚧 Next Steps

- Add references to ISO 27005 risk assessment steps.  
- Create a “Control Mapping” table linking these risks to specific framework controls (ISO/NIST).  
- Expand risk entries as my network and lab environments grow.
