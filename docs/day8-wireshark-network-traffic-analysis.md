# Day 8 – Wireshark Network Traffic Analysis

## Objective

Learn how to capture and analyse network traffic using Wireshark. This lab focuses on understanding common networking protocols including ICMP, DNS, TCP and TLS, and observing how devices communicate across a network.

---

## Tasks Completed

### 1. Captured Live Network Traffic

- Started a live packet capture on the Windows virtual machine.
- Generated network activity by:
  - Pinging the Ubuntu VM.
  - Pinging `google.com`.
  - Browsing websites to generate HTTPS traffic.
- Used Wireshark display filters to isolate specific protocols.

**Screenshot:**

![Starting Packet Capture](screenshots/day8/01-starting-packet-capture.png)

---

### 2. Analysed ICMP Traffic

Applied the display filter:

```text
icmp
```

Observed:

- Echo Request packets
- Echo Reply packets

Learned that ICMP is primarily used for connectivity testing and network troubleshooting.

**Screenshot:**

![ICMP Packet Capture](screenshots/day8/02-icmp-packet-capture.png)

---

### 3. Analysed DNS Traffic

Applied the display filter:

```text
dns
```

Observed:

- Standard Query
- Standard Query Response

Example:

- **Hostname requested:** `google.com`
- **Returned IPv4 address:** `142.250.195.142`

Learned that before communicating with a website, the client must first resolve the domain name into an IP address using DNS.

**Screenshots:**

![DNS Query Request](screenshots/day8/03-dns-query-request.png)

![DNS Query Response](screenshots/day8/04-dns-query-response.png)

---

### 4. Analysed TCP Communication

Applied the display filter:

```text
tcp
```

Observed the TCP three-way handshake:

1. SYN
2. SYN-ACK
3. ACK

Also observed TCP connection termination using FIN packets.

Learned that TCP establishes a reliable connection before transmitting application data.

**Screenshots:**

![TCP Three-Way Handshake](screenshots/day8/05-tcp-three-way-handshake.png)

![TCP Connection Termination](screenshots/day8/07-tcp-connection-termination.png)

---

### 5. Analysed TLS Traffic

After the TCP handshake, observed encrypted HTTPS traffic including:

- TLS Client Hello
- TLS Application Data

Learned that although packet headers remain visible, the application payload is encrypted, protecting data transmitted between the client and server.

**Screenshot:**

![TLS Client Hello](screenshots/day8/06-tls-client-hello.png)

---

### 6. Followed a TCP Stream

Used:

```text
Right Click → Follow → TCP Stream
```

Wireshark reconstructed the entire TCP conversation between the client and server.

This demonstrated how individual packets can be reassembled into a complete communication session for troubleshooting and analysis.

**Screenshot:**

![Follow TCP Stream](screenshots/day8/08-follow-tcp-stream.png)

---

## Key Concepts Learned

- Packet capture using Wireshark
- ICMP Echo Requests and Replies
- DNS hostname resolution
- TCP three-way handshake
- TCP connection termination
- TLS encrypted communication
- TCP Stream reconstruction
- Using protocol display filters

---

## Reflection

This lab improved my understanding of how different networking protocols work together during everyday internet communication.

By analysing ICMP, DNS, TCP and TLS traffic, I gained practical experience reading packet captures and following the sequence of events from hostname resolution to encrypted communication. Learning to use Wireshark filters and the **Follow TCP Stream** feature also demonstrated how network traffic can be inspected for troubleshooting and security investigations.

This exercise strengthened my networking fundamentals and provided hands-on experience with one of the most widely used tools in IT support and cybersecurity.

---

## Skills Demonstrated

- Network traffic analysis
- Wireshark packet capture
- ICMP troubleshooting
- DNS analysis
- TCP session analysis
- TLS traffic inspection
- Network protocol analysis
- Troubleshooting methodology