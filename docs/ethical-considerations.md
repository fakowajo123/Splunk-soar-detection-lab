# Ethical, Legal and Professional Considerations

---

## Research Ethics

This project was conducted in full compliance with Kingston University London's research ethics guidelines and received appropriate approval before any practical work commenced.

---

## Legal Compliance

All attack simulations and security testing were conducted exclusively within a self-contained, isolated AWS Virtual Private Cloud (VPC) with no connectivity to external networks, real systems, or live infrastructure. No real users, organisations, or data were targeted at any point.

This project complies with:
- **UK Computer Misuse Act 1990** — no unauthorised access to computer systems
- **UK GDPR / Data Protection Act 2018** — no real personal data collected or processed
- **Kingston University Research Ethics Policy** — approved research methodology

---

## Human Oversight in Automated Response

A core principle of this project is that automation should augment human decision-making, not replace it entirely. The system is designed with this in mind:

- **LOW severity alerts** — logged and queued for analyst review
- **MEDIUM severity alerts** — automated containment with immediate analyst notification
- **HIGH severity alerts** — automated isolation with mandatory human escalation before further action

The system maintains a complete audit trail of every automated action taken, including timestamps, actions performed, and outcomes. This ensures full accountability and auditability of all automated responses.

---

## Transparency and Auditability

Every step of the automated workflow is logged in n8n's execution history and Splunk's audit logs. This means:
- Every detection can be traced back to a specific log event
- Every response action can be reviewed and reversed if needed
- Every Slack notification includes the evidence chain that triggered it

---

## Responsible Disclosure

The scripts and techniques documented in this repository are provided for educational and research purposes only. Any use of these materials against systems without explicit written authorisation is illegal and unethical.

---

## Professional Standards

This project aligns with professional cybersecurity standards and frameworks:
- **NIST Cybersecurity Framework** — Identify, Protect, Detect, Respond, Recover
- **MITRE ATT&CK** — industry standard adversarial technique taxonomy
- **ISC2 Code of Ethics** — protect society, act honourably, provide diligent service
