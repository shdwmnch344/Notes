---
tags:
  - networking
  - ports
  - cheatsheet
  - security
  - reference
aliases:
  - Master Port Reference
  - Port Security Matrix
date_created: 2026-10-11
---

# 🚪 Networking Reference: Master Port, Protocol & Service Directory

> [!ABSTRACT] Quick Reference
> This directory catalogs over 60 essential port numbers spanning web, infrastructure, database, DevOps, email, and directory services, complete with RFC definitions, transport mechanisms, and security audit implications.

---

## 1. Web, Remote Access & Infrastructure Ports

| Port | Protocol / Service | Transport | RFC | Security Assessment & Recommendations |
|:---:|:---:|:---:|:---:|---|
| **20 / 21** | **FTP** | TCP | RFC 959 | Cleartext user/password and data. Replace with SFTP (Port 22). |
| **22** | **SSH / SFTP** | TCP | RFC 4253 | Secure encrypted shell. Restrict access via VPN, MFA, and `fail2ban`. |
| **23** | **Telnet** | TCP | RFC 854 | Completely unencrypted. Drop at edge firewall. |
| **53** | **DNS** | UDP / TCP | RFC 1035 | Core resolution. Secure using DNSSEC, DoH, or DoT. |
| **67 / 68** | **DHCP** | UDP | RFC 2131 | Auto-configuration. Prevent rogue servers with DHCP Snooping. |
| **69** | **TFTP** | UDP | RFC 1350 | Trivial FTP. No authentication; restrict to isolated provisioning VLANs. |
| **80** | **HTTP** | TCP | RFC 2616 | Unencrypted web. Enforce 301 redirect to Port 443 + HSTS. |
| **88** | **Kerberos** | TCP / UDP | RFC 4120 | Enterprise authentication (Active Directory). |
| **111** | **RPCBind / Portmapper** | TCP / UDP | RFC 1833 | Legacy RPC. Frequent target of DDoS reflection. |
| **123** | **NTP** | UDP | RFC 5905 | Network Time Protocol. Protect against monlist amplification. |
| **161 / 162** | **SNMP** | UDP | RFC 1157 | Network device telemetry. Upgrade from SNMPv1/v2c to SNMPv3. |
| **389** | **LDAP** | TCP / UDP | RFC 4511 | Cleartext directory queries. Migrate to LDAPS (Port 636). |
| **443** | **HTTPS** | TCP | RFC 2818 | Encrypted web. Enforce TLS 1.3 and disable legacy TLS 1.0/1.1. |
| **445** | **SMB over IP** | TCP | RFC 445 | Windows file sharing. Never expose to public WAN (WannaCry vector). |
| **514** | **Syslog** | UDP / TCP | RFC 5424 | Centralized logging. Use TLS-wrapped syslog over TCP. |
| **636** | **LDAPS** | TCP | RFC 4513 | Secure LDAP over TLS. |
| **853** | **DNS-over-TLS (DoT)** | TCP | RFC 7858 | Encrypted DNS queries. |
| **3389** | **RDP** | TCP / UDP | Proprietary | Microsoft Remote Desktop. Gate behind VPN/bastion host + MFA. |

---

## 2. Databases & In-Memory Cache Ports

| Port | Database Engine | Transport | Default Status | Production Hardening Best Practice |
|:---:|:---:|:---:|:---:|---|
| **1433** | **MS SQL Server** | TCP | Unencrypted by default | Enforce TLS, bind to private VPC subnet. |
| **1521** | **Oracle Database** | TCP | Sensitive | Restrict listener via `sqlnet.ora` IP restrictions. |
| **3306** | **MySQL / MariaDB** | TCP | Common WAN target | Bind strictly to `127.0.0.1` or private subnet, never `0.0.0.0`. |
| **5432** | **PostgreSQL** | TCP | Secure | Enforce `hostssl` in `pg_hba.conf`, rotate credentials. |
| **6379** | **Redis** | TCP | Unauthenticated by default | High RCE risk! Enforce `requirepass`, rename dangerous commands. |
| **11211** | **Memcached** | TCP / UDP | Reflection target | Bind strictly to localhost; disable UDP listener. |
| **27017** | **MongoDB** | TCP | Common data leak vector | Enable role-based access control (RBAC), bind to private IP. |

---

## 3. DevOps, Cloud & Container Orchestration Ports

| Port | Service | Transport | Role in Infrastructure |
|:---:|:---:|:---:|---|
| **2375 / 2376** | **Docker Daemon** | TCP | Remote Docker API (`2375` unencrypted, `2376` TLS). Never expose unencrypted! |
| **6443** | **Kubernetes API** | TCP | Core Kubernetes control plane API server. |
| **8080 / 8443** | **HTTP / HTTPS Alt** | TCP | Development web servers, Tomcat, Jenkins. |
| **9000** | **SonarQube / Portainer** | TCP | Code quality & container management. |
| **9090** | **Prometheus** | TCP | Systems metrics monitoring server. |
| **9092** | **Apache Kafka** | TCP | Distributed event streaming broker. |
| **9200 / 9300** | **Elasticsearch** | TCP | Search engine REST API (9200) & internal node cluster (9300). |
| **3000** | **Grafana / React** | TCP | Metrics dashboard & front-end development server. |
