# 🔐 Web Application Portfolio Assessment

**Engagement Type:** Portfolio Configuration Review & Vulnerability Assessment  
**Date:** December 2025  
**Scope:** 30 web application endpoints across staging and pre-production environments  
**Role:** Junior Penetration Tester (contract support under senior tester)  
**Methodology:** OWASP Testing Guide v4.2  
**Deliverable:** Formal findings report + weekly status updates to senior tester

---

## 📋 Engagement Overview

Performed a security assessment across a portfolio of 30 staging and pre-production web application endpoints for a confidential client. Access was primarily unauthenticated (URL-based), with credentials available for select endpoints only.

The assessment focused on:
- HTTP security header configuration
- Session management and cookie security
- Access control and information disclosure
- TLS/cryptographic configuration
- Supported HTTP methods

A key outcome was identifying a **"Two-Tier" security posture** across the portfolio:

| Tier | Infrastructure | Posture |
|------|---------------|---------|
| Tier 1 | Modern cloud (Azure/AWS PaaS) | Strong — consistent CSP, HSTS, and secure session management |
| Tier 2 | Legacy IIS on-prem/shared hosting | Systemic weaknesses — partially hardened template with recurring misconfigurations |

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| Nmap | Port enumeration, TLS cipher suite identification, HTTP method detection |
| curl | HTTP response header extraction and manual verification |
| Burp Suite | Request inspection, header analysis, cookie flag review |
| Custom scripts | Clickjacking PoC generation, batch header validation, cookie evaluation |
| OWASP ZAP | Automated scanning baseline |

---

## 🔍 Findings Summary

| # | Finding | Risk | Affected Hosts |
|---|---------|------|---------------|
| 1 | Critical CSP Misconfiguration (Wildcard Directive) | 🔴 High | 1 host (Admin Portal) |
| 2 | Exposure of Internal Testing Tool (No Auth Required) | 🔴 High | 2 hosts |
| 3 | Insecure Session Management (Missing Secure Cookie Flag) | 🟡 Medium | 18 of 30 hosts |
| 4 | Missing Anti-Framing Headers (Clickjacking) | 🟡 Medium | 6 hosts |
| 5 | Legacy TLS Cipher Suites Enabled (CBC/Lucky13) | 🟢 Low | All IIS-hosted endpoints |
| 6 | HTTP TRACE Method Enabled | 🟢 Low | All IIS-hosted endpoints |

---

## 📌 Detailed Findings

### Finding 1 — Critical CSP Misconfiguration (Wildcard Directive)
**Risk:** 🔴 High | **Type:** Security Misconfiguration

The admin portal implemented a Content Security Policy with a wildcard `*` directive for `default-src` and `script-src`, combined with `'unsafe-inline'` and `'unsafe-eval'`. This effectively disables all XSS protections, allowing scripts to be loaded from any origin.

**Header observed:**
```
Content-Security-Policy: default-src * 'unsafe-inline'; script-src * 'unsafe-eval' 'unsafe-inline'
```

**Impact:** Enables execution of malicious scripts from external domains, bypassing XSS mitigations and facilitating session hijacking or data exfiltration.

---

### Finding 2 — Exposure of Internal Testing Tool (Unauthenticated Access)
**Risk:** 🔴 High | **Type:** Broken Access Control / Information Disclosure

An internal diagnostic endpoint (`/internal/ImageTest`) was publicly accessible without any authentication. The endpoint disclosed server technology stack details via response headers (`X-AspNetMvc-Version`, `X-Powered-By`) and accepted large payloads, creating potential for DoS abuse.

**Impact:** Expands attack surface, leaks infrastructure details, and allows unauthorized interaction with internal processing logic.

---

### Finding 3 — Insecure Session Management (Missing Secure Cookie Flag)
**Risk:** 🟡 Medium | **Type:** Insecure Configuration

Secondary session tokens (load balancer persistence cookies) were consistently set without the `Secure` attribute across **18 of 30 hosts**. While the primary session cookie was sometimes protected, these secondary tokens remained vulnerable to interception over HTTP.

**Impact:** Tokens can be intercepted via MitM attacks if a user is downgraded from HTTPS to HTTP.

**Scale:** Systemic — validated across all IIS-hosted endpoints in the portfolio.

---

### Finding 4 — Missing Anti-Framing Headers (Clickjacking)
**Risk:** 🟡 Medium | **Type:** UI Redressing

Six endpoints failed to implement either `X-Frame-Options` or `Content-Security-Policy: frame-ancestors`, allowing the application to be embedded in attacker-controlled iframes. PoC HTML files were created to validate each affected endpoint.

**Impact:** Facilitates clickjacking attacks where users can be manipulated into unintended actions without their knowledge.

---

### Finding 5 — Legacy TLS Cipher Suites Enabled
**Risk:** 🟢 Low | **Type:** Cryptographic Failure

All IIS-hosted endpoints supported legacy CBC-mode cipher suites (e.g., `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`), which are susceptible to padding oracle attacks (Lucky13). Azure/AWS-hosted applications correctly prioritized GCM suites.

**Impact:** Fails modern cryptographic standards (PCI-DSS). Exploitation is complex but the configuration represents unnecessary risk.

---

### Finding 6 — HTTP TRACE Method Enabled
**Risk:** 🟢 Low | **Type:** Security Misconfiguration

The HTTP TRACE method was enabled across all IIS-hosted endpoints. While Cross-Site Tracing (XST) is largely mitigated by modern browsers, enabling TRACE violates secure configuration baselines and aids in header enumeration.

**Impact:** Minor information disclosure; violates hardening best practices.

---

## ✅ Positive Security Control Identified

Manual testing of input fields on authenticated pages confirmed correct output encoding — special characters such as `<` were properly encoded as `&lt;`, effectively mitigating Reflected XSS in tested paths. This was documented as a positive control in the final report.

---

## 📝 Methodology Notes

- Reconnaissance performed via subdomain enumeration and infrastructure fingerprinting (IIS vs. cloud PaaS)
- Automated scanning with Nmap and custom scripts for ports, TLS ciphers, and HTTP methods
- Manual verification used for all findings — clickjacking validated with custom PoC HTML files, headers reviewed directly via curl and Burp Suite
- Findings documented with screenshots, raw request/response evidence, and remediation recommendations
- Weekly status updates delivered to senior tester throughout engagement
- Final structured report delivered upon engagement completion

---

## 🔧 Remediation Recommendations Delivered

1. Replace wildcard CSP with explicit, allowlisted directives
2. Restrict or remove the internal testing endpoint from public access
3. Add `Secure` flag to all session and persistence cookies
4. Implement `X-Frame-Options: DENY` or `CSP: frame-ancestors 'none'` across all endpoints
5. Disable CBC cipher suites and enforce GCM-only TLS configuration
6. Disable HTTP TRACE across all web servers via IIS configuration
