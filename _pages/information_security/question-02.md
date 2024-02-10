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

Three vulnerabilities:
- bounds check bypass (Spectre)
- branch target injection (Spectre)
- rogue data cache load (Meltdown)

They exploit the three major designs in modern processors:
- out-of-order execution
- speculative execution
- caching

The attacks use a cache-timing side channel to obtain the information from the accessed memory location. Fast data access means it was stored in the cache, slower access means it was stored in main memory.

**Cache exploit types**:
- Flush + Reload
  - flush a cache line with the `CLFLUSH` instruction
  - let a program run and access the same memory (the memory page needs to be physically shared in this attack)
  - reload elements from the controlled memory and see whether they were accessed by the other program or not by the timing
- Flush + Flush
  - the reload is not needed, the second flush will be fast if the data were not accessed and slow if the data were accessed
- Prime + Probe
  - the attacker occupies a cache set and measures whenever a victim replaces a line in the cache set by timing side channel
  - does not demand page sharing, coarse grained
  - need to find out the eviction set

### Meltdown
```asm
; rcx = kernel address, rbx = probe array
xor rax, rax
retry:
mov al, byte [rcx]
shl rax, 0xc
jz retry
mov rbx, qword [rbx + rax]
```

1. The content of an attacker-chosen memory location, which is inaccessible to the attacker, is loaded into a register.
2. A transient instruction accesses a cache line based on the secret content of the register.
3. The attacker uses Flush+Reload to determine the accessed cache line and hence the secret stored at the chosen memory location.

### Spectre
```c
if (x < array1_size)
  y = array2[array1[x] * 4096];
```

1. Induce speculative execution, set up the side channel (e.g. the Flush part of the Flush+Reload attack).
2. Speculatively execute the instructions that transfer confidential information from the victim context into the covert chanenl.
3. Recover the sensitive data (Reload phase of the Flush+Reload attack).

- [Spectre, Meltdown explained](https://www.cs.toronto.edu/~arnold/427/20s/427_20S/spectreMeltdown/presentation.pdf){:target="_blank"}

# Use of Hardware for Protection of Sensitive/Cryptographic Data and Their Operations
## Hardware Security Module
An HSM is a physical computing device that safeguards and manages secrets (keys), performs encryption/decryption functions for digital signatures, strong authentication and other cryptographic functions. HSMs may provide tamper evidence (visible signs of tampering) or tamper resistance (makes tampering difficult without making the HSM inoperable).

HSMs are typically certified according to recognized standards such as Common Criteria or FIPS 140 (current version FIPS 140-3).

**FIPS 140-2 Levels**:
- FIPS 140-2 Level 1 the lowest, imposes very limited requirements; loosely, all components must be "production-grade" and various egregious kinds of insecurity must be absent.
- FIPS 140-2 Level 2 adds requirements for physical tamper-evidence and role-based authentication.
- FIPS 140-2 Level 3 adds requirements for physical tamper-resistance (making it difficult for attackers to gain access to sensitive information contained in the module) and identity-based authentication, and for a physical or logical separation between the interfaces by which "critical security parameters" enter and leave the module, and its other interfaces.
- FIPS 140-2 Level 4 makes the physical security requirements more stringent, and requires robustness against environmental attacks.

*[HSM]: Hardware Security Module
*[FIPS]: Federal Information Processing Standard

## FIDO U2F Tokens
FIDO U2F Tokens can replace passwords by a smartcard with an asymmetric keypair, challenge-response protocol and prevent phishing. During authentication, the token verifies:
1. that the user has physical access to the registered device,
2. that the user is active during authentication (need to press a button on the token).

U2F is a challenge-response protocol extended with *phishing and MitM protection*, *application-specific keys*, *device cloning detection* and *device attestation*.

The U2F device device has a private key $k_{priv}$ and the relying party is given the corresponding public key $k_{pub}$. The key pair is generated in the device's **tamper-resistant** execution environment, from where $k_{priv}$ cannot leave.

There are two flows:
1. Registration: includes **optional** device attestation (gives relying parties the possibility to verify token properties, implemented vi an attestation certificate signed by the device vendor)
  ![u2f-registration](/masters-security/assets/u2f_registration_flow.svg 'U2F Registration Flow')

2. Authentication
  - phishing protection: Origin (URI)
  - MitM protection (optional): TLS Channel ID
  - tracking prevention: application-specific keys
  - device cloning detection: counter
  ![u2f-authentication](/masters-security/assets/u2f_auth_flow.svg 'U2F Authentication Flow')

*[FIDO]: Fast Identity Online
*[U2F]: Universal 2nd Factor
*[MitM]: Man-in-the-Middle

### WebAuthn - evolution of U2F protocol
- similar but more complex standard than U2F

## TPM
- Cryptographic smart card connected to device/inside device
  - secure storage, secure crypto
- physical placement:
  1. additional chip on the motherboard (discrete TPM)
  2. incorporated inside CPU (fTPM)
  3. incorporated in a peripheral (integrated TPM, e.g. Ethernet card)
  4. software TPM - development, debugging
- is accessed during boot time
  - measured boot (TPM's PCR registers)
  - Bitlocker encrypted drive keys
- can be accessed later (private key operations)

*[TPM]: Trusted Platform Module
*[PCR]: Platform Configuration Register

### Functionality
1. Measured boot with remote attestation
  - signed log of what was executed on the platform (in the PCR)
2. Storage of keys (disk encryption, private keys, ...)
3. Binding and sealing of data
  - encryption key wrapped by the TPM's public key
4. Platform integrity
  - software will not start if the PCR value is incorrect

### PCR
- measurement cumulatively stored in PCR
  - `measurement = SHA1(next block to execute)`
  - `PCR[i] = SHA1(PCR[i] | new_measurement)`
- PCR **cannot be erased** until reboot

Remote attestation:
1. Take PCR values (inside TPM)
2. Sign them inside TPM by TPM's private key
3. Remote party holds the public key and can verify signature

### Keys
- Endorsement key (EK)
  - generated during manufacturing, permanent
  - remain in TPM during whole chip lifetime
- TPM Storage Root Key (SRK)
  - generated by use after taking ownership
  - new storage root key can be generated after TPM clear
  - used to protect TPM keys created by application
- Various delegate keys
  - separate keys signed/wrapped by EK, SRK, ...
  - application can generate and store own keys

## Intel SGX
- a set of new CPU instructions
- protection against privileged attacker
- application requests private region of code and data - a **security enclave**
  - encrypted enclave is stored in RAM, decrypted only inside CPU
  - access from outside enclave is prevented on CPU level
- proprietary Intel code inside CPU
- vulnerable to side-channels
- depreceated on non-server CPUs

*[SGX]: Software Guard Extension

# Smartcards and Their Role as a Security Mechanism
**Trusted element** is an element (hardware, software, or both) in the system intended to **increase security level** w.r.t. situation without the presence of such an element.

## Types of (Smart) Cards
1. contactless "barcode"
  - fixed identification string (RFID)
2. simple memory cards (magnetic stripe, RFID)
  - small write memory (< 1KB)
3. memory cards with PIN protection
  - memory (< 5KB), simple protection logic
4. cryptographic smart cards
  - support for real cryptographic algorithms
  - Mifare Classic, Mifare DESFire
5. user-programmable cryptographic smart cards
  - JavaCard, .NET card, MULTOS
  - chip manufacturers: NXP, Infineon, Gemalto, ...
6. secure environment (enclave) inside more complex CPUs
  - ARM TrustZone, Intel SGX, ...

*[RFID]: radio frequency identification
*[PIN]: personal identification number

## Smartcards
- 8-32 bit processor @ 5-50 MHz
- persistent memory 32-200+ kB (EEPROM)
- volatile fast RAM, usually much less than 10 kB
- truly random generator
- cryptographic coprocessor (3DES, AES, RSA-2048, ECC, ...)
- many possible forms (ISO 7816 standard): SIM size, USB dongles, Java rings, implants, ...
- contact physical interface + contact-less interface (NFC)

## Protection
- intended for physically unprotected environment
  - FIPS 140-2 Level 4
  - Common Criteria EAL5+/6+
- tamper protection
  - tamper evidence (visible if physically manipulated)
  - tamper resistance (can withstand physical attack)
  - tamper response (e.g. erase keys during tampering)
- protection against side-channel attacks (timing, power, EM)
- periodic tests of TRNG functionality
- approved crypto algorithms and key management
- limited interface, smaller trusted computing base

*[SIM]: Subscriber Identity Module
*[EEPROM]: electrically erasable programmable read-only memory
*[NFC]: Near Field Communication
*[EAL]: Evaluation Assurance Level
*[TRNG]: true random number generator

## Modes of Usage
- carrier of fixed information
- secure carrier
  - secret protected for transport
- encryption/signing device
  - key **never leaves the secure element**
  - attacker must attack the secure element
- verification device
  - device with lower overall security embeds secure element for sensitive tasks via dedicated API
- root of trust (TPM)
  - robust store with integrity
- computational device
  - PC sends input for application running on secure element

## Real-world Examples
- FIDO2 U2F/WebAuthn tokens
- Smart-ID signature system
- cryptocurrency hardware wallets (Trezor One, Ledger Nano S)

## Communication
Smart cards communicates with the smart card reader using APDU messages. The structure of the APDU is defined by ISO/IEC 7816-4.

- APDU is basic logical communication datagram
  - header (5 bytes), up to ~256 bytes of user data
- Header/Data format
  - **CLA**: instruction class
  - **INS**: instruction number
  - **P1**, **P2**: optional data
  - **Lc**: length of incoming data
  - **Data**: user data
  - **Le**: length of the expected output data
- some values of CLA/INS/P1/P2 standardized, for better interoperability

*[APDU]: application protocol data unit

## JavaCard
- cross-platform and cross-vendor applet interoperability
- JavaCard applet is Java-like application (using a restricted subset of Java + cryptographic JavaCard APIs)
  - uploaded to a smart card
  - executed by the JCVM
- access to other applet's methods and attributes prevented

*[JCVM]: JavaCard Virtual Machine
*[API]: Application Programming Interface

## Attacks on Trusted Elements
1. Non-invasive attacks
  - API-level attacks
    - incorrectly designed and implemented application
    - malfunctioning application
    - for example, key shortening
  - communication-level attacks
    - observation and manipulation of communication channel
  - (remote) timing attacks
2. Semi-invasive attacks
  - passive side-channel attacks
    - timing (local) / power / EM / acoustic / cache-usage / error... analysis attacks
  - active side-channel attacks: fault injection
    - power/light/clock glitches...
3. Invasive attacks
  - dismantle chip, microprobes, ...
