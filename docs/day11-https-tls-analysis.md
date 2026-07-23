# Day 11 – HTTPS and TLS Analysis with Wireshark

## Objective

The objective of this lab was to understand how HTTPS protects web traffic using Transport Layer Security (TLS). I compared unencrypted HTTP traffic against encrypted HTTPS traffic using Wireshark, examined the TLS handshake, and inspected a website's digital certificate.

---

# Environment

- **Host OS:** Windows 11
- **Virtual Machine:** Ubuntu
- **Packet Analyzer:** Wireshark
- **Web Browser:** Microsoft Edge
- **Local HTTP Server:**
  ```bash
  python3 -m http.server 8000
  ```
- **Test Websites:**
  - http://192.168.64.4:8000
  - https://example.com

---

# Part 1 – Capture HTTPS Traffic

A new packet capture was started in Wireshark before visiting **https://example.com**.

The following display filter was applied:

```text
tls
```

The capture showed multiple TLS packets, including:

- Client Hello
- Server Hello
- Change Cipher Spec
- Application Data

This demonstrated that HTTPS first establishes a secure encrypted session before webpage data is exchanged.

---

# Part 2 – Inspect the TLS Handshake

The **Client Hello** packet was expanded in Wireshark.

Several important fields were examined, including:

- TLS Version
- Random Value
- Cipher Suites
- Supported Versions
- Supported Groups
- Signature Algorithms
- Key Share
- Application Layer Protocol Negotiation (ALPN)
- Server Name Indication (SNI)

The Client Hello advertises the encryption algorithms and capabilities supported by the client, allowing the server to negotiate a secure connection.

---

# Part 3 – Follow the TLS Stream

The TCP stream for the TLS connection was inspected.

Unlike HTTP, the stream did **not** reveal readable webpage content.

Only encrypted binary data and TLS records were visible.

This demonstrates that HTTPS encrypts application data before transmission across the network.

---

# Part 4 – Compare HTTP and HTTPS

The local Python HTTP server was accessed:

```text
http://192.168.64.4:8000
```

Using Wireshark's **Follow HTTP Stream**, the complete HTTP request and response could be read in plaintext.

Visible information included:

- HTTP GET request
- Host header
- User-Agent
- HTTP response headers
- HTML source code
- Directory listing

The same process was repeated with:

```text
https://example.com
```

This time, only encrypted TLS records were visible.

The webpage contents could not be read because they were protected by encryption.

This comparison clearly demonstrated the primary difference between HTTP and HTTPS.

| HTTP | HTTPS |
|------|-------|
| Plaintext | Encrypted |
| Readable by packet capture | Not readable |
| No confidentiality | Confidential communication |

---

# Part 5 – Examine TLS Application Data

Application Data packets were selected in Wireshark.

Although packet sizes, source and destination addresses, and timing information remained visible, the payload itself appeared as encrypted binary data.

This shows that encryption protects the application data while still allowing network devices to deliver the packets.

---

# Part 6 – Explore the Client Hello Packet

The Client Hello packet was expanded to inspect its internal structure.

Fields examined included:

- Ethernet Header
- IPv4 Header
- TCP Header
- TLS Record Layer
- Handshake Protocol
- Client Hello
- Cipher Suites
- Compression Methods
- Supported Groups
- Key Share
- Signature Algorithms
- Supported Versions
- ALPN
- Server Name Indication (SNI)
- Pre-Shared Key Extension

This exercise provided a detailed view of the information exchanged before encryption begins.

---

# Part 7 – Inspect the Website Certificate

The TLS certificate for **example.com** was inspected using the browser.

Information observed included:

- Common Name (CN)
- Certificate Authority (Issuer)
- Validity Period
- SHA-256 Fingerprint
- Public Key

The certificate allows the browser to verify the identity of the website before establishing an encrypted session.

If certificate validation fails, the browser warns the user that the connection may not be secure.

---

# Key Concepts Learned

- HTTPS uses TLS to encrypt web traffic.
- TLS begins with a handshake before encrypted communication starts.
- The Client Hello advertises the client's supported encryption capabilities.
- The server selects compatible encryption settings during the handshake.
- HTTP traffic can be fully reconstructed from captured packets.
- HTTPS encrypts webpage contents, preventing packet inspection from revealing sensitive information.
- Digital certificates verify a server's identity using trusted Certificate Authorities.
- Wireshark can inspect TLS metadata even though encrypted application data remains unreadable.

---

# Skills Practiced

- Capturing HTTPS traffic
- Using Wireshark display filters
- Inspecting TLS packets
- Following TCP and HTTP streams
- Comparing HTTP and HTTPS traffic
- Examining TLS handshake messages
- Identifying TLS encryption metadata
- Inspecting digital certificates
- Understanding the role of Certificate Authorities

---

# Conclusion

This lab demonstrated how HTTPS secures web communication through TLS encryption. By comparing HTTP and HTTPS traffic, inspecting the TLS handshake, and examining a website certificate, I gained a practical understanding of how modern web browsers establish secure connections and protect data transmitted over the network.