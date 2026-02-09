# Assignment – Group 5

## DNS & SSL/TLS (Networking + Security Foundations)

---

## Objective

By the end of this group, students should be able to:

* Explain **how traffic reaches a domain**
* Explain **why HTTPS exists**
* Explain **how TLS secures data**
* Read and validate **SSL/TLS certificates**
* Understand **DNS hierarchy, caching, and records**
* Debug **basic DNS & HTTPS issues** (interview level)

⚠️ This group is **concept-heavy by design**
⚠️ Practical tasks are **verification-based**, not build-heavy

---

## Prerequisites

* Linux VM with internet access
* `curl`, `openssl`, `dig` (or `nslookup`) available

Verify:

```bash
whoami
curl --version
openssl version
```

---

# 🔹 PART 1: Networking + HTTPS Foundations

---

## Task 1: IP, Port & DNS Resolution (Verification)

### Goal

Understand how a domain becomes an IP.

```bash
# Resolve domain to IP
ping -c 1 google.com
```

👉 Confirms:

* DNS resolution works
* Domain → IP mapping

```bash
# Show which IP curl connects to
curl -v https://google.com 2>&1 | grep Connected
```

👉 Shows:

* Remote IP
* Port 443 (HTTPS)

---

## Task 2: TCP vs UDP (Concept Task)

### Write (No Commands)

Explain in your own words:

1. Why TCP is used for HTTP/HTTPS
2. Why UDP is not reliable for web traffic
3. One real use case for UDP

---

## Task 3: TCP 3-Way Handshake (Concept + Verify)

### Concept

Explain:

* SYN
* SYN-ACK
* ACK

### Verify

```bash
# Show TCP connections
ss -tan | head
```

👉 Shows TCP states like:

* ESTAB
* SYN-SENT
* TIME-WAIT

---

## Task 4: HTTP vs HTTPS

### Goal

Understand why HTTP is insecure.

```bash
# HTTP request (plain text)
curl -I http://example.com
```

```bash
# HTTPS request (encrypted)
curl -I https://example.com
```

### Write

Explain:

* What attackers can see in HTTP
* Why HTTPS fixes this

---

# 🔹 PART 2: TLS / SSL Deep Understanding

---

## Task 5: TLS Encryption & Handshake (Concept)

### Write (Mandatory)

Explain:

1. What TLS protects (CIA triad)
2. Why symmetric encryption is used after handshake
3. Role of public & private keys

---

## Task 6: TLS Handshake Version Check (Practical)

```bash
# Check TLS handshake details
openssl s_client -connect google.com:443
```

👉 Observe:

* Protocol version
* Cipher suite
* Certificate chain

---

## Task 7: TLS 1.2 vs TLS 1.3 (Concept)

### Write

Explain:

* Why TLS 1.3 is faster
* What was removed in TLS 1.3
* Why fewer round trips matter

---

## Task 8: ECDHE (Concept)

### Write

Explain:

* What ECDHE stands for
* Why it provides **Perfect Forward Secrecy**
* What happens if server private key leaks

---

## Task 9: SSL/TLS Certificates (Inspection)

```bash
# Fetch and display certificate
openssl s_client -connect google.com:443 | openssl x509 -noout -text
```

👉 Identify:

* Issuer
* Subject
* Validity dates
* Public key algorithm

---

## Task 10: What’s Inside a Certificate (Written)

List and explain:

* CN / SAN
* Issuer
* Validity
* Public Key
* Signature

---

## Task 11: Certificate Expiry Check

```bash
# Check certificate expiry date
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
```

👉 Shows `notBefore` and `notAfter`

---

## Task 12: Auto-Renewal Concept (Let’s Encrypt)

### Write

Explain:

* Why manual cert renewal is dangerous
* How auto-renewal works at high level
* What could break auto-renewal

---

## Task 13: Domain Ownership Verification

### Write

Explain:

* DNS-01 challenge
* HTTP-01 challenge
* Which is preferred and why

---

# 🔹 PART 3: DNS Deep Dive

---

## Task 14: DNS Resolution Basics

```bash
# Resolve domain using dig
dig google.com
```

👉 Identify:

* Answer section
* TTL
* Record type

---

## Task 15: /etc/hosts Override

```bash
# View hosts file
cat /etc/hosts
```

### Write

Explain:

* Why `/etc/hosts` exists
* When it is useful
* Why it does not scale

---

## Task 16: DNS Hierarchy (Concept)

### Draw or Write

Explain resolution order:

1. Recursive Resolver
2. Root DNS
3. TLD DNS
4. Authoritative DNS

---

## Task 17: DNS Caching & TTL

```bash
# Check TTL in DNS response
dig google.com | grep TTL
```

### Write

Explain:

* What TTL means
* Why low TTL can be dangerous
* Why high TTL can be dangerous

---

## Task 18: DNS Record Types (Verification)

```bash
# A record
dig google.com A
```

```bash
# AAAA record
dig google.com AAAA
```

```bash
# MX record
dig google.com MX
```

```bash
# TXT record
dig google.com TXT
```

```bash
# NS record
dig google.com NS
```

---


