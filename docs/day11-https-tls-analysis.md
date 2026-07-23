# Day 11 – HTTPS and TLS Analysis with Wireshark

## Objective

The objective of this lab was to understand how HTTPS protects web traffic using Transport Layer Security (TLS). I compared unencrypted HTTP traffic against encrypted HTTPS traffic using Wireshark, examined the TLS handshake, and inspected a website's digital certificate.

---

# Environment

- **Host:** MacBook (Apple Silicon), macOS
- **Hypervisor:** UTM
- **Client VM:** Windows 11 ARM64
- **Server VM:** Ubuntu Server
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

![TLS packets captured in Wireshark](../screenshots/day11/part1/01_tls_packet_list.png)

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

![Client Hello packet details](../screenshots/day11/part2/02_tls_client_hello_details.png)

![Cipher suites advertised by the client](../screenshots/day11/part2/03_tls_cipher_suites.png)

![Server Name Indication (SNI) extension](../screenshots/day11/part2/04_tls_server_name_indication_sni.png)

![Server Hello response](../screenshots/day11/part2/05_tls_server_hello.png)

![Server Hello key share](../screenshots/day11/part2/06_tls_server_hello_key_share.png)

![TLS handshake overview](../screenshots/day11/part3/07_tls_handshake_overview.png)

![Server certificate sent during the handshake](../screenshots/day11/part3/08_tls_server_certificate_overview.png)

![Certificate status (OCSP)](../screenshots/day11/part3/9_tls_certificate_status_ocsp.png)

![Server key exchange](../screenshots/day11/part3/10_tls_server_key_exchange.png)

![Certificate issuer details](../screenshots/day11/part3/11_tls_certificate_issuer_details.png)

![Certificate subject details](../screenshots/day11/part3/12_tls_certificate_subject_details.png)

![Certificate subject details, continued](../screenshots/day11/part3/13_tls_certificate_subject_continued.png)

---

# Part 3 – Follow the TLS Stream

The TCP stream for the TLS connection was inspected.

Unlike HTTP, the stream did **not** reveal readable webpage content.

Only encrypted binary data and TLS records were visible.

This demonstrates that HTTPS encrypts application data before transmission across the network.

![Following the TLS stream shows only encrypted data](../screenshots/day11/part4/16_tls_follow_stream_encrypted.png)

![TLS handshake visible within the stream](../screenshots/day11/part5/17_tls_handshake_follow_stream.png)

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

![Following the HTTP stream shows readable plaintext](../screenshots/day11/part4/14_http_follow_stream_plaintext.png)

---

# Part 5 – Examine TLS Application Data

Application Data packets were selected in Wireshark.

Although packet sizes, source and destination addresses, and timing information remained visible, the payload itself appeared as encrypted binary data.

This shows that encryption protects the application data while still allowing network devices to deliver the packets.

![Encrypted TLS application data](../screenshots/day11/part4/15_tls_encrypted_application_data.png)

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

![Client Hello packet selected](../screenshots/day11/part6/18_tls_client_hello_packet.png)

![TCP packet details](../screenshots/day11/part6/19_tls_tcp_packet_details.png)

![TCP flags](../screenshots/day11/part6/20_tls_tcp_flags.png)

![Client Hello structure expanded](../screenshots/day11/part6/21_tls_client_hello_details.png)

![Cipher suites list](../screenshots/day11/part6/22_tls_cipher_suites.png)

![Key share extension](../screenshots/day11/part6/23_tls_key_share_extension.png)

![Signature algorithms extension](../screenshots/day11/part6/24_tls_signature_algorithms.png)

![Encrypted Client Hello extension](../screenshots/day11/part6/25_tls_encrypted_client_hello.png)

![ALPN and SNI extensions](../screenshots/day11/part6/26_tls_alpn_sni_extensions.png)

![Supported versions extension](../screenshots/day11/part6/27_tls_supported_versions.png)

![Session ticket and pre-shared key](../screenshots/day11/part6/28_tls_session_ticket_psk.png)

![PSK binders](../screenshots/day11/part6/29_tls_psk_binders.png)

![Client Hello summary](../screenshots/day11/part6/30_tls_client_hello_summary.png)

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

![Website TLS certificate viewed in the browser](../screenshots/day11/part7/31_https_tls_certificate_details.png)

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