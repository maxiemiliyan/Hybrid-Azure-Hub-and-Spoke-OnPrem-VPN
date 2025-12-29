# Key Learnings

## Overview

This document summarizes the **technical, architectural, and operational learnings** gained from designing and implementing the hybrid Azure Hub-and-Spoke architecture with on-premises integration.

The project provided hands-on experience with **real-world enterprise networking patterns**, security enforcement, and hybrid workload communication.

---

## Architectural Learnings

- Understood the importance of **Hub-and-Spoke architecture** for scalable and secure network design
- Learned how centralizing connectivity and security in a Hub simplifies management and auditing
- Gained clarity on why workloads should never communicate directly across spokes in enterprise environments
- Learned how workload-driven design is more valuable than simple connectivity setups

---

## Hybrid Connectivity Learnings

- Implemented **route-based Site-to-Site VPN** using IPsec and IKEv2
- Learned how Azure VPN Gateway and on-prem RRAS work together as tunnel endpoints
- Understood routing dependencies between Azure and on-prem environments
- Gained experience troubleshooting VPN connectivity and routing issues

---

## Routing and Traffic Control Learnings

- Learned how **User Defined Routes (UDRs)** enforce mandatory traffic paths
- Understood how default routes (`0.0.0.0/0`) can be used to force centralized inspection
- Gained clarity on preventing routing bypass and lateral movement
- Learned how routing and security must be designed together, not separately

---

## Security Learnings

- Implemented **deny-by-default security posture** using Azure Firewall
- Learned how centralized firewall policies improve consistency and control
- Understood the importance of inspecting both inter-VNet and hybrid traffic
- Gained experience validating security using firewall metrics and logs

---

## Operational Learnings

- Learned how to validate enterprise architectures using structured test cases
- Gained experience monitoring traffic flow using Azure metrics
- Understood the importance of documentation for maintainability and audits
- Learned how to structure infrastructure documentation professionally for GitHub and resumes

---

## Troubleshooting and Validation Learnings

- Improved skills in diagnosing:
  - VPN connectivity issues
  - Routing misconfigurations
  - Firewall rule conflicts
- Learned how to validate both positive and negative traffic scenarios
- Understood how to confirm that security controls cannot be bypassed

---

## Professional Growth

Through this project, I gained:

- Confidence in designing hybrid cloud architectures
- Practical experience aligned with enterprise infrastructure roles
- A deeper understanding of Azure networking, security, and hybrid integration
- The ability to clearly explain architectural decisions in interviews

---

## Outcome

This project strengthened my ability to **design, implement, validate, and document enterprise-grade hybrid cloud infrastructure**, preparing me for real-world cloud and infrastructure engineering roles.
