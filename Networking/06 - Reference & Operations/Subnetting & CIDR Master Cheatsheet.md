---
tags:
  - networking
  - subnetting
  - cheatsheet
  - cidr
  - reference
aliases:
  - Subnetting Cheatsheet
  - CIDR Conversion Table
  - Wildcard Masks
date_created: 2026-10-11
---

# 📊 Networking Reference: Complete Subnetting & CIDR Master Cheatsheet

> [!ABSTRACT] Quick Reference
> This document provides an exhaustive, instant lookup table for all 33 IPv4 subnet masks (`/0` through `/32`), wildcard masks, host capacities, and mental subnetting shortcuts.

---

## 1. Complete IPv4 CIDR Master Reference Table

| CIDR Prefix | Subnet Mask | Wildcard Mask | Total IPs ($2^H$) | Usable Hosts ($2^H - 2$) | Classful Equivalent |
|:---:|---|---|:---:|:---:|---|
| **/32** | `255.255.255.255` | `0.0.0.0` | 1 | 1 (Single Host / Loopback) | Single Host Route |
| **/31** | `255.255.255.254` | `0.0.0.1` | 2 | 2 (RFC 3021 Point-to-Point) | PtP Links (No broadcast) |
| **/30** | `255.255.255.252` | `0.0.0.3` | 4 | 2 | Legacy PtP Router Link |
| **/29** | `255.255.255.248` | `0.0.0.7` | 8 | 6 | Small Public WAN Block |
| **/28** | `255.255.255.240` | `0.0.0.15` | 16 | 14 | DMZ / Microservice Pod |
| **/27** | `255.255.255.224` | `0.0.0.31` | 32 | 30 | Database Cluster |
| **/26** | `255.255.255.192` | `0.0.0.63` | 64 | 62 | Medium Branch Office |
| **/25** | `255.255.255.128` | `0.0.0.127` | 128 | 126 | Large Department Subnet |
| **/24** | `255.255.255.0` | `0.0.0.255` | 256 | 254 | 1 Class C |
| **/23** | `255.255.254.0` | `0.0.1.255` | 512 | 510 | 2 Class C networks |
| **/22** | `255.255.252.0` | `0.0.3.255` | 1,024 | 1,022 | 4 Class C networks |
| **/21** | `255.255.248.0` | `0.0.7.255` | 2,048 | 2,046 | 8 Class C networks |
| **/20** | `255.255.240.0` | `0.0.15.255` | 4,096 | 4,094 | 16 Class C networks |
| **/19** | `255.255.224.0` | `0.0.31.255` | 8,192 | 8,190 | 32 Class C networks |
| **/18** | `255.255.192.0` | `0.0.63.255` | 16,384 | 16,382 | 64 Class C networks |
| **/17** | `255.255.128.0` | `0.0.127.255`| 32,768 | 32,766 | 128 Class C networks |
| **/16** | `255.255.0.0` | `0.0.255.255`| 65,536 | 65,534 | 1 Class B |
| **/15** | `255.254.0.0` | `0.1.255.255`| 131,072 | 131,070 | 2 Class B networks |
| **/14** | `255.252.0.0` | `0.3.255.255`| 262,144 | 262,142 | 4 Class B networks |
| **/13** | `255.248.0.0` | `0.7.255.255`| 524,288 | 524,286 | 8 Class B networks |
| **/12** | `255.240.0.0` | `0.15.255.255`| 1,048,576 | 1,048,574 | RFC 1918 (172.16.0.0/12) |
| **/11** | `255.224.0.0` | `0.31.255.255`| 2,097,152 | 2,097,150 | 32 Class B networks |
| **/10** | `255.192.0.0` | `0.63.255.255`| 4,194,304 | 4,194,302 | RFC 6598 CGNAT block |
| **/9** | `255.128.0.0` | `0.127.255.255`| 8,388,608 | 8,388,606 | 128 Class B networks |
| **/8** | `255.0.0.0` | `0.255.255.255`| 16,777,216 | 16,777,214 | 1 Class A (10.0.0.0/8) |
| **/0** | `0.0.0.0` | `255.255.255.255`| 4,294,967,296 | All IPv4 Internet | Default Route (`0.0.0.0/0`) |

---

## 2. The 3-Second Mental Subnetting Method

Whenever you encounter a CIDR prefix $P$:

1. **Find the Interesting Octet:**
   * $P$ between 1 and 8 $\implies$ Octet 1
   * $P$ between 9 and 16 $\implies$ Octet 2
   * $P$ between 17 and 24 $\implies$ Octet 3
   * $P$ between 25 and 32 $\implies$ Octet 4
2. **Find Remaining Bits in that Octet ($R$):**
   $$R = P \bmod 8 \quad (\text{if } 0 \text{, } R = 8)$$
3. **Calculate the Magic Number (Step Size):**
   $$\text{Step Size} = 2^{(8 - R)}$$

### Real-World Example:
* Given: `10.50.120.0/21`
* Octet: 21 is between 17 and 24 $\implies$ **3rd Octet is interesting**.
* Bits: $21 \bmod 8 = 5$ bits borrowed. Remaining bits in 3rd octet = $8 - 5 = 3$ bits.
* Step Size: $2^3 = \mathbf{8}$.
* Subnets increment by 8 in the 3rd octet: `0, 8, 16, ... 112, 120, 128...`
* The subnet is `10.50.120.0`, next is `10.50.128.0`.
* Broadcast: `10.50.127.255`. Usable: `10.50.120.1` to `10.50.127.254`. Done in 3 seconds!
