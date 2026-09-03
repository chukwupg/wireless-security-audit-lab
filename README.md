# Wireless Security Assessment Lab (WPA/WPA2 Audit)

## ⚠️ Disclaimer
This project was conducted in a controlled lab environment against an authorized personal network and device. All techniques are for educational and defensive purposes only.

---

## Overview

This project demonstrates a structured wireless security assessment against a WPA/WPA2-protected network. The objective was to evaluate real-world weaknesses in wireless authentication and password security.

The assessment covers:

- Wireless reconnaissance and access point identification
- Client identification and traffic monitoring
- WPA/WPA2 4-way handshake capture
- Controlled deauthentication testing
- Offline password recovery
- Security analysis and defensive recommendations

---

## Objectives

- Assess Wi-Fi network exposure to handshake-based attacks
- Demonstrate risks of weak password selection
- Evaluate management frame vulnerabilities
- Provide actionable hardening recommendations

---

## Result

- Successfully captured WPA authentication handshake
- Performed offline password recovery using wordlist attack
- Identified weak credential exposure risk
- Analyzed management frame weaknesses
- Produced defensive recommendations aligned with industry practices

---

## Tools & Technologies

See [`tools-used.md`](tools-used.md)

---

## Methodology

See [`methodology.md`](methodology.md)

For commands and screenshots see: [`command-log`](command-log.md)

---

## Attack Workflow

See [`adversary-simulation.md`](adversary-simulation.md)

---

## Findings

See [`findings.md`](findings.md)

---

## Remediation

See [`remediation.md`](remediation.md)

---

## Lessons Learned

See [`lessons-learned.md`](lessons-learned.md)

---

## Insight

The security of WPA/WPA2 networks depends heavily on password strength and configuration, not just encryption protocol. Weak credentials render otherwise secure networks vulnerable to offline attacks.

---
