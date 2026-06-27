# Wazuh Agent Enrollment & Connection Types

> A practical guide to understanding **Wazuh Agent Enrollment**, authentication methods, connection modes, security best practices, and production-ready deployment recommendations.

![Wazuh](https://img.shields.io/badge/Wazuh-4.x-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Community%20Guide-success)

---

## Overview

This repository contains a comprehensive guide explaining how **Wazuh agents securely enroll with a Wazuh Manager**, how authentication works, and how to choose the appropriate enrollment method for different environments.

The guide is designed for:

- Security Engineers
- SOC Analysts
- DevSecOps Engineers
- System Administrators
- Wazuh Community Members

Whether you're deploying a small lab or managing hundreds of endpoints in production, this guide provides practical examples and security recommendations.

---

## What You'll Learn

✔ How Wazuh Agent Enrollment Works

✔ Authentication Methods

- No Authentication
- Password Authentication
- TLS/SSL Certificates
- Password + TLS (Recommended)

✔ Connection Modes

- TCP vs UDP
- Event Forwarding
- Secure Communication

✔ Production Configuration

- Manager Configuration
- Agent Configuration
- Certificate Setup
- Password Management

✔ Step-by-Step Walkthroughs

- Password Only
- TLS Only
- Password + TLS

✔ Troubleshooting

Common enrollment problems including:

- Invalid password
- TLS verification failures
- Agent already registered
- Never connected
- Protocol mismatch

---

## Repository Structure

```
.
├── Wazuh Agent Enrollment.docx
├── README.md
└── images/
```

---

## Recommended Production Setup

| Component | Recommendation |
|-----------|----------------|
| Authentication | Password + TLS |
| Transport | TCP |
| Certificates | Per-Agent |
| Encryption | AES |
| Password Management | Secret Manager |
| CA Key | Keep Offline |

---

## Security Best Practices

- Use **Password + TLS** whenever possible.
- Keep your **Root CA private key offline**.
- Rotate enrollment passwords regularly.
- Prefer **TCP** over UDP in production.
- Automate certificate issuance for large deployments.
- Store secrets using secure secret management solutions.

---

## Document Contents

- Overview
- Agent Enrollment Workflow
- Authentication Methods
- Connection Modes
- Configuration Reference
- Method Comparison
- Production Recommendations
- Walkthroughs
- Troubleshooting
- Quick Reference Cheatsheet

---

## Target Audience

- Wazuh Administrators
- DevSecOps Engineers
- Security Engineers
- Infrastructure Engineers
- SOC Teams
- Blue Teams

---

## Contributing

Suggestions, corrections, and improvements are welcome.

If you discover an issue or have recommendations for improving the guide, feel free to open an Issue or submit a Pull Request.

---

## About

This guide was created to help the Wazuh community better understand secure agent enrollment and deployment practices.

As a **Wazuh Ambassador**, my goal is to create practical, production-focused documentation that simplifies complex topics while promoting security best practices.

If you find this guide useful, consider giving the repository a ⭐.

---

## License

This project is released under the MIT License.
