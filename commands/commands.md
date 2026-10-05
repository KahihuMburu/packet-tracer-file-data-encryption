
# Commands — File & Data Encryption Lab

# Part 1 = Mary's FTP Credentials

# Tool

OpenSSL

# Encryption

AES-256-CBC

# Key Derivation

PBKDF2

# Encoding

Base64

# Command

```bash
echo '<encrypted-data>' | openssl aes-256-cbc -pbkdf2 -a -d

# Part 2 — Upload Confidential Data

# File

clientinfo.enc

# FTP Server

10.0.3.30

# Commands

```text
ftp 10.0.3.30
dir
put clientinfo.enc
dir
quit

# 11. PART 3 — RECOVER BOB'S FTP CREDENTIALS

Go to:

```text
Laptop BR-2
→ Desktop
→ Text Editor

# Part 3 — Recover Bob's FTP Credentials

# Source

bobftplogin.txt

# Tool

OpenSSL

# Encryption

AES-256-CBC

# Key Derivation

PBKDF2

# Command

```bash
echo '<encrypted-data>' | openssl aes-256-cbc -pbkdf2 -a -d


# Part 4 — Download Confidential Data

# User

Bob

# FTP Server

10.0.3.30

# Commands

```text
ftp 10.0.3.30
dir
get clientinfo.enc
quit
dir
