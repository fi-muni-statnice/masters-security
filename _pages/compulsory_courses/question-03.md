---
title: Cryptosystems – Applications
author: John Doe
category: Compulsory Courses
layout: post
---

$\def\xor{\oplus}
\def\mc{\mathcal}
\def\set#1{\\{#1\\}}
\def\abs#1{|#1|}$

Application of symmetric block cipher algorithms – integrity and confidentiality, modes of operation. Application of other symmetric algorithms. Application of hash functions, MAC. Hybrid cryptosystems. (IA174, PV079)

#### Other Sources
- [https://hackmd.io/@rsss-statnice2022/r1rYOKju5](https://hackmd.io/@rsss-statnice2022/r1rYOKju5){:target="_blank"}
- [http://statnice.dqd.cz/mgr-szz:in-bit:3-bit](http://statnice.dqd.cz/mgr-szz:in-bit:3-bit){:target="_blank"}

# Application of Symmetric Block Cipher Algorithms

## The CIA Triad
These are the foundational principles in cybersecurity:
- **Confidentiality**: Confidentiality measures are designed to prevent sensitive information from unauthorized access attempts.
- **Integrity**: Consistency, accuracy and trustworthyness of data must be maintained over its lifecycle. Data must not be changed in transit, and cannot be altered by unauthorized people.
- **Availability**: Information should be consistently and readily accessible for authorized parties.

*[CIA]: Confidentiality, Integrity, Availability

How can we ensure confidentiality and integrity of data?

For confidentiality, using a strong symmetric block cipher with a secret key is the solution. To ensure integrity of data, we use MAC or digital signatures.

## Padding
As symmetric block ciphers work with a fixed block size (128 bits - AES, 512 bits - ChaCha20), for some modes of operation (CBC), we need to divide the plaintext into blocks of the block size. The last block may not be long enough - we need to apply a padding mechanism to the plaintext.

## ANSI X9.23
The block is padded with random bytes, the last byte of the block is set to the number of bytes added.

### PKCS#5/PKCS#7 Compatible Padding
The padding adds $N$ bytes with value $N$. If the length of the original data is a multiple of block size $B$, then an extra block of $B$ bytes with value $B$ is added to the end.

*[PKCS]: Public-Key Cryptography Standards

## Initialization Vectors
Most modes of operation require a unique binary string, called the IV for each encryption operation. The IV must always be non-repeating and for some modes has to be random (CBC). The IV is used to ensure that encryption of the same plaintext under the same key leads to different ciphertexts, which is a necessary condition for CPA security.

*[CPA]: chosen-plaintext attack
*[IV]: initialization vector

## Modes of Operation
### ECB
Each block is encrypted separately.

![ecb-encryption](/masters-security/assets/ECB_encryption.svg 'ECB mode encryption')
![ecb-decryption](/masters-security/assets/ECB_decryption.svg 'ECB mode decryption')

#### Features
- encryption/decryption can be parallelised
- change in 1 ciphertext block causes 1 corrupted plaintext block
- **encrypting the same plaintext produces the same ciphertext**
  - INSECURE WHEN ENCRYPTING MORE THAN ONE BLOCK UNDER THE SAME KEY!

  ![ecb-penguin](/masters-security/assets/ecb_penguin.png 'Using ECB allows patterns to be easily discerned')
- the plaintext block is corrupted unpredictably

### CBC
Each block of plaintext is XORed with the previous ciphertext block before being encrypted. The first block is XORed with the IV.

![cbc-encryption](/masters-security/assets/CBC_encryption.svg 'CBC mode encryption')
![cbc-decryption](/masters-security/assets/CBC_decryption.svg 'CBC mode decryption')

It is important not to provide information about whether the padding of a decrypted message is valid or not when using CBC. It leads to the [padding oracle attack](https://en.wikipedia.org/wiki/Padding_oracle_attack){:target="_blank"} which allows an attacker to decrypt messages without the knowledge of the key.

#### Features
- decryption can be parallelized
- change in 1 ciphertext block causes 2 corrupted plaintext blocks
- the plaintext block is corrupted predictably (changing bits in ciphertext causes changes in the same positions in plaintext) - the second one
- the plaintext block is corrupted unpredictably - the first one
- needs padding

### CFB
Similar to CBC. Each block of plaintext is XORed with the previous ciphertext after **the previous ciphertext** is encrypted. The first ciphertext block is IV encrypted and then XORed with the first plaintext block. Be aware the decryption process uses block cipher **encryption**, as is the case with all the ciphers that XOR after the encryption of the IV/previous ciphertext (CFB, OFB, CTR).

![cfb-encryption](/masters-security/assets/CFB_encryption.svg 'CFB mode encryption')
![cfb-decryption](/masters-security/assets/CFB_decryption.svg 'CFB mode decryption')

#### Features
- decryption can be parallelized
- change in 1 ciphertext block causes 2 corrupted plaintext blocks
- the plaintext block is corrupted predictably (changing bits in ciphertext causes changes in the same positions in plaintext)
- the plaintext block is corrupted unpredictably

### OFB
The plaintext is XORed with the **previous output of the encryption**. That means the encryption only repeatedly encrypts the IV and then XORs it with plaintext to get ciphertext. Encryption and decryption are **exactly the same**. OFB can be implemented by using CBC with a constant string of zeroes as the input.

![ofb-encryption](/masters-security/assets/OFB_encryption.svg 'OFB mode encryption')
![ofb-decryption](/masters-security/assets/OFB_decryption.svg 'OFB mode decryption')

#### Features
- change in 1 ciphertext block causes 1 corrupted plaintext block
- encryption/decryption can be accelerated by precomputation
- the plaintext block is corrupted predictably (changing bits in ciphertext causes changes in the same positions in plaintext)
- block synchronization is required (swapping two blocks while decrypting would cause all remaining plaintext to be unreadable)

### CTR
The encryption is done by encrypting successive values of a "counter", which can be any function producing a non-repeating sequence. The encrypted counter is XORed with the plaintext blocks to produce the ciphertext. Usually, the basic increment-by-one counter is used. The counter needs to be combined with a nonce (IV) - a one-time-used bit sequence. If the nonce is random, it can be combined with the counter using any invertible operation (concatenation, addition, XOR). If it is not, the nonce and counter should be concatenated not to break the CPA security (the nonce-counter pair **cannot** repeat twice for CTR to be CPA secure).

![ctr-encryption](/masters-security/assets/CTR_encryption.svg 'CTR mode encryption')
![ctr-decryption](/masters-security/assets/CTR_decryption.svg 'CTR mode decryption')

#### Features
- encryption can be parallelised
- change in 1 ciphertext block causes 1 corrupted plaintext block
- encryption/decryption can be accelerated by precomputation
- the plaintext block is corrupted predictably (changing bits in ciphertext causes changes in the same positions in plaintext)
- block synchronization is required (swapping two blocks while decrypting would cause all remaining plaintext to be unreadable)

*[ECB]: Electronic codebook
*[CBC]: Cipher block chaining
*[OFB]: Output feedback
*[CTR]: Counter

## Which of These Modes to Use?
None (also, [never roll your own crypto](https://loup-vaillant.fr/articles/rolling-your-own-crypto){:target="_blank"}). Use an AEAD such as ChaCha20-Poly1305 or AES-GCM. AES-CBC was used until TLS 1.2, but all CBC ciphersuites have been removed from TLS 1.3. AES-CTR is theoretically secure, but needs careful implementation to provide strong authenticity and does not provide integrity.

# Application of Other Symmetric Algorithms
In contrast with symmetric **block cipher** algorithms, we also have symmetric **stream ciphers**, such as RC4 (insecure), or ChaCha20 (use this one).

# Application of Hash Functions, MAC
We use hash functions for a plethora of different purposes, such as:
- verifying the integrity of messages and files
  - fast hash function needed
  - MD5, SHA-1 commonly used, but collisions found for both
  - BLAKE2/BLAKE3 modern algorithms
- signature generation and verification
  - ECDSA standardly uses the SHA2 family (SHA-256, SHA-384, SHA-512)
  - Ed25519 uses SHA-512
- password verification
  - the hash function should be slow and memory-hard
  - PBKDF2, scrypt, Argon2
- proof-of-work
  - Bitcoin uses SHA-256, Ethereum used KECCAK-256 (Ethereum does not use PoW anymore)
- file/data identifier
  - Git uses SHA-1 with [plans](https://git-scm.com/docs/hash-function-transition/){:target="_blank"} to move to a stronger hash function

## MAC
A MAC is used for authenticating and integrity-checking a message. The MAC value allows verifiers (who possess the secret key) to detect any changes to the message content.

> Let $M$ be a message space, $H$ a hash/tag space, and $K$ a key space. A MAC over $(K, M, H)$ is a pair $$\text{MAC} = (S, V)$$, where:
> - $S: K \times M \to \Delta(H)$ is a (possibly randomized) **signing algorithm**; and
> - $V: K \times M \times H \to \set{\text{true}, \text{false}}$ is a deterministic **verification algorithm**.
>
> We require that for all $k \in K, m \in M$
>
> $$V(k, m, S(k, m)) = \text{true}$$
>
> holds with probability one.
{: .block-tip}

*[MAC]: message authentication code

MAC values are both generated and verified using the same secret key, in contrast with digital signatures. Therefore, MAC cannot provide non-repudiation, as anyone who can verify a MAC can also generate MAC for other messages.

MAC algorithms can be constructed from other cryptographic primitives, like cryptographic hash functions (HMAC), block cipher algorithms (GCM/CCM), or constructed based on universal hashing (Poly1305).

*[GCM]: Galois/Counter mode
*[CCM]: counter with cipher block chaining message authentication code
*[HMAC]: keyed-hash message authentication code

### HMAC
$$\text{HMAC}(K, m) = H\left((K' \xor opad) \Vert H\left((K^\prime \xor ipad) \Vert m\right)\right)$$

where:
- $H$ is a cryptographic hash function,
- $m$ message to be authenticated,
- $K$ the secret key,
- $K^\prime$ is a block-sized key derived from the secret key either by padding (too short) or by hashing (too long),
- $\Vert$ is concatenation,
- $opad$ is block-sized outer padding, consisting of repeated `0x5c` bytes,
- $ipad$ is block-sized inner padding, consisting of repeated `0x36` bytes

Standard HMAC uses two passes of hash computation. The construction used has been chosen to be secure against length extension attacks, which the hash functions using the Merkle-Damgård construction (MD-5, SHA-1, SHA-2) are susceptible to.

With a sponge-based construction (Keccak), it is enough to create a MAC by prepending the key to the message: $$\text{MAC}_{\text{sponge}}(K, m) = \text{Keccak}(K \Vert m)$$.

### CBC-MAC
CBC-MAC is an example of constructing MAC from a block cipher.

![cbc-mac](/masters-security/assets/cbc_mac.svg 'CBC-MAC construction')

To calculate the CBC-MAC of message $m$, one encrypts $m$ in CBC mode with zero IV and keeps the last block.

CBC-MAC on its own **is not secure** for variable-length messages. It is used to construct a pseudorandom function family which is used as a component of the CCM mode.

### Poly1305
Poly1305 is a universal hash family which also serves as a MAC. It works by evaluating a high-degree polynomial (the point to evaluate at is specified by the key, the polynomial coefficients by the message) over a prime field $\mathbb{F}_{2^{130} - 5}$ (yep, $2^{130} - 5$ is a prime).

> Let $U$ be the set of universe keys, $M$ the number of possible messages to hash. A family of functions $H = \set{h: U \to \set{1, \dots, M}}$ is a **universal hash function family** if for $x, y \in U, x \not= y$,
>
> $$\abs{\set{h \in H: h(x) = h(y)}} \leq \frac{\abs{H}}{M}$$
>
> This means that a probability of collision for two different $x$ and $y$ and a randomly chosen $h \in H$ is at most $\frac{1}{M}$.
{: .block-tip}

Any universal hash family can be used as a one-time MAC to authenticate a single message using a secret key shared between sender and recipient. Key reuse with Poly1305 is **catastrophical** and the adversary can forge additional MAC if the key is reused.

## Authenticated Encryption
*[AE]: authenticated encryption
AE is an encryption scheme which simultaneously assures the data confidentiality and authenticity (non-forgeability). Examples of encryption modes that provide AE are AES-GCM, AES-CCM, or ChaCha20-Poly1305.

### AEAD
It is a variant of AE that allows the message to include plaintext associated data, which are still provided with integrity and authenticity, but not confidentiality.

### Encrypt-then-MAC
![encrypt-then-mac](/masters-security/assets/encrypt_then_mac.png 'EtM approach'){:width="30%"}

The plaintext is encrypted and a MAC is produced on the ciphertext. This is considered to be the strongest approach.

**Features**:
- ciphertext integrity
- plaintext integrity
- MAC does not provide any information about the plaintext

AES-GCM follows the encrypt-then-MAC approach.

### Encrypt-and-MAC
![encrypt-and-mac](/masters-security/assets/encrypt_and_mac.png 'E&M approach'){:width="30%"}

A MAC is produced from the plaintext and the plaintext is encrypted without the MAC. It is used in SSH.

**Features**:
- no integrity on the ciphertext - can be vulnerable to CCA
- plaintext integrity
- may reveal information about the plaintext in the MAC if the same key-plaintext combo was reused (and a static IV/nonce)

### MAC-then-Encrypt
![mac-then-encrypt](/masters-security/assets/mac_then_encrypt.png 'MtE approach'){:width="30%"}

A MAC is produced based on the plaintext, and then the concatenation of plaintext and MAC is encrypted to produce a ciphertext.

This approach can be vulnerable to padding oracle attacks ([Lucky Thirteen](https://en.wikipedia.org/wiki/Lucky_Thirteen_attack){:target="_blank"}).

- no ciphertext integrity
  - we have to decrypt the message first to see whether it is authentic or spoofed
- plaintext integrity
- MAC does not provide any information about the plaintext

*[CCA]: chosen ciphertext attack

AES-CCM is a MAC-then-encrypt composition, but it is proven to be IND-CCA3 secure.

*[IND-CCA3]: (authenticated) Indistinguishability under adaptive chosen ciphertext attack

# Hybrid Cryptosystems
A hybrid cryptosystem combines the convenience of a public-key cryptosystem (no need for a pre-established shared secret) with the efficiency of a symmetric-key cryptosystem.

A hybrid cryptosystem can be constructed using two separate cryptosystems:
- a key encapsulation mechanism (public-key),
- a data encapsulation scheme (symmetric-key).

Most famous examples of protocols using hybrid cryptography are TLS and SSH.

*[SSH]: secure shell

## TLS
Transport Layer Security is a protocol designed to provide communications security over a computer network. TLS builds on the deprecated SSL specification of the HTTPS protocol.

*[SSL]: Secure Sockets Layer
*[HTTPS]: Hypertext Transfer Protocol Secure

All connections between a client and a server secured by TLS will have the following properties:
- the connection has **confidentiality** as it is encrypted with a symmetric-key algorithm
- the identity of the communicating parties can be **authenticated** using public-key cryptography (required for the server, optional for the client)
- the connection has **integrity** so data cannot be altered with during transmission without notice

### How it Works
Go check out [https://tls13.xargs.org/](https://tls13.xargs.org/){:target="_blank"}.

## TLS Modern Cipher Suites
What ciphersuite should we use for TLS? These are the "recommended suites" with the best security available ([Cloudflare](https://developers.cloudflare.com/ssl/reference/cipher-suites/recommendations/){:target="_blank"}). All these suites are forward-secret and support AEAD.

- [`ECDHE-ECDSA-AES128-GCM-SHA256`](https://ciphersuite.info/cs/TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256/){:target="_blank"}
- [`ECDHE-ECDSA-CHACHA20-POLY1305`](https://ciphersuite.info/cs/TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256/){:target="_blank"}
- [`ECDHE-RSA-AES128-GCM-SHA256`](https://ciphersuite.info/cs/TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256/){:target="_blank"}
- [`ECDHE-RSA-CHACHA20-POLY1305`](https://ciphersuite.info/cs/TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256/){:target="_blank"}
- [`ECDHE-ECDSA-AES256-GCM-SHA384`](https://ciphersuite.info/cs/TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384/){:target="_blank"}
- [`ECDHE-RSA-AES256-GCM-SHA384`](https://ciphersuite.info/cs/TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384/){:target="_blank"}

- **Key Exchange**: ECDHE
  - TLS 1.3 supported EC groups are: [secp256r1](https://neuromancer.sk/std/secg/secp256r1){:target="_blank"}, [secp384r1](https://neuromancer.sk/std/secg/secp384r1){:target="_blank"}, [secp521r1](https://neuromancer.sk/std/secg/secp521r1){:target="_blank"}, [x25519](https://neuromancer.sk/std/other/Curve25519){:target="_blank"}, [x448](https://neuromancer.sk/std/other/Ed448-Goldilocks){:target="_blank"}
- **Authentication** (asymmetric cipher): ECDSA, RSA
- **Encryption** (symmetric cipher): AES128-GCM, CHACHA20 + POLY1305, AES256-GCM
- **Hash function**: SHA256, SHA384

*[EC]: elliptic curve
*[AEAD]: authenticated encryption with associated data
*[ECDHE]: Elliptic Curve Diffie-Hellman Ephemeral
*[ECDSA]: Elliptic Curve Digital Signature Algorithm
*[RSA]: Rivest Shamir Adleman algorithm
*[AES128-GCM]: Advanced Encryption Standard with 128bit key in Galois/Counter mode
*[CHACHA20]: ChaCha stream cipher
*[AES256-GCM]: Advanced Encryption Standard with 256bit key in Galois/Counter mode
*[SHA256]: Secure Hash Algorithm 256
*[POLY1305]: Poly1305 message authentication code
*[SHA384]: Secure Hash Algorithm 384
*[TLS]: Transport Layer Security