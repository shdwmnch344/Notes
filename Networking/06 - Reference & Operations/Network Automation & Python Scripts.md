---
tags:
  - networking
  - python
  - automation
  - scanner
  - scripts
aliases:
  - Network Automation Scripts
  - Python Port Scanner
  - Subnet Audit Script
date_created: 2026-10-11
---

# 🐍 Network Automation & Python Engineering Toolkit

> [!ABSTRACT] Production Automation Scripts
> Clean, production-grade Python scripts that network engineers, DevOps professionals, and SREs use for subnet auditing, socket scanning, DNS latency benchmarking, and automated network troubleshooting.
> 
> * **Prerequisites:** Python 3.8+ (Zero external dependencies required; uses pure standard library).

---

## Script 1: Multi-Threaded TCP Port Scanner (`netscan.py`)

A high-performance socket scanner that audits open ports on target hosts with thread pool concurrency:

```python
#!/usr/bin/env python3
"""
High-Speed Multi-Threaded TCP Port Scanner
Usage: python3 netscan.py <target_host> <start_port> <end_port>
"""
import socket
import sys
from concurrent.futures import ThreadPoolExecutor
from datetime import datetime

COMMON_PORTS = {
    21: "FTP", 22: "SSH", 23: "Telnet", 25: "SMTP", 53: "DNS",
    80: "HTTP", 110: "POP3", 123: "NTP", 143: "IMAP", 443: "HTTPS",
    445: "SMB", 3306: "MySQL", 3389: "RDP", 5432: "PostgreSQL",
    6379: "Redis", 8080: "HTTP-Alt", 8443: "HTTPS-Alt"
}

def scan_port(host, port, timeout=1.0):
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.settimeout(timeout)
            result = s.connect_ex((host, port))
            if result == 0:
                service = COMMON_PORTS.get(port, "Unknown Service")
                print(f"  [+] Port {port:<5} OPEN  ({service})")
                return port
    except Exception:
        pass
    return None

def main():
    target = sys.argv[1] if len(sys.argv) > 1 else "127.0.0.1"
    start_port = int(sys.argv[2]) if len(sys.argv) > 2 else 20
    end_port = int(sys.argv[3]) if len(sys.argv) > 3 else 1024

    print("=" * 60)
    print(f"Starting Scan on: {target} (Ports {start_port} - {end_port})")
    print(f"Time Started:     {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    print("=" * 60)

    open_ports = []
    with ThreadPoolExecutor(max_workers=50) as executor:
        futures = [executor.submit(scan_port, target, port) for port in range(start_port, end_port + 1)]
        for f in futures:
            res = f.result()
            if res:
                open_ports.append(res)

    print("=" * 60)
    print(f"Scan Complete. Found {len(open_ports)} open ports.")
    print("=" * 60)

if __name__ == "__main__":
    main()
```

### How to Run:
```bash
python3 netscan.py scanme.nmap.org 20 100
```

---

## Script 2: Automated Subnet Plan Generator & Host Allocator (`subnet_planner.py`)

Takes any parent CIDR block and a list of department host requirements, automatically calculating the most optimal VLSM design:

```python
#!/usr/bin/env python3
"""
Automated VLSM Subnet Planner
"""
import ipaddress
import math

def calculate_vlsm(parent_cidr, requirements):
    """
    Allocates subnets ordered from largest host requirement to smallest.
    """
    parent = ipaddress.IPv4Network(parent_cidr)
    # Sort requirements descending
    sorted_reqs = sorted(requirements.items(), key=lambda x: x[1], reverse=True)
    
    current_addr = int(parent.network_address)
    allocations = []
    
    print(f"\nParent Network: {parent_cidr} (Total Capacity: {parent.num_addresses} IPs)")
    print("=" * 75)
    print(f"{'Department':<15} | {'Needed':<6} | {'Allocated CIDR':<18} | {'Usable Range':<28}")
    print("-" * 75)

    for dept, needed in sorted_reqs:
        # Minimum host bits: 2^H - 2 >= needed
        host_bits = math.ceil(math.log2(needed + 2))
        prefix = 32 - host_bits
        sub = ipaddress.IPv4Network((current_addr, prefix))
        
        allocations.append((dept, sub))
        usable_range = f"{sub.network_address + 1} - {sub.broadcast_address - 1}"
        print(f"{dept:<15} | {needed:<6} | {str(sub):<18} | {usable_range:<28}")
        
        # Advance current address
        current_addr += sub.num_addresses

    print("=" * 75)

calculate_vlsm("10.100.0.0/22", {
    "Engineering": 250,
    "Marketing": 120,
    "Sales": 50,
    "HR": 20,
    "Point-to-Point Link": 2
})
```

---

## Script 3: DNS Resolver Latency Benchmarker (`dns_bench.py`)

Benchmarks multiple public DNS resolvers (Google, Cloudflare, Quad9, OpenDNS) to find the lowest-latency DNS server for your machine:

```python
#!/usr/bin/env python3
"""
DNS Latency Benchmarker
"""
import socket
import time

RESOLVERS = {
    "Cloudflare": "1.1.1.1",
    "Google": "8.8.8.8",
    "Quad9": "9.9.9.9",
    "OpenDNS": "208.67.222.222"
}

def benchmark_dns(name, ip, test_domain="example.com", iterations=5):
    latencies = []
    for _ in range(iterations):
        start = time.perf_counter()
        try:
            s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
            s.settimeout(2.0)
            # Minimal DNS query packet for 'example.com'
            query = b"\xaa\xbb\x01\x00\x00\x01\x00\x00\x00\x00\x00\x00\x07example\x03com\x00\x00\x01\x00\x01"
            s.sendto(query, (ip, 53))
            data, _ = s.recvfrom(512)
            latencies.append((time.perf_counter() - start) * 1000)
            s.close()
        except Exception:
            latencies.append(999.0)
    avg = sum(latencies) / len(latencies)
    return avg

print(f"{'Provider':<15} | {'Server IP':<16} | {'Avg Latency (ms)':<15}")
print("-" * 50)
for prov, srv in RESOLVERS.items():
    lat = benchmark_dns(prov, srv)
    print(f"{prov:<15} | {srv:<16} | {lat:.2f} ms")
```
