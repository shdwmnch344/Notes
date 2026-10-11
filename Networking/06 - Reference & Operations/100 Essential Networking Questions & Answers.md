---
tags:
  - networking
  - interview-prep
  - ccna
  - network-plus
  - flashcards
  - q-and-a
aliases:
  - 100 Essential Networking Questions
  - Networking Interview Bank
date_created: 2026-10-11
---

# 🎯 100 Essential Networking Interview Questions & Answers

> [!ABSTRACT] High-Yield Interview & Exam Preparation
> This question bank covers the top 100 most frequently tested networking concepts in technical interviews (SRE, DevOps, Cloud Engineering, Systems Engineering, Security) and industry certifications (Cisco CCNA, CompTIA Network+).
> 
> * **Format:** Obsidian toggle callouts (`> [!QUESTION]` with nested `> [!ANSWER]`) for interactive self-quizzing.

---

## Section 1: Networking Models & Architecture (OSI & TCP/IP)

> [!QUESTION] Q1: What is the primary difference between the OSI model and the TCP/IP model?
> > [!ANSWER] 
> > The OSI model is a theoretical 7-layer framework developed by ISO to prescribe how communications should be standardized. The TCP/IP model is a pragmatic 4-layer architecture developed by DARPA based on protocols actually implemented and deployed across the Internet. OSI separates Presentation and Session layers, whereas TCP/IP consolidates them into the Application layer.

> [!QUESTION] Q2: What is the difference between a Packet, a Frame, and a Segment?
> > [!ANSWER]
> > They are Protocol Data Units (PDUs) at different layers:
> > * **Segment / Datagram:** Layer 4 (Transport) PDU with port headers.
> > * **Packet:** Layer 3 (Network) PDU with logical IP addresses that survives across router hops.
> > * **Frame:** Layer 2 (Data Link) PDU with physical MAC addresses that is recreated at every hop.

> [!QUESTION] Q3: What happens during data decapsulation when a frame arrives at an operating system?
> > [!ANSWER]
> > 1. Layer 2: NIC checks CRC32 in FCS; if valid and MAC matches, strips Ethernet header and inspects **EtherType** (e.g., `0x0800`).
> > 2. Layer 3: Kernel decodes IPv4 packet, validates checksum, decrements TTL, inspects **Protocol** field (e.g., `6` for TCP), and strips IP header.
> > 3. Layer 4: TCP subsystem checks checksum, sequence numbers, and inspects **Destination Port** (e.g., `443`), stripping TCP header.
> > 4. Layer 7: Payload buffer is delivered to the socket buffer of the listening application.

> [!QUESTION] Q4: What is MTU and what occurs when a packet exceeds the link MTU?
> > [!ANSWER]
> > Maximum Transmission Unit (MTU) is the largest size of a Layer 3 packet that can be transmitted over a Layer 2 frame without fragmentation (standard Ethernet MTU is 1500 bytes). If a packet exceeds link MTU:
> > * If **DF (Don't Fragment) = 0**, the router fragments the packet into multiple smaller packets.
> > * If **DF = 1**, the router drops the packet and transmits an `ICMP Type 3 Code 4` error back to the sender.

> [!QUESTION] Q5: What is MSS (Maximum Segment Size)?
> > [!ANSWER]
> > MSS is the maximum payload data in bytes that a host can receive in a single unfragmented TCP segment.
> > $$\text{MSS} = \text{MTU} - (\text{IP Header [20B]} + \text{TCP Header [20B]}) = 1500 - 40 = 1460\text{ bytes}$$

---

## Section 2: IP Addressing & Subnetting

> [!QUESTION] Q6: Why do we subtract 2 when calculating usable hosts in an IPv4 subnet?
> > [!ANSWER]
> > Because two addresses are reserved by the IP standard:
> > 1. **Network ID:** All host bits set to `0`. Identifies the network segment itself.
> > 2. **Directed Broadcast Address:** All host bits set to `1`. Targets every host on the subnet simultaneously.

> [!QUESTION] Q7: What is CIDR and why was it introduced?
> > [!ANSWER]
> > Classless Inter-Domain Routing (RFC 1519, 1993) replaced rigid Class A, B, and C address assignments with arbitrary prefix lengths (`/N`). It prevented IPv4 exhaustion by eliminating wasted address blocks and allowed route aggregation (supernetting) to shrink global routing tables.

> [!QUESTION] Q8: What are the three RFC 1918 private address ranges?
> > [!ANSWER]
> > 1. `10.0.0.0/8` (`10.0.0.0` - `10.255.255.255`) $\implies 16.7\text{M}$ hosts.
> > 2. `172.16.0.0/12` (`172.16.0.0` - `172.31.255.255`) $\implies 1.04\text{M}$ hosts.
> > 3. `192.168.0.0/16` (`192.168.0.0` - `192.168.255.255`) $\implies 65,534$ hosts.

> [!QUESTION] Q9: What is an APIPA address and what does it indicate?
> > [!ANSWER]
> > Automatic Private IP Addressing (`169.254.0.0/16`, RFC 3927). If a DHCP-enabled client receives no response from any DHCP server, the OS assigns itself a random link-local address in this range. Seeing APIPA indicates **DHCP failure, cable disconnection, or VLAN misconfiguration**.

> [!QUESTION] Q10: How many usable hosts are in a `/28` subnet?
> > [!ANSWER]
> > Host bits $H = 32 - 28 = 4$.  
> > Usable hosts $= 2^4 - 2 = 16 - 2 = \mathbf{14}$.

> [!QUESTION] Q11: What is the purpose of an RFC 3021 `/31` subnet?
> > [!ANSWER]
> > A `/31` mask has 1 host bit ($2^1 = 2$ IPs). RFC 3021 permits using both addresses without a separate Network ID or Broadcast address exclusively on point-to-point router links, saving millions of public IP addresses globally.

> [!QUESTION] Q12: How does IPv6 eliminate the need for broadcast addressing?
> > [!ANSWER]
> > IPv6 replaces broadcast entirely with **Multicast** (`ff00::/8`) and **Anycast**. Broadcast wakes up and consumes CPU cycles on every host on a LAN; multicast allows only interested hosts (subscribed to a specific multicast group) to process the frames at the NIC level.

---

## Section 3: Switches, Routers & LAN Topologies

> [!QUESTION] Q13: What is the fundamental difference between a Collision Domain and a Broadcast Domain?
> > [!ANSWER]
> > * **Collision Domain:** A physical network segment where data packets can collide if sent simultaneously. Each switch port is an isolated collision domain.
> > * **Broadcast Domain:** A logical network area where any broadcast frame sent is received by all nodes. A switch is one broadcast domain; a router breaks broadcast domains at each port.

> [!QUESTION] Q14: How does a Layer 2 switch learn MAC addresses?
> > [!ANSWER]
> > A switch passively inspects the **Source MAC address** of every incoming frame. It pairs that MAC address with the ingress physical port in its CAM (Content Addressable Memory) table along with a timestamp (aging timer ~300s).

> [!QUESTION] Q15: What is an "Unknown Unicast Flood"?
> > [!ANSWER]
> > When a switch receives a frame destined for a unicast MAC address that is **not present in its CAM table**, it floods the frame out every active port except the port it arrived on, ensuring the target receives it.

> [!QUESTION] Q16: What is a Broadcast Storm and how does STP prevent it?
> > [!ANSWER]
> > A broadcast storm occurs when redundant switch links create a Layer 2 loop. Because Ethernet frames have no TTL, broadcast frames circulate endlessly, multiplying exponentially and consuming 100% of bandwidth. STP (Spanning Tree Protocol) dynamically elects a Root Bridge and places redundant backup ports in a blocking/discarding state until a primary link fails.

> [!QUESTION] Q17: What is the difference between an Access Port and a Trunk Port?
> > [!ANSWER]
> > * **Access Port:** Belongs to a single VLAN and carries untagged standard Ethernet frames (connects to end-user PCs, printers).
> > * **Trunk Port:** Carries traffic for multiple VLANs simultaneously by inserting an IEEE 802.1Q 4-byte tag into each frame (connects switch to switch or switch to router).

> [!QUESTION] Q18: What is Longest Prefix Match (LPM)?
> > [!ANSWER]
> > The routing algorithm rule where a router compares a destination IP against its routing table and selects the route with the most specific mask (highest CIDR prefix). A `/32` host route takes precedence over a `/24` route, which takes precedence over the `0.0.0.0/0` default route.

---

## Section 4: Protocols (DNS, DHCP, ARP, HTTP/S, TCP/UDP)

> [!QUESTION] Q19: Explain the 4 steps of the DHCP DORA process.
> > [!ANSWER]
> > 1. **Discover:** Client broadcasts `DHCPDISCOVER` to `255.255.255.255:67` seeking a server.
> > 2. **Offer:** Server sends `DHCPOFFER` proposing an available IP, mask, lease, and gateway.
> > 3. **Request:** Client broadcasts `DHCPREQUEST` confirming acceptance of that specific server's offer.
> > 4. **Acknowledge:** Server unicasts `DHCPACK` finalizing the lease.

> [!QUESTION] Q20: What is ARP Cache Poisoning (Spoofing) and how is it mitigated?
> > [!ANSWER]
> > An attack where a malicious actor sends unsolicited Gratuitous ARP replies linking their MAC address to the default gateway's IP. Hosts update their ARP tables and send all outbound traffic to the attacker (Man-in-the-Middle). Mitigated using **Dynamic ARP Inspection (DAI)** on enterprise switches and 802.1X port security.

> [!QUESTION] Q21: What is the difference between Recursive and Iterative DNS queries?
> > [!ANSWER]
> > * **Recursive:** Client asks its configured resolver: "Find the answer for me and return the final IP or an error."
> > * **Iterative:** The resolver asks Root, TLD, and Authoritative servers: "Give me the IP or the next best referral nameserver."

> [!QUESTION] Q22: What happens during a TCP 3-way handshake?
> > [!ANSWER]
> > 1. Client sends `SYN` with Initial Sequence Number (ISN) $X$.
> > 2. Server responds with `SYN-ACK` with ISN $Y$ and `Ack = X + 1`.
> > 3. Client responds with `ACK` with `Ack = Y + 1`. Connection enters `ESTABLISHED` state.

> [!QUESTION] Q23: Why does TCP use a `TIME_WAIT` state during connection termination?
> > [!ANSWER]
> > To ensure the remote endpoint received the final ACK. If the ACK was lost, the remote peer retransmits FIN, which can be re-acknowledged. It also allows delayed, duplicated packets in the network to expire ($2 \times \text{MSL}$) so they don't corrupt a new connection reusing the same 5-tuple.

> [!QUESTION] Q24: What is the main operational difference between HTTP/2 and HTTP/3?
> > [!ANSWER]
> > HTTP/2 multiplexes streams over a single **TCP** connection, making it susceptible to TCP Head-of-Line blocking (a single dropped packet stalls all streams). HTTP/3 uses **QUIC over UDP**, which implements independent packet loss recovery per stream with zero HoL blocking across streams.

---

## Section 5: Ports & Security

> [!QUESTION] Q25: What is a 5-Tuple and why is it critical?
> > [!ANSWER]
> > A 5-Tuple consists of: `(Source IP, Source Port, Destination IP, Destination Port, Protocol)`. It uniquely identifies every bidirectional socket communication on an operating system and in stateful firewall state tables.

> [!QUESTION] Q26: What is the difference between a Stateless Firewall and a Stateful Packet Inspection (SPI) Firewall?
> > [!ANSWER]
> > A stateless firewall filters packets individually based strictly on header values without tracking connection history, requiring open return rules. A stateful firewall tracks connection handshakes and sequence numbers in a State Table, automatically permitting return packets for valid established flows.

> [!QUESTION] Q27: What is the difference between an AWS Security Group and a Network ACL?
> > [!ANSWER]
> > * **Security Group:** Applied at the virtual network interface (ENI); **Stateful** (return traffic automatically allowed); supports only `ALLOW` rules.
> > * **NACL:** Applied at the subnet boundary; **Stateless** (outbound return rules must be explicitly configured); evaluated sequentially; supports both `ALLOW` and `DENY` rules.

> [!QUESTION] Q28: What port does DNS use for zone transfers?
> > [!ANSWER]
> > **TCP Port 53** (standard DNS queries use UDP Port 53).

> [!QUESTION] Q29: What happens when you send a TCP packet to a closed port on a live server?
> > [!ANSWER]
> > The target operating system kernel responds immediately with a TCP packet having the **`RST` (Reset)** flag set, resulting in a "Connection Refused" error.

> [!QUESTION] Q30: What is the security purpose of HSTS (HTTP Strict Transport Security)?
> > [!ANSWER]
> > An HTTP response header (`Strict-Transport-Security`) instructing web browsers that the site must only be accessed using HTTPS, preventing SSL-stripping Man-in-the-Middle attacks.
