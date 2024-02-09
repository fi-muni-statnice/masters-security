---
title: Cryptographic Attacks, Smartcards as a Security Mechanism
author: John Doe
category: Information Security
layout: post
---

Attacks on cryptographic systems and protocols. Use of hardware for protection of sensitive/cryptographic data and their operations. Smartcards and their role as a security mechanism. (PA018, PV079, PV204)

#### Other Sources
- [https://hackmd.io/@rsss-statnice2022/rJeSLJ9dq](https://hackmd.io/@rsss-statnice2022/rJeSLJ9dq){:target="_blank"}
- [http://statnice.dqd.cz/mgr-szz:in-bit:6-bit](http://statnice.dqd.cz/mgr-szz:in-bit:6-bit){:target="_blank"}
- [http://statnice.dqd.cz/mgr-szz:in-bit:10-bit](http://statnice.dqd.cz/mgr-szz:in-bit:10-bit){:target="_blank"}

# Attacks on Cryptographic Systems and Protocols
Here are some principles of attacks on cryptographic systems and the potential mitigations:
- **Known-key attack**: attacker gains some keys used previously and uses this info in some malicious fashion.
  - (perfect) forward secrecy: plain-text of the data exchange that occurs during key agreement phase does not reveal the key that was used to encrypt the remainder of the session
- **Replay**: attacker records messages and replays them at a later time.
  - session IDs, timestamping, challenge-response
- **Impersonation**: attacker assumes the identity of one of the legitimate parties in a network.
  - proper authentication, challenge-response
- **Man-in-the-Middle**: attacker interposes himself between two parties and pretends to each to be the other.
  - *Examples*: ARP Spoofing, DNS Spoofing, SSL Stripping
  - digital signatures - DNSSEC, HSTS (only protects after visiting the site once)
- **Interleaving attack**: attacker injects spurious messages into a protocol run to disrupt or subvert it.
  - linking together messages from a protocol run (using chained nonces, for example)
- **Reflection attack**: interleaving attack involving sending information from an ongoing protocol execution back to the originator of the information (in simple terms, forcing the server to solve its own challenge)

*[ARP]: Address Resolution Protocol
*[DNS]: Domain Name System
*[SSL]: Secure Socket Layer
*[DNSSEC]: Domain Name System Security Extensions
*[HSTS]: HTTP Strict Transport Security

## Password Cracking
**Scenario**: dump of database with password hashes, find original password

- brute-force attack (up to 8 characters)
- dictionary attack
- patterns: dictionary + brute-force
- rainbow tables (time-memory trade-off)
- parallelization
- GPU/FPGA/ASIC speedup of cracking

*[GPU]: graphical processing unit
*[FPGA]: field-programmable gate array
*[ASIC]: application-specific integrated circuit

**Mitigations**:
- strong passwords
- use salting to prevent rainbow-table-based attacks
- make the hashing function slower
  - PBKDF2: $n$ iterations of an HMAC function (often SHA-256)
  - scrypt/bcrypt/Argon2: memory-hard functions
  - state-of-the-art (2024): **argon2i**

*[PBKDF2]: Password-Based Key Derivation Function 2

## Side-channel Vulnerabilities
### Power Analysis
1. Simple power analysis
  - direct observation of a single/a few power traces
  - visible operation $\to$ reverse engineering
  - visible patterns $\to$ data dependency
2. Differential power analysis
  - statistical processing of many power traces

### Timing Attack
Execution of crypto algorithm can take **different time** to process input data with some **dependence on secret value**:
- due to performance optimizations (by developer or by compiler)
- due to conditional statements (branching)
- due to cache misses/microarchitectural effects
- due to operations taking different number of CPU cycles

**Measurement techniques**:
- start/stop time
- power/EM trace (very precise if operation can be located)

*[CPU]: central processing unit
*[EM]: electromagnetic

- [Minerva](https://minerva.crocs.fi.muni.cz/){:target="_blank"}
  - time leakage of the nonce bit-length allows extraction of ECDSA private key (using a lattice attack)

### Other Side Channels
- acoustic side channel
- cache-timing
- branch prediction (Meltdown, Spectre)

### Mitigations
1. Do not leak
  - constant-time crypto, bitslicing, ...
2. Shielding - preventing leakage outside
  - acoustic shielding, noisy environment
3. Creating additional noise
  - parallel software load, noisy power consumption circuits
4. Compensating for leakage
  - inverse computation/storage
5. Prevent leaking exploitability
  - key regeneration, masking

### Active Side Channels - Fault Injection
**Semi-invasive attacks**:
- physical manipulation (but the card is still working)
- micro probes placed on the bus
- fault induction
  - liquid nitrogen, power glitches, light flashes, ...
  - bit faults in memory locations
  - protection with shadow variable (every variable has its inverse, both need to be checked)

## Other Attacks
- chosen-plaintext attacks (BREACH)
- attacks agains implementation errors (Heartbleed)

*[BREACH]: Browser Reconnaissance and Exfiltration via Adaptive Compression of Hypertext

## Microarchitectural Attacks
- Spectre, Meltdown

# Use of Hardware for Protection of Sensitive/Cryptographic Data and Their Operations

## FIDO U2F Tokens

## TPM

## HSM

## Intel SGX

*[HSM]: Hardware Security Module

# Smartcards and Their Role as a Security Mechanism