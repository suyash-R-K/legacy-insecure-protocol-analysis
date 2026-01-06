# Legacy Insecure Protocol Analysis

A comprehensive security analysis demonstrating vulnerabilities in legacy network protocols using Wireshark packet analysis and MITM techniques.

## Project Overview

This project provides hands-on demonstrations of critical security vulnerabilities present in commonly used but insecure network protocols. Through practical packet capture and analysis, we illustrate why these protocols should be replaced with modern, encrypted alternatives.

## Disclaimer

**This project is for educational and security research purposes only.** All tests were conducted in a controlled, isolated environment on localhost. Unauthorized interception of network traffic or use of these techniques against systems you don't own is illegal and unethical.

## Test 1: FTP (File Transfer Protocol)

### Overview
- **Port**: 21 (control), 20 (data)
- **Vulnerability**: Plaintext credentials and data transmission
- **Status**: Complete
- **Secure Alternative**: SFTP, FTPS

### Findings Summary

| Finding | Severity | Description |
|---------|----------|-------------|
| **Plaintext Credentials** | Critical | Username and password transmitted without encryption |
| **Data Exposure** | Critical | File contents readable by network observers |
| **Information Disclosure** | Medium | Server version and OS type revealed |
| **Session Hijacking** | High | Unencrypted sessions can be intercepted and replayed |

### Attack Demonstration

```
Attacker's Perspective:
1. Start Wireshark on shared network
2. Filter: ftp || tcp.port == 21
3. Wait for victim to login
4. Follow TCP Stream
5. Extract: username + password (plaintext)
6. Gain unauthorized access
```

## Repository Structure

```
legacy-insecure-protocol-analysis/
├── captures/                       # Wireshark packet capture files
│   ├── ftp_plaintext_auth.pcap    # FTP authentication capture
│   └── telnet_full_session.pcap   # Telnet session capture (Test 2)
│
├── screenshots/                    # Documentation images
│   ├── ftp_auth_1.png             # FTP server setup
│   ├── ftp_auth_2.png             # FTP client login
│   ├── ftp_auth_3.png             # Wireshark packet capture
│   └── ftp_auth_4.png             # TCP stream analysis
│
├── app/                            # Additional tools/scripts
│
└── README.md                       # This file
```

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| **Wireshark** | Latest | Network protocol analyzer and packet capture |
| **vsFTPd** | 3.0.5 | FTP server for testing |
| **FTP Client** | Built-in | Command-line FTP client |

## Getting Started

### Prerequisites

```bash
# Arch Linux / Manjaro
sudo pacman -S wireshark-qt vsftpd

# Ubuntu / Debian
sudo apt-get install wireshark vsftpd

# Fedora / RHEL
sudo dnf install wireshark vsftpd
```

### Running Test 1: FTP Analysis

**Step 1: Setup FTP Server**
```bash
# Install and start vsFTPd
sudo pacman -S vsftpd
sudo systemctl enable --now vsftpd

# Verify service is running
sudo systemctl status vsftpd
```

**Step 2: Start Wireshark**
```bash
# Run Wireshark with sudo (or add user to wireshark group)
sudo wireshark

# Apply display filter
ftp || tcp.port == 21
```

**Step 3: Connect FTP Client**
```bash
# Connect to local FTP server
ftp 127.0.0.1

# Login with test credentials
Name: ftpuser
Password: testpass1234
```

**Step 4: Analyze Captured Traffic**
1. Locate the FTP packets in Wireshark
2. Right-click on any FTP packet
3. Select "Follow" → "TCP Stream"
4. Observe plaintext credentials

## Key Evidence

### FTP Authentication Sequence

```
Client → Server: TCP SYN (Connection Request)
Server → Client: TCP SYN-ACK
Client → Server: TCP ACK

Server → Client: 220 (vsFTPd 3.0.5)
Client → Server: USER ftpuser
Server → Client: 331 Please specify the password.
Client → Server: PASS testpass1234
Server → Client: 230 Login successful.
Client → Server: SYST
Server → Client: 215 UNIX Type: L8
```

### What Was Captured

The Wireshark analysis revealed:
- **Server Banner**: `220 (vsFTPd 3.0.5)` - exposes software version
- **Username**: `USER ftpuser` - visible in plaintext
- **Password**: `PASS testpass1234` - completely unencrypted
- **System Info**: `215 UNIX Type: L8` - reveals operating system
- **All Commands**: Every FTP command transmitted without protection

## Secure Alternatives

### Migration Recommendations

| Legacy Protocol | Secure Replacement | Key Benefits |
|----------------|-------------------|--------------|
| **FTP** | **SFTP** (SSH FTP) | SSH encryption, key-based auth, port 22 |
| **FTP** | **FTPS** (FTP+TLS) | TLS encryption, compatible with FTP clients |

### Implementation Example: Migrating to SFTP

```bash
# Install OpenSSH server (if not already installed)
sudo pacman -S openssh

# Start SSH service
sudo systemctl enable --now sshd

# Connect using SFTP instead of FTP
sftp user@hostname

# Or use SCP for simple file transfers
scp file.txt user@hostname:/path/to/destination
```

## Learning Objectives

After completing this test, you will understand:

- How legacy protocols transmit data without encryption
- Practical packet analysis using Wireshark
- The ease of credential theft on unsecured networks
- Why protocol-level security is essential
- How to identify vulnerable services in network traffic
- The importance of migrating to secure alternatives
- Basic network forensics and traffic analysis

## Lab Environment Setup

### Network Topology

```
┌─────────────────┐
│   Test Machine  │
│   127.0.0.1     │
│                 │
│  ┌───────────┐  │
│  │ FTP Server│  │
│  │   :21     │  │
│  └───────────┘  │
│        ↓        │
│  ┌───────────┐  │
│  │ Wireshark │  │
│  │  Capture  │  │
│  └───────────┘  │
│        ↓        │
│  ┌───────────┐  │
│  │FTP Client │  │
│  │    ftp    │  │
│  └───────────┘  │
└─────────────────┘
```

### Safety Considerations

- **Use localhost only** (127.0.0.1) for initial testing
- **Isolated VM** recommended for advanced testing
- **Never test on production networks** without authorization
- **Use test credentials only** - never real passwords

## Risk Assessment

### Severity: CRITICAL

| Risk Factor | Rating | Justification |
|-------------|--------|---------------|
| Confidentiality | Critical | Credentials and data exposed in plaintext |
| Integrity | High | Data can be modified in transit via MITM |
| Availability | Medium | Service can be disrupted through connection hijacking |
| Exploitability | Very High | Simple tools, minimal skill required |

### Business Impact
- **Credential Compromise**: Stolen credentials may be reused for other systems
- **Data Breach**: Sensitive files transmitted via FTP are exposed
- **Compliance Violations**: GDPR, HIPAA, PCI-DSS require encryption in transit
- **Lateral Movement**: Compromised FTP accounts can be pivot points

## Educational Use Cases

- **Cybersecurity Training**: Demonstrate real-world attack vectors
- **Network Security Courses**: Hands-on protocol analysis
- **Penetration Testing Labs**: Understanding cleartext protocols
- **Security Awareness**: Show ease of credential capture
- **CTF Preparation**: Network traffic analysis skills

## Additional Resources

### Documentation
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [RFC 959 - FTP Specification](https://datatracker.ietf.org/doc/html/rfc959)
- [OWASP - Unencrypted Data Transport](https://owasp.org/www-community/vulnerabilities/Unencrypted_Data_Transport)

### Related Projects
- [PacketWhisper](https://github.com/TryCatchHCF/PacketWhisper) - Advanced PCAP analysis
- [NetworkMiner](https://www.netresec.com/?page=NetworkMiner) - Network forensics tool
- [Bettercap](https://www.bettercap.org/) - Network attack and monitoring framework


