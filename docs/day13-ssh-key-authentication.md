# Day 13 – SSH Key-Based Authentication

## Objective

Learn how SSH key-based authentication works by generating an SSH key pair on a Windows client, installing the public key on an Ubuntu server, and connecting without entering a password.

---

## Environment

- Host OS: Windows 11
- Virtual Machine: Ubuntu Server 24.04 LTS
- Hypervisor: VirtualBox
- Client: OpenSSH for Windows
- Server: OpenSSH Server
- Network: Internal Virtual Network

---

## What I Learned

- How public-key authentication differs from password authentication
- How SSH uses a public/private key pair
- Why the private key never leaves the client
- How the server verifies ownership of the private key
- How to configure the `authorized_keys` file
- How SSH provides secure authentication without transmitting passwords

---

## Steps Performed

### 1. Verified SSH connectivity

Connected to the Ubuntu server using password authentication.

Example:

```bash
ssh lucas@192.168.64.4
```

---

### 2. Generated an SSH key pair

Generated an Ed25519 key pair on the Windows client.

Command:

```bash
ssh-keygen -t ed25519
```

Accepted the default save location.

Files created:

```
id_ed25519
id_ed25519.pub
```

- `id_ed25519` → Private key (kept secret)
- `id_ed25519.pub` → Public key (copied to the server)

---

### 3. Created the .ssh directory

On the Ubuntu server:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

---

### 4. Created the authorized_keys file

Created the file:

```bash
nano ~/.ssh/authorized_keys
```

Copied the contents of:

```
id_ed25519.pub
```

into the file.

Saved the file.

---

### 5. Applied correct permissions

Configured secure permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

---

### 6. Connected using SSH keys

Connected again:

```bash
ssh lucas@192.168.64.4
```

The server authenticated using the SSH key instead of prompting for a password.

---

## How SSH Key Authentication Works

1. The client connects to the SSH server.
2. The server checks the user's `authorized_keys` file.
3. If a matching public key exists, the server sends a challenge.
4. The client signs the challenge using its private key.
5. The server verifies the signature using the stored public key.
6. If verification succeeds, authentication is granted.

The private key is never transmitted across the network.

---

## Security Benefits

- Passwords are not sent during authentication.
- Resistant to password guessing attacks.
- Supports automated administration securely.
- More secure than password-based authentication.
- Authentication relies on cryptographic key pairs.

---

## Wireshark Observations

Captured the SSH connection after key-based authentication.

Observed:

- SSH Protocol Version exchange
- Key Exchange Init
- Elliptic Curve Diffie-Hellman key exchange
- New Keys packet
- Fully encrypted SSH traffic

After the key exchange, all SSH payload data became encrypted and unreadable in Wireshark.

---

## Screenshots

### SSH Key Authentication Setup

1. `Day13_01_Existing_SSH_Connection.png`
   - Verified existing SSH password-based connection to the Ubuntu server.

2. `Day13_02_SSH_Key_Pair_Generated.png`
   - Generated an Ed25519 SSH public/private key pair using `ssh-keygen`.

3. `Day13_03_Public_Key_Displayed.png`
   - Displayed the generated public key before copying it to the server.

4. `Day13_04_SSH_Directory_Created.png`
   - Created the `~/.ssh` directory on the Ubuntu server.

5. `Day13_05_Authorized_Keys_File_Created.png`
   - Created the `authorized_keys` file and added the public key.

6. `Day13_06_SSH_Permissions_Configured.png`
   - Applied secure permissions to the `.ssh` directory and `authorized_keys` file.

7. `Day13_07_SSH_Service_Restarted.png`
   - Restarted the OpenSSH service and verified it was running correctly.

8. `Day13_08_SSH_Key_Login_Test.png`
   - Successfully logged into the Ubuntu server using SSH key authentication.

9. `Day13_09_SSH_Key_Authentication_Verified.png`
   - Verified key-based authentication by executing a test command over SSH.

---

### Wireshark Analysis

10. `Day13_10_Wireshark_Capture_Started.png`
    - Began capturing network traffic before initiating the SSH connection.

11. `Day13_11_Wireshark_Capture_Before_Filter.png`
    - Displayed the full packet capture before applying any display filters.

12. `Day13_12_SSH_Filter_Applied.png`
    - Applied the `ssh` display filter to isolate SSH traffic.

13. `Day13_13_SSH_Filter_Applied_2.png`
    - Continued viewing filtered SSH packets.

14. `Day13_14_SSH_Filter_Applied_3.png`
    - Displayed the complete filtered SSH communication.

15. `Day13_15_SSH_Protocol_Version_Exchange.png`
    - Examined the SSH protocol version exchange between the client and server.

16. `Day13_16_SSH_Key_Exchange_Init.png`
    - Captured the SSH Key Exchange Initialization packet.

17. `Day13_17_SSH_Key_Exchange_Algorithms.png`
    - Viewed the negotiated cryptographic algorithms supported during key exchange.

18. `Day13_18_ECDH_Key_Exchange_Init.png`
    - Examined the Elliptic Curve Diffie-Hellman (ECDH) Key Exchange Initialization message.

19. `Day13_19_ECDH_Key_Exchange_Reply.png`
    - Captured the server's ECDH Key Exchange Reply containing its ephemeral public key.

20. `Day13_20_ECDH_Key_Exchange_Reply_2.png`
    - Examined the ECDH Key Exchange Reply in greater detail, including the server host key, signature, and transition to encrypted communication.

21. `Day13_21_SSH_New_Keys_Packet.png`
    - Captured the SSH **New Keys** packet, indicating both client and server switched to the newly negotiated encryption keys.

22. `Day13_22_SSH_Encrypted_SSH_Traffic.png`
    - Observed that all subsequent SSH packets were fully encrypted and unreadable in Wireshark.

---

## Key Takeaways

- SSH can authenticate users using cryptographic key pairs instead of passwords.
- The server stores only the public key.
- The client keeps the private key secret.
- The private key never travels across the network.
- After key exchange, SSH encrypts all communication, protecting confidentiality and integrity.