<h1 id="networking">Networking</h1>

Interview and engineering reference for software developers.

<h2 id="table-of-contents">Table of Contents</h2>

* [Fundamentals](#fundamentals)
  * [What is networking?](#what-is-networking)
  * [OSI model](#osi-model)
  * [TCP/IP model](#tcp-ip-model)
  * [IP addressing](#ip-addressing)
  * [Subnetting and CIDR](#subnetting-and-cidr)
  * [Private vs public IP](#private-vs-public-ip)
  * [NAT](#nat)
  * [Ports and sockets](#ports-and-sockets)
  * [MAC vs IP](#mac-vs-ip)
* [TCP, UDP, and IP](#tcp-udp-and-ip)
* [HTTP, HTTPS, and TLS](#http-https-and-tls)
  * [SSL/TLS certificates](#ssl-tls-certificates)
* [DNS, CDN, and load balancing](#dns-cdn-and-load-balancing)
* [Proxies and security](#proxies-and-security)
* [Troubleshooting](#troubleshooting)
* [Interview questions](#interview-questions)

<h2 id="fundamentals">Fundamentals</h2>

<h3 id="what-is-networking">What is networking?</h3>

Networking is how devices exchange data over wired or wireless links using agreed protocols (rules for format, ordering, error handling, and addressing).

As a software engineer you typically care about:

- How a browser request reaches a server and returns
- Why latency, packet loss, or DNS failures break apps
- How load balancers, proxies, TLS, and firewalls sit in the path
- How to debug with `ping`, `curl`, `traceroute`, and logs

<h3 id="osi-model">OSI model (7 layers)</h3>

Mnemonic (top to bottom): **All People Seem To Need Data Processing**

| Layer | Name | Unit | Role | Examples |
|-------|------|------|------|----------|
| 7 | Application | Data | APIs and user-facing protocols | HTTP, DNS, SMTP, FTP |
| 6 | Presentation | Data | Encoding, encryption, compression | TLS, JPEG |
| 5 | Session | Data | Sessions between apps | RPC session, TLS resumption |
| 4 | Transport | Segment | End-to-end delivery, ports | TCP, UDP |
| 3 | Network | Packet | Routing between networks | IP, ICMP |
| 2 | Data Link | Frame | Hop-to-hop on LAN | Ethernet, MAC |
| 1 | Physical | Bits | Signals on wire/air | Cable, Wi-Fi |

**Interview tip:** Most backend questions map to **Layer 4 (TCP/UDP)**, **Layer 3 (IP)**, and **Layer 7 (HTTP/DNS)**.

<h3 id="tcp-ip-model">TCP/IP model (4 layers)</h3>

| TCP/IP layer | Maps to OSI | Protocols |
|--------------|-------------|-----------|
| Application | 5–7 | HTTP, DNS, SSH |
| Transport | 4 | TCP, UDP |
| Internet | 3 | IP, ICMP |
| Network access | 1–2 | Ethernet, Wi-Fi |

- **OSI** — teaching/reference model (7 layers).
- **TCP/IP** — what the internet runs on (4 layers).

<h3 id="ip-addressing">IP addressing</h3>

- **IPv4:** 32-bit, dotted decimal (e.g. `192.168.1.10`).
- **IPv6:** 128-bit, hex groups (e.g. `2001:db8::1`).
- **Default gateway:** router IP to reach other networks.

<h3 id="subnetting-and-cidr">Subnetting and CIDR</h3>

**CIDR** writes network size in the address:

- `10.0.0.0/8` → ~16M hosts
- `192.168.1.0/24` → 256 addresses; typically 254 usable hosts
- **Subnet mask** `/24` = `255.255.255.0`

**Quick calc (/24):** network `192.168.1.0`, broadcast `192.168.1.255`, usable `192.168.1.1` – `192.168.1.254`

**Interview:** Usable hosts in `/26`? Host bits = 6 → 64 addresses → **62 usable**.

<h3 id="private-vs-public-ip">Private vs public IP</h3>

| Range | CIDR |
|-------|------|
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

**Public IP:** globally routable (ISP or cloud).

<h3 id="nat">NAT</h3>

**Network Address Translation** maps many private IPs to one or more public IPs.

- Home router: LAN shares one public IP.
- Cloud: SNAT outbound; DNAT / load balancer inbound.
- Side effect: complicates P2P and logging; use IPv6 or port mapping where needed.

<h3 id="ports-and-sockets">Ports and sockets</h3>

- **Port:** 16-bit (0–65535); well-known 0–1023 (HTTP 80, HTTPS 443, SSH 22, DNS 53).
- **Socket:** IP + port + protocol (e.g. TCP).
- **`LISTEN`** / **`ESTABLISHED`** — server waiting / active connection.

<h3 id="mac-vs-ip">MAC address vs IP address</h3>

| | MAC | IP |
|---|-----|-----|
| Layer | 2 | 3 |
| Scope | Same LAN | End-to-end (routed) |
| Used for | Local delivery (ARP) | Routing |

**ARP** resolves IP → MAC on the local subnet.

<h2 id="tcp-udp-and-ip">TCP, UDP, and IP</h2>

### TCP vs UDP

| | TCP | UDP |
|---|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed, ordered, retransmit | Best effort |
| Overhead | Higher | Lower |
| Use cases | HTTP, DB, file transfer | DNS, video/voice, gaming |

**UDP:** latency over perfect delivery. **TCP:** must not lose or reorder data.

### Three-way handshake

```
Client                    Server
  |---- SYN -------------->|
  |<--- SYN-ACK ------------|
  |---- ACK -------------->|
  |     ESTABLISHED        |
```

SYN flood attacks target this phase.

### TCP connection teardown

**Four-way close:** FIN → ACK → FIN → ACK.

**TIME_WAIT:** ~2× MSL wait so late packets don’t confuse a new connection on the same 4-tuple.

### TCP reliability

Sequence numbers, ACKs, retransmission, checksums.

### Flow control vs congestion control

| | Flow control | Congestion control |
|---|--------------|-------------------|
| Problem | Receiver buffer overflow | Network overload |
| Mechanism | Sliding window | cwnd, slow start |

Flow control is receiver-driven; congestion control is network-driven.

### UDP use cases

DNS, DHCP, SNMP, NTP, video/voice (jitter buffers), gaming state sync.

### QUIC and HTTP/3

- **QUIC** over UDP — encryption and streams in user space.
- **HTTP/3** — no TCP head-of-line blocking across streams; faster setup; some networks block UDP.

### ICMP

- **ping** — Echo Request/Reply (reachability, RTT).
- **Destination Unreachable**, **Time Exceeded** (traceroute).

<h2 id="http-https-and-tls">HTTP, HTTPS, and TLS</h2>

### HTTP basics

- Application layer (L7); stateless by default.
- Request: method, path, headers, body. Response: status, headers, body.
- See also: [misc/http.md](../misc/http.md) for methods, idempotency, PUT vs PATCH.

### HTTP versions

| Version | Highlights |
|---------|------------|
| HTTP/1.1 | Keep-alive, Host header, chunked encoding |
| HTTP/2 | Multiplexed streams on one TCP, HPACK |
| HTTP/3 | QUIC (UDP), independent streams |

### HTTPS and TLS

- **HTTPS** = HTTP over **TLS**; port **443** (HTTP **80**).
- Confidentiality, integrity, authentication.

### TLS handshake (simplified)

1. **ClientHello** — versions, ciphers, **SNI** hostname.
2. **ServerHello** — chosen cipher, certificate chain.
3. Key exchange (ECDHE).
4. **Finished** — encrypted app data.

**TLS 1.3:** fewer round trips; 0-RTT resumption (replay risk if misused).

<h3 id="ssl-tls-certificates">SSL/TLS certificates</h3>

**SSL** (Secure Sockets Layer) is deprecated; modern systems use **TLS**. People still say “SSL certificate” for the X.509 file used in HTTPS.

#### SSL vs TLS

| | SSL | TLS |
|---|-----|-----|
| Status | Deprecated (SSL 3.0 and earlier) | Current (TLS 1.2, **1.3** recommended) |
| Used by | Legacy term only | HTTPS, email, VPNs |
| Certificate | Same X.509 format for both | Same X.509 format for both |

#### What is in a certificate?

An **X.509 certificate** binds a **public key** to an identity (usually a DNS name):

| Field | Purpose |
|-------|---------|
| **Subject** | CN (Common Name) or SAN hostnames the cert is valid for |
| **Issuer** | CA that signed the cert |
| **Validity** | `Not Before` / `Not After` dates |
| **Public key** | RSA or ECDSA key for TLS handshake |
| **Signature** | Issuer’s signature over the cert contents |

The server keeps the **private key** secret; the cert (with public key) is sent to clients during the TLS handshake.

#### Chain of trust

Browsers trust **root CAs** preinstalled in the OS/browser store.

```
Root CA (self-signed, in trust store)
  └── Intermediate CA
        └── Leaf certificate (your site: api.example.com)
```

- Clients verify leaf → intermediate → root.
- **Broken chain** (missing intermediate) causes “unable to get local issuer certificate”.
- **Self-signed** certs are not trusted by browsers unless manually added (dev/k8s internal).

#### CSR and key generation

1. Generate **private key** (never share).
2. Create **CSR** (Certificate Signing Request) with CN/SAN and public key.
3. Submit CSR to CA (or sign locally for dev).
4. Receive **leaf certificate**; install with private key on server/LB.

```bash
# Generate private key + CSR
openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr

# View CSR details
openssl req -in server.csr -noout -text
```

#### Types of certificates

| Type | Covers | Typical use |
|------|--------|-------------|
| **Single-domain** | One hostname | `www.example.com` |
| **Wildcard** | `*.example.com` | All subdomains of one level |
| **SAN / multi-domain** | Listed names in Subject Alternative Name | `api.example.com`, `cdn.example.com` on one cert |

**Wildcard limit:** `*.example.com` does **not** match `a.b.example.com`.

#### Let’s Encrypt and ACME

- **Free** automated certs via **ACME** protocol.
- Prove domain control (HTTP-01, DNS-01, TLS-ALPN-01 challenges).
- Short-lived certs (~90 days); renew automatically (certbot, **cert-manager** in Kubernetes).

#### Renewal and expiry

- Expired cert → browser warning / API clients fail TLS handshake.
- Renew **before** expiry; stagger deploys so all nodes get new cert.
- Monitor with alerts (e.g. 30 days before `Not After`).

#### Inspecting certificates

```bash
# From live host (shows chain + dates)
openssl s_client -connect example.com:443 -servername example.com </dev/null 2>/dev/null | openssl x509 -noout -dates -subject -issuer

# Local PEM file
openssl x509 -in cert.pem -noout -dates -text

# Check SAN names
openssl x509 -in cert.pem -noout -ext subjectAltName
```

#### Common certificate errors

| Error | Cause |
|-------|--------|
| **Certificate expired** | Past `Not After`; renew ACME/cert |
| **Hostname mismatch** | Cert CN/SAN doesn’t include requested host (wrong SNI or wrong cert on LB) |
| **Untrusted issuer** | Self-signed, corporate MITM proxy, or incomplete chain |
| **Revoked** | Compromised key; check OCSP/CRL (rare in interviews but good to mention) |

**SNI:** Client sends hostname in ClientHello; server must present the matching cert when multiple sites share one IP.

#### mTLS (mutual TLS)

- **Client** also presents a certificate; server verifies client identity.
- Used in **service meshes** (Istio, Linkerd), B2B APIs, zero-trust internal traffic.
- Requires client cert issuance and rotation like server certs.

#### Where certs live in production

| Layer | Examples |
|-------|----------|
| Load balancer | AWS ALB/ACM, nginx `ssl_certificate` |
| Ingress | Kubernetes Ingress + cert-manager `Certificate` |
| App server | Spring Boot `server.ssl.*`, embedded Tomcat |
| CDN | CloudFront, Cloudflare “edge certificate” |

**TLS termination:** LB holds cert; traffic to backend may be HTTP on private network — add **mTLS** or re-encrypt if policy requires.

#### Interview questions (certificates)

- **What is the difference between a certificate and a private key?** Cert = public identity + key; private key signs/decrypts and must stay secret.
- **Why do you need an intermediate CA?** Protect root key offline; issue from intermediate; rotate intermediates without updating all browsers.
- **Wildcard vs SAN?** Wildcard for many subdomains at one level; SAN for explicit list of unrelated names.
- **How does Let’s Encrypt prove you own the domain?** ACME challenge (HTTP file on host, DNS TXT record, etc.).
- **Browser shows NET::ERR_CERT_AUTHORITY_INVALID?** Untrusted root, missing intermediate, or corporate proxy replacing certs.

### Cookies, CORS, Same-Origin

- **Same-Origin:** scheme + host + port.
- **CORS:** `Access-Control-Allow-Origin`; preflight **OPTIONS**.
- Cookies: `HttpOnly`, `Secure`, `SameSite`.

### WebSockets

HTTP **Upgrade** to full-duplex TCP — chat, live dashboards.

### gRPC over HTTP/2

Binary framing, Protobuf; LB must support HTTP/2 end-to-end.

### Caching headers

| Header | Role |
|--------|------|
| `Cache-Control` | max-age, no-store |
| `ETag` / `If-None-Match` | 304 validation |
| `Vary` | cache key includes headers |

### Status codes (network angle)

| Code | Typical cause |
|------|---------------|
| 502 | LB got bad response from upstream |
| 503 | Overloaded / maintenance |
| 504 | Upstream too slow |
| 499 (nginx) | Client closed early |

<h2 id="dns-cdn-and-load-balancing">DNS, CDN, and load balancing</h2>

### DNS fundamentals

Maps names (`api.example.com`) to IPs. Hierarchical: root → TLD → authoritative. **UDP 53** (TCP for large/zone transfer).

### DNS record types

| Record | Purpose |
|--------|---------|
| **A** / **AAAA** | IPv4 / IPv6 |
| **CNAME** | Alias (no other records at same name) |
| **MX** | Mail |
| **TXT** | SPF, verification |
| **NS** / **SOA** | Zone authority |
| **PTR** | Reverse DNS |

### DNS resolution flow

1. Browser/OS cache → 2. Recursive resolver → 3. root → TLD → authoritative → 4. cache by **TTL**

```bash
dig api.example.com A
dig +trace api.example.com
```

### TTL and caching

Low TTL = faster failover, more queries. High TTL = slower propagation after IP change.

### CDN

Edge caches near users; **origin** on miss. Purge on bad deploys. Respect `Cache-Control` / `Vary`.

### Load balancing

| Type | Layer | Decides by |
|------|-------|------------|
| L4 | TCP/UDP | IP, port |
| L7 | HTTP | URL, headers, host |
| DNS LB | DNS | Multiple A records, geo |

Algorithms: round robin, least connections, weighted, IP hash.

Examples: ALB/NLB, nginx, HAProxy, Envoy, K8s Service.

### Health checks and sticky sessions

- **Active** probe `/health`; **passive** on 5xx.
- **Sticky sessions:** cookie/IP hash — prefer stateless apps + Redis/JWT.

<h2 id="proxies-and-security">Proxies and security</h2>

### Forward vs reverse proxy

| | Forward | Reverse |
|---|---------|---------|
| Sits near | Client | Server |
| Use | Corporate egress, policy | TLS, LB, WAF, hide backends |

Examples: nginx, Traefik, Envoy, ALB. See [micro-services/API-Gateway.md](../micro-services/API-Gateway.md).

### Firewalls

Packet filter, stateful, **WAF** (HTTP attacks), security groups, NACL. **Default deny** inbound.

### VPN and Zero Trust

VPN encrypts tunnel site-to-site or remote access. **Zero Trust:** no trust by network location; mTLS mesh common in K8s.

### DDoS (overview)

Volume (SYN/UDP flood), protocol (slowloris), application (expensive APIs). Mitigate: rate limits, CDN, WAF, scale.

### TLS termination

Decrypt at LB/proxy; backend may use HTTP on private network — use **mTLS** re-encrypt if required.

### Common ports

| Port | Service |
|------|---------|
| 22 | SSH |
| 53 | DNS |
| 80 / 443 | HTTP / HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 27017 | MongoDB |

<h2 id="troubleshooting">Troubleshooting</h2>

### Mindset

1. Reproduce  2. Isolate layer (DNS → TCP → TLS → HTTP → app)  3. Compare environments  4. Change one variable

### Layered checks

```
hostname resolves?  → dig / nslookup
port open?          → nc / curl -v
TLS valid?          → openssl s_client
HTTP OK?            → curl -i
app healthy?        → logs, metrics, DB
```

### Essential commands

```bash
ping example.com
traceroute example.com
ss -tlnp                    # Linux listening ports
nc -zv host 443
openssl s_client -connect host:443 -servername host
curl -v https://api.example.com/health
curl -I https://api.example.com/
dig api.example.com
```

### Connection states

`LISTEN`, `SYN-SENT`, `ESTABLISHED`, `TIME-WAIT` (many = port exhaustion), `CLOSE_WAIT` (app leak).

### Production scenarios

| Symptom | Likely cause |
|---------|----------------|
| Could not resolve host | DNS, typo |
| Connection refused | Nothing listening, firewall |
| Connection timed out | Firewall drop, SG |
| SSL certificate problem | Expired, wrong SNI |
| 502 / 504 | Backend down / slow |
| Works in curl, not browser | CORS, mixed content |

**Kubernetes:**

```bash
kubectl get svc,endpoints
kubectl exec -it pod -- curl -v http://service:8080/health
```

Unix CLI shortcuts: [unix/Unix.md](../unix/Unix.md#networking)

<h2 id="interview-questions">Interview questions</h2>

**Fundamentals**

- **OSI vs TCP/IP?** Seven-layer teaching model vs four-layer internet stack.
- **Type URL in browser?** DNS → TCP/QUIC → TLS → HTTP → render; caching at each step.
- **Hub vs switch vs router?** Hub broadcasts; switch by MAC on LAN; router by IP between networks.

**TCP / UDP**

- **TCP vs UDP?** Reliable ordered stream vs best-effort datagrams.
- **Three-way handshake?** SYN, SYN-ACK, ACK.
- **Why TIME_WAIT?** Avoid stale segments on reused 4-tuple.
- **Head-of-line blocking?** TCP loss blocks all streams; HTTP/3 QUIC streams are independent.
- **Why is TCP slower?** Handshake, ACKs, retransmits, congestion control.

**HTTP / TLS / certificates**

- **HTTP vs HTTPS?** TLS adds encryption, integrity, server auth.
- **HTTP/1.1 vs 2 vs 3?** Keep-alive; multiplex on TCP; QUIC on UDP.
- **SSL vs TLS?** SSL deprecated; TLS 1.2/1.3 is what HTTPS uses today.
- **What is in an SSL certificate?** Public key, identity (CN/SAN), issuer, validity, signature.
- **Chain of trust?** Leaf signed by intermediate signed by root in browser trust store.
- **CSR?** Request to CA containing public key and requested names; signed with private key.
- **Wildcard vs SAN?** `*.example.com` vs explicit hostname list on one cert.
- **SNI?** Client sends hostname so server picks correct cert on shared IP.
- **Cert expired / hostname mismatch?** Renew ACME cert; fix SAN or LB SNI routing.
- **CORS?** Browser blocks cross-origin unless server allows via headers.
- **401 vs 403?** Not authenticated vs not authorized.

**DNS / infrastructure**

- **A vs CNAME?** Name → IP vs name → another name.
- **TTL?** DNS cache lifetime; affects failover speed.
- **L4 vs L7 LB?** IP/port vs HTTP-aware routing.
- **ALB vs NLB?** L7 HTTP vs L4 throughput / TLS passthrough.
- **Reverse proxy vs LB?** Often combined (nginx, ALB).

**Security / ops**

- **Firewall vs WAF?** L3/L4 ports vs HTTP attack patterns.
- **TLS termination?** Decrypt at edge; optional mTLS to backend.
- **SYN flood?** Half-open connection exhaustion; SYN cookies, DDoS protection.
- **Site down — debug order?** DNS → trace → port → curl -v → LB health → logs → deploy.
- **High latency?** DNS, distance, TLS, slow DB, packet loss, no keep-alive.
- **Scale read-heavy API globally?** CDN, geo-DNS, replicas, Redis, stateless tier.
- **Secure microservices?** mTLS mesh, private VPC, no public DB ports.

**System design**

- **CAP and networking?** Partitions happen; choose consistency vs availability under partition.
