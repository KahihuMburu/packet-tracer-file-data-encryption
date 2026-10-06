# Network Security Lab — File Encryption, FTP & OpenSSL

# Overview

This project demonstrates encryption, credential handling, FTP authentication, file transfer, OpenSSL-based decryption, and security analysis using Cisco Packet Tracer and Kali Linux.

The scenario simulates a branch-office environment in which two employees, Mary and Bob, exchange confidential customer information through an FTP server.

The lab was approached from a security perspective rather than simply as a procedural Packet Tracer exercise.

# Objectives

* Recover encrypted FTP credentials using OpenSSL.
* Authenticate users to an FTP server.
* Upload encrypted confidential information.
* Download encrypted information.
* Decrypt protected files.
* Analyze the security limitations of traditional FTP.
* Examine the relationship between encryption, authentication, key management, and secure transport.


## Environment

| Component              | Details             |
| ---------------------- | ------------------- |
| Network simulator      | Cisco Packet Tracer |
| Security workstation   | Kali Linux          |
| Cryptographic tool     | OpenSSL             |
| File transfer protocol | FTP                 |
| FTP server             | BR Server           |
| Server IP              | 10.0.3.30           |
| Client 1               | Laptop BR-1 — Mary  |
| Client 2               | Laptop BR-2 — Bob   |
| Encryption             | AES-256-CBC         |
| Key derivation         | PBKDF2              |
| Encoding               | Base64              |



# Architecture

The simulated environment consists of two branch-office clients communicating with a central FTP server.

```text
             Branch Office
                  |
        +---------+---------+
        |                   |
   Laptop BR-1          Laptop BR-2
      Mary                  Bob
        |                   |
        +---------+---------+
                  |
              BR Server
             10.0.3.30
                  |
                 FTP
```

The Packet Tracer topology is included in:

```text
PacketTracer-File-Data-Encryption.pkt
```


# Attack / Data Flow

The lab demonstrates the following workflow:

```text
Encrypted Mary Credentials
          |
          | OpenSSL
          v
Mary FTP Credentials
          |
          | FTP authentication
          v
       BR Server
          |
          | Upload
          v
   clientinfo.enc
          |
          | Bob authenticates
          v
       Download
          |
          v
   clientinfo.enc
          |
          | Decryption key
          v
   OpenSSL AES-256-CBC
          |
          v
   clientinfo.txt
          |
          v
 Decrypted Customer Data
```

---

# Part 1 — Mary's Credential Recovery

Mary's FTP credentials were stored in an encrypted text file.

The encrypted credential data was processed using OpenSSL:

```bash
echo '<encrypted-data>' | openssl aes-256-cbc -pbkdf2 -a -d
```

The process used:

* AES-256-CBC
* PBKDF2
* Base64 decoding

The credentials were successfully recovered and used to authenticate to the BR Server.

# Evidence

![Mary encrypted credentials](mary-encrypted-credentials-file.png)

![Mary credentials decrypted](mary-credentials-decrypted.png)

Credentials are intentionally redacted from this public repository.

---

# Part 2 — Uploading Encrypted Customer Information

The file:

```text
clientinfo.enc
```

contained encrypted customer information.

Mary authenticated to the FTP server and uploaded the file.

Commands:

```text
ftp 10.0.3.30
dir
put clientinfo.enc
dir
quit
```

The file was successfully transferred to the BR Server.

# Evidence

![Encrypted customer data](encrypted-client-data.png)

![FTP upload](ftp-upload.png)

---

# Part 3 — Bob's Credential Recovery

Bob's FTP credentials were also stored in encrypted form.

OpenSSL was used to recover the credentials:

```bash
echo '<encrypted-data>' | openssl aes-256-cbc -pbkdf2 -a -d
```

The recovered credentials allowed Bob to authenticate to the FTP server.

# Evidence

![Bob encrypted credentials](Bob-encrypted-credentials.png)

![Bob credentials decrypted](Bob-credentials-decrypted.png)

Credentials are intentionally redacted from the public repository.


# Part 4 — Downloading the Encrypted File

Bob authenticated to the BR Server and downloaded:

```text
clientinfo.enc
```

Commands:

```text
ftp 10.0.3.30
dir
get clientinfo.enc
quit
dir
```

The downloaded file remained encrypted.

# Evidence

![FTP download](07-ftp-download.png)



# Part 5 — Decrypting the Confidential Information

The decryption key was provided to Bob through email.

The encrypted file was then processed using OpenSSL:

```bash
openssl aes-256-cbc -pbkdf2 -a -d \
-in clientinfo.enc \
-out clientinfo.txt
```

After successful decryption, the plaintext customer information became available in:

```text
clientinfo.txt
```

# Evidence

![Decryption key](decryption-key.png)

![Decrypted customer information](decrypted-client-data.png)

Sensitive credentials, keys, and customer information have been redacted from the public portfolio.



# Security Analysis

# Finding 1 — Traditional FTP Does Not Provide Secure Transport

Traditional FTP does not encrypt the FTP control or data channels.

An attacker capable of monitoring the connection could potentially obtain:

* Usernames
* Passwords
* FTP commands
* File names
* Session information

# Risk

Compromised FTP credentials could allow unauthorized access to the server.

# Recommendation

Replace traditional FTP with a secure alternative such as:

* SFTP
* FTPS
* HTTPS-based file transfer


## Finding 2 — Encryption of a File Does Not Secure the Entire Workflow

The customer file was encrypted before transfer.

However, the surrounding FTP session was not encrypted.

This demonstrates:

```text
Encrypted file
       ≠
Secure communication channel
```

Data security must consider both **data at rest** and **data in transit**.



# Finding 3 — Key Management Is Critical

The encrypted files could only be decrypted using the appropriate password/key.

If the decryption key is exposed, the encryption protection can be bypassed by an attacker who possesses the encrypted file.

Keys should therefore be protected independently from the encrypted data.



# Finding 4 — AES-CBC Does Not Provide Authenticated Integrity

AES-256-CBC provides confidentiality but, when used without an authentication mechanism, does not itself guarantee that ciphertext has not been modified.

For modern applications, authenticated encryption such as AES-GCM is generally preferable where appropriate.



# Finding 5 — Credentials Should Not Be Stored in Easily Accessible Files

Even encrypted credential files increase the attack surface.

A production environment should use appropriate secrets-management mechanisms rather than relying on manually encrypted credential files.



# Security Concepts Demonstrated

# Confidentiality

Encryption prevents unauthorized users from immediately reading the protected information.

# Authentication

FTP credentials were required before users could access the server.

# Encryption at Rest

The customer information was stored as encrypted ciphertext.

# Encryption in Transit

The lab demonstrates why protecting the file itself is not enough when the transport protocol remains insecure.

# Key Management

Access to the encrypted data depends on protecting the decryption key.

# Cryptographic Integrity

The lab demonstrates the limitation of using CBC encryption without authenticated integrity.

# Tools Used

```text
Cisco Packet Tracer
Kali Linux
OpenSSL
FTP
Linux CLI
```

# Key Commands

# Decrypt encrypted credential data

```bash
echo '<encrypted-data>' | openssl aes-256-cbc -pbkdf2 -a -d
```

# Connect to FTP server

```text
ftp 10.0.3.30
```

# List FTP files

```text
dir
```

# Upload a file

```text
put clientinfo.enc
```

# Download a file

```text
get clientinfo.enc
```

# Decrypt an encrypted file

```bash
openssl aes-256-cbc -pbkdf2 -a -d \
-in clientinfo.enc \
-out clientinfo.txt
```

# 

This lab reinforced several important security principles:

1. Encryption does not automatically make a communication channel secure.
2. Traditional FTP exposes authentication information.
3. Encryption keys and passwords must be protected independently.
4. Confidentiality and integrity are different security properties.
5. Secure file transfer requires protection of both the data and the transport mechanism.
6. Security analysis should consider the entire data lifecycle rather than a single security control.


# Improvements for a Production Environment

A real-world implementation should replace the lab's simplified architecture with:

```text
User
 |
 | Strong authentication
 v
Secure File Transfer
 |
 | Encrypted transport
 v
Secure Storage
 |
 | Access controls
 v
Encrypted Data
 |
 | Managed keys
 v
Key Management System
```

Potential improvements include:

* SFTP/FTPS instead of FTP
* Strong unique authentication
* MFA where supported
* Centralized secrets management
* Authenticated encryption
* Proper key rotation
* Least-privilege access
* Logging and monitoring
* File integrity verification
* Network segmentation



# Conclusion

This project demonstrated a complete encrypted-data workflow in a simulated enterprise environment, from credential recovery and FTP authentication through encrypted file transfer and final decryption.
The most important lesson was that **security is a system-level property**.
Encrypting a file can protect its contents, but it does not automatically protect credentials, transport channels, metadata, keys, or file integrity.
A secure implementation therefore requires multiple complementary controls rather than relying on encryption alone.
