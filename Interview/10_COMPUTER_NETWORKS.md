# 10 — Computer Networks Fundamentals

---

## 10.1 OSI Model (7 Layers) vs TCP/IP (4 Layers)

```
OSI Model (Reference)          TCP/IP Model (Practical)
┌─────────────────────┐
│ 7. Application      │ ──┐
│ 6. Presentation     │   ├── Application Layer (HTTP, FTP, DNS, SMTP)
│ 5. Session          │ ──┘
│ 4. Transport        │ ──── Transport Layer (TCP, UDP)
│ 3. Network          │ ──── Internet Layer (IP, ICMP, ARP)
│ 2. Data Link        │ ──┐
│ 1. Physical         │ ──┘── Network Access Layer (Ethernet, Wi-Fi)
└─────────────────────┘
```

### Mnemonic for OSI: "**A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing" (top to bottom)

### Key Protocols by Layer

| Layer | Protocol | Purpose |
|-------|----------|---------|
| Application | HTTP/HTTPS | Web communication |
| Application | DNS | Domain name → IP address |
| Application | FTP | File transfer |
| Application | SMTP | Email sending |
| Transport | TCP | Reliable, ordered delivery |
| Transport | UDP | Fast, unreliable delivery |
| Network | IP | Packet routing |
| Network | ICMP | Error reporting (ping) |
| Data Link | ARP | IP → MAC address resolution |

---

## 10.2 TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| **Connection** | Connection-oriented (handshake first) | Connectionless |
| **Reliability** | Guaranteed delivery (ACKs, retransmission) | No guarantee |
| **Ordering** | Packets arrive in order | Packets may arrive out of order |
| **Speed** | Slower (overhead for reliability) | Faster (no overhead) |
| **Header size** | 20-60 bytes | 8 bytes |
| **Use cases** | HTTP, email, file transfer, database queries | Video streaming, gaming, DNS lookups, VoIP |

**Q: "Which does your MySQL database use?"**
> "MySQL uses **TCP** — it requires reliable, ordered delivery of query data. You can't have half a SQL result arrive out of order. The connection is established via TCP on port 3306."

---

## 10.3 TCP 3-Way Handshake

```
Client                    Server
  │                          │
  │──── SYN (seq=x) ────▶   │   Step 1: Client initiates connection
  │                          │
  │◀── SYN-ACK (seq=y,  ──  │   Step 2: Server acknowledges + sends own SYN
  │     ack=x+1)             │
  │                          │
  │──── ACK (ack=y+1) ──▶   │   Step 3: Client acknowledges server's SYN
  │                          │
  │    CONNECTION             │
  │    ESTABLISHED           │
```

**Q: "Why 3-way and not 2-way?"**
> "Two-way would only confirm the client can reach the server. The third step confirms the server can also reach the client — establishing bidirectional communication. Without it, the server doesn't know if the client received its response."

---

## 10.4 HTTP vs HTTPS

| Feature | HTTP | HTTPS |
|---------|------|-------|
| **Port** | 80 | 443 |
| **Encryption** | ❌ Plaintext | ✅ TLS/SSL encrypted |
| **Security** | Data can be intercepted | Data is encrypted in transit |
| **Certificate** | Not required | Requires SSL/TLS certificate |
| **SEO impact** | Lower ranking | Google prefers HTTPS |

**HTTP Status Codes to Know:**
| Code | Meaning |
|------|---------|
| 200 | OK — Request successful |
| 201 | Created — Resource created (POST success) |
| 301 | Moved Permanently — URL changed |
| 400 | Bad Request — Client error |
| 401 | Unauthorized — Authentication required |
| 403 | Forbidden — Access denied |
| 404 | Not Found — Resource doesn't exist |
| 500 | Internal Server Error — Server crashed |
| 503 | Service Unavailable — Server overloaded |

---

## 10.5 DNS (Domain Name System)

**What**: Translates human-readable domain names (google.com) to IP addresses (142.250.190.78).

### DNS Lookup Sequence
```
1. Browser cache    → Already resolved recently?
2. OS cache         → Checked /etc/hosts or Windows DNS cache
3. Recursive Resolver (ISP) → ISP's DNS server checks its cache
4. Root Server      → "I don't know google.com, but .com is handled by..."
5. TLD Server (.com) → "google.com is handled by ns1.google.com"
6. Authoritative NS → "google.com = 142.250.190.78" ← ANSWER
```

---

## 10.6 IP Addressing

### IPv4 vs IPv6

| Feature | IPv4 | IPv6 |
|---------|------|------|
| **Format** | 32-bit (e.g., 192.168.1.1) | 128-bit (e.g., 2001:0db8::1) |
| **Address space** | ~4.3 billion | ~340 undecillion |
| **Notation** | Dotted decimal | Hexadecimal with colons |
| **Why needed** | Running out of addresses | Virtually unlimited |

### Private IP Ranges (Memorize)
- `10.0.0.0` to `10.255.255.255` (Class A private)
- `172.16.0.0` to `172.31.255.255` (Class B private)
- `192.168.0.0` to `192.168.255.255` (Class C private)

### Subnet Mask
- Determines which part of IP is network vs host
- `255.255.255.0` (/24) → First 3 octets = network, last octet = host (254 usable addresses)

---

## 10.7 Quick-Reference Network Questions

| Question | Answer |
|----------|--------|
| What is a MAC address? | Physical hardware address of a network interface (48-bit, e.g., AA:BB:CC:DD:EE:FF) |
| What is a firewall? | Security system that monitors/controls incoming/outgoing network traffic based on rules |
| What is NAT? | Network Address Translation — maps private IPs to a single public IP for internet access |
| What is a VPN? | Virtual Private Network — encrypted tunnel over public internet for secure communication |
| What is a proxy server? | Intermediary between client and server — can cache, filter, or anonymize requests |
| What is latency vs bandwidth? | Latency = delay (ms). Bandwidth = maximum data rate (Mbps). "Bandwidth is the width of the pipe; latency is how long water takes to flow through" |
| What is a socket? | Combination of IP address + port number — endpoint for network communication |
| What is DHCP? | Dynamic Host Configuration Protocol — automatically assigns IP addresses to devices on a network |

---

*Next: [11_SDLC_AGILE_GIT.md](./11_SDLC_AGILE_GIT.md) — Software Engineering, Agile & Git*
