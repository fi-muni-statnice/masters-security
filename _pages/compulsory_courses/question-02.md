---
title: Cryptosystems – Theory
author: John Doe
category: Compulsory Courses
layout: post
mermaid: true
---

Principles of symmetric block cipher algorithms (Feistel ciphers, DES, AES) and asymmetric algorithms (RSA, Diffie-Hellman, DSA/ElGamal). Factorization and primality testing. Principles of hash function construction. Elliptic curve cryptosystems. (IA174, PV079)

$\def\bin{\\{0, 1\\}}
\def\xor{\oplus}$

# Symmetric Block Cipher Algorithms

## Feistel Ciphers
Block ciphers designed as a Feistel network are called **Feistel ciphers**.

An $n$-**round Feistel network** is a composition of $n$ Feistel permutations. Michael Luby and Charles Rackoff proved that if the round function is a cryptographically secure pseudorandom function

### Feistel Permutation
Let $f: \bin^{n} \to \bin^{n}$ be a function. We construct a permutation $\pi: \bin^{2n} \to \bin^{2n}$ as follows:

$$\pi(x, y) := (y, x \xor f(y))$$

It is a permutation because the inverse exists:

$$\pi^{-1}(u, v) = (v \xor f(u), u)$$

$$
\begin{align*}
\pi^{-1}(\pi(x, y)) &= \pi^{-1}(y, x \xor f(y)) \\
                    &= (x \xor f(y) \xor f(y), y) \\
                    &= (x, y).
\end{align*}
$$

The diagram of the Feistel permutation is below:
![feistel-permutation](/masters-security/assets/feistel_permutation.png 'The Feistel permutation')

## DES
DES is a Feistel cipher with the Feistel permutation function $f$ taking $32$-bit inputs and the resulting permutation $\pi$ operating on 64-bit blocks.

The DES encryption algorithm is a 16-round Feistel network where each round uses a different $f: \bin^n \to \bin^{2n}$. In the $i$-th round, the function $f$ is defined as

$$f(x) := F(k_i, x),$$

where $k_i$ is a 48-bit key for the $i$-th round and $F$ is the **DES round function**.

![feistel-permutation](/masters-security/assets/des_round_function.png 'The DES round function')

- The function $E$ expands a 32-bit input to a 48-bit output by rearranging and replicating the input bits.
- The function $P$ (the **mixing permutation**) maps a 32-bit input to a 32-bit output by rearranging the bits of the input.
- The functions $S_1, \dots, S_8$ are called **S-boxes**. Each S-box $S_i$ maps a 6-bit input to a 4-bit output by a lookup table. The DES standard contains the definitions for these S-boxes.

## AES


# Asymmetric Algorithms

## RSA

## Diffie-Hellman

## DSA/ElGamal

# Factorization and Primality Testing

# Principles of Hash Function Construction

# Elliptic Curve Cryptosystems

*[DES]: Data Encryption Standard
*[AES]: Advanced Encryption Standard
*[RSA]: Rivest-Shamir-Adleman
*[DSA]: Digital Signature Algorithm

# References
- [Boneh-Shoup, Chapter 4](https://toc.cryptobook.us/){:target="_blank"}
- [Pseudorandom functions from pseudorandom generators and CPA security](https://intensecrypto.org/public/lec_05_prf-from-prg.html){:target="_blank"}
- [Evaluation of Some Blockcipher Modes of Operation](https://web.cs.ucdavis.edu/~rogaway/papers/modes.pdf){:target="_blank"}