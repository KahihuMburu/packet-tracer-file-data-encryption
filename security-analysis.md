# Security Analysis — File & Data Encryption Lab

# Executive Summary

The lab demonstrates an encrypted-file workflow using OpenSSL and traditional FTP.

The main security weakness identified is that **the confidentiality of the file does not protect the FTP session itself**.

The system therefore has different security properties at different stages of the data lifecycle.



## Finding 01 — Cleartext FTP Authentication

**Severity:** High

**Issue:** Traditional FTP does not encrypt authentication traffic.

**Potential impact:** An attacker monitoring the network could capture usernames and passwords.

**Recommendation:** Replace FTP with SFTP, FTPS, or another appropriately secured transfer mechanism.



## Finding 02 — Weak Key Management

**Severity:** High

**Issue:** The security of encrypted credential files depends on the secrecy and strength of the decryption password.

**Potential impact:** Exposure of the decryption password could allow recovery of protected credentials.

**Recommendation:** Use strong, unique secrets and dedicated secrets-management solutions.



## Finding 03 — Lack of Authenticated Integrity

**Severity:** Medium

**Issue:** AES-CBC provides confidentiality but does not inherently provide authenticated integrity.

**Potential impact:** Unauthorized modification of ciphertext may not be reliably detected by encryption alone.

**Recommendation:** Use an authenticated encryption construction such as AES-GCM where appropriate.



# Finding 04 — Credential Storage

**Severity:** Medium

**Issue:** Authentication credentials are stored in files, even though the files are encrypted.

**Potential impact:** Compromise of the encrypted files and associated passwords could expose credentials.

**Recommendation:** Use centralized identity and secrets-management mechanisms.


# Security Principles

# Confidentiality

The encrypted customer file prevents immediate disclosure of its contents.

# Authentication

FTP credentials control access to the server.

# Integrity

The AES-CBC implementation used in the lab does not independently provide authenticated integrity.

# Availability

The lab does not extensively test availability controls.

# Key Management

Protection of encryption keys/passwords is essential to the overall security of the system.


# Recommended Secure Architecture

Instead of:

```text
Client → FTP → Server
```

a production architecture should use something closer to:

```text
Client
  |
  | Encrypted transport
  v
SFTP / FTPS / HTTPS
  |
  v
Access-controlled server
  |
  v
Encrypted storage
  |
  v
Managed encryption keys
```

Additional controls should include:

* MFA
* Least privilege
* Network segmentation
* Centralized logging
* Monitoring
* Strong password policies
* Key rotation
* Integrity verification


# Final Assessment

The lab successfully demonstrates encryption and decryption but also highlights an important security principle:

> Protecting the data alone is insufficient; the credentials, transport mechanism, keys, and integrity of the entire workflow must also be protected.
