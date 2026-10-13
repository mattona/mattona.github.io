---
layout: page
title: Wallet Seed to Ethereum Address
description: Hashing, Key Derivation, and Cryptographic Primitives in Ethereum.
img: assets/img/hmw_2.jpg
importance: 2
category: Statistics
---

The journey from a user-friendly mnemonic phrase to a secure, functional Ethereum address is not handled by a single algorithm. Instead, it relies on a sophisticated pipeline of distinct cryptographic primitives and standards, each serving a highly specific purpose. This project breaks down the Ethereum wallet pipeline, exploring its historical context, mathematical distinctions, and statistical properties.

### 1. Temporal and Historical Context

To understand why modern Ethereum wallets (like MetaMask or Rabby) use this specific combination of technologies, it is helpful to view them in their chronological order of development:

*   **1977 – RSA:** The introduction of practical public-key cryptography. It proved that asymmetric key pairs could securely encrypt data and verify signatures, laying the conceptual groundwork for all modern blockchains.
*   **2001 – SHA-2 / SHA-512:** Standardized by NIST, this family of hash functions established the baseline for secure, collision-resistant data mapping.
*   **2012 – BIP-32:** The introduction of Hierarchical Deterministic (HD) wallets for Bitcoin. Before this, wallets generated random keys that all required separate backups. BIP-32 allowed infinite keys to be derived deterministically from a single master seed.
*   **2013 – BIP-39:** Introduced mnemonic sentences (usually 12 or 24 words). It replaced raw hexadecimal master seeds with human-readable words, drastically improving user experience and backup safety.
*   **2014 – BIP-44:** A logical extension of BIP-32, providing standard derivation paths for multiple accounts and different cryptocurrencies under a single seed.
*   **2015 – SHA-3:** Standardized by NIST. It is based on the Keccak algorithm, introducing a sponge construction that is fundamentally different from the SHA-2 family.
*   **2016 – EIP-55:** Introduced by Vitalik Buterin for Ethereum, adding a mixed-case checksum to hexadecimal addresses to prevent user typos and accidental loss of funds.

---

### 2. The Cryptographic Pipeline

A modern Ethereum wallet operates through a strict sequence of transformations. Each step alters the statistical and structural properties of the data.

<div class="row justify-content-sm-center mt-4 mb-4">
    <div class="col-sm-10">
        <div class="card p-3 z-depth-1 rounded text-center" style="background-color: transparent; border: 1px solid rgba(128,128,128,0.2);">
            <code style="font-size: 1.1em; color: inherit;">
                Mnemonic Phrase<br>
                ↓ <i>BIP-39</i> ↓<br>
                Binary Seed<br>
                ↓ <i>BIP-32 / BIP-44</i> ↓<br>
                Private Key<br>
                ↓ <i>secp256k1</i> ↓<br>
                Public Key<br>
                ↓ <i>Keccak-256</i> ↓<br>
                Raw Ethereum Address<br>
                ↓ <i>EIP-55</i> ↓<br>
                Checksummed Address
            </code>
        </div>
    </div>
</div>

#### Step-by-Step Breakdown

1.  **BIP-39 (Mnemonic to Seed):** Initial entropy (randomness) is generated and represented as a list of words from a standardized dictionary. Through a key stretching function (PBKDF2), these words are deterministically hashed into a 512-bit binary seed.
2.  **BIP-32 & BIP-44 (Key Derivation):** The master seed generates a master private key. BIP-32 allows for the mathematical derivation of "child" keys from this "parent" key. BIP-44 simply dictates the folder structure or "path" for these derivations (e.g., `m/44'/60'/0'/0/0` is the standard path for the first Ethereum account).
3.  **secp256k1 (Private to Public Key):** Using Elliptic Curve Cryptography, the private key undergoes scalar multiplication (as explored in HMWK 1) to generate a corresponding public key. This is a one-way mathematical function retaining an algebraic structure.
4.  **Keccak-256 (Public Key to Address):** The public key is hashed using Keccak-256. The Ethereum address is derived by taking the last 20 bytes (40 hex characters) of this hash output.
5.  **EIP-55 (Encoding the Checksum):** The raw address is hashed again. Depending on the statistical output of this hash (whether specific bits are 1 or 0), alphabetical characters in the raw address are capitalized, providing built-in typo detection.

---

### 3. Concept Distinctions

To fully grasp the architecture, it is necessary to separate overlapping but distinct concepts.

**Key Generation vs. Key Derivation**
*Key Generation* relies on true entropy (randomness). It is the act of creating the initial unpredictability (the mnemonic). *Key Derivation* is entirely deterministic; it uses algorithms (like HMAC-SHA512 in BIP-32) to generate multiple child keys from the parent seed. If the parent seed is known, the derived keys are mathematically guaranteed to be the same every time.

**Public-Key Cryptography vs. Hashing**
*Public-key cryptography* (like secp256k1) is asymmetric and structured. It creates a key pair where one key can encrypt or sign data, and the other can verify it. It preserves an underlying algebraic group law. *Hashing* (like Keccak-256) is a one-way mapping process designed to destroy structural relationships, turning input data of any size into a fixed-size, pseudo-random output. You cannot "verify" a hash algebraically; you can only recompute it to check for equivalence.

**Checksums vs. Encoding**
*Encoding* (like Hexadecimal or Base58) translates binary data into a readable format for human interfaces; it does not add security. A *Checksum* (like EIP-55 or the 11-bit checksum in a BIP-39 phrase) adds redundant data or formatting rules used exclusively for error detection, ensuring that the encoded data hasn't been accidentally altered or mistyped.

---

### 4. Cryptographic Nuances and Design Choices

#### SHA-2 vs. Keccak / SHA-3
Though both belong to the family of cryptographic hash functions, their underlying architectures differ. SHA-2 relies on the Merkle-Damgård construction, whereas Keccak introduced the Sponge construction, offering better resistance to certain types of attacks. 
Interestingly, **Ethereum uses Keccak-256, not the standardized SHA3-256.** When NIST finalized the SHA-3 standard in 2015, they changed the padding parameters (domain separation) of the original Keccak submission. Because Ethereum's development had already started using the original Keccak design, it retained the original padding, making Ethereum's Keccak-256 output entirely distinct from NIST's SHA3-256 for the exact same input.

#### Why a Pipeline Instead of a Single Algorithm?
Security systems favor a separation of concerns. Using one algorithm for everything creates systemic vulnerabilities. The Ethereum pipeline delegates specific tasks to the tools best suited for them:
*   **BIP-39/32/44** handle *user experience and management*. They solve the human problem of backing up and organizing infinite keys using a single piece of paper.
*   **secp256k1** handles *mathematical proofs*. It provides the asymmetric relationship required to securely sign transactions without revealing the private key.
*   **Keccak-256** handles *standardization and compression*. It ensures that varying public keys are compressed into uniform, fixed-length identifiers (addresses) without predictable collisions.
*   **EIP-55** handles *error detection*. It acts as a safety net against human error at the UI level.

By layering these specific primitives, the system achieves human readability, hierarchical organization, robust asymmetric security, and error prevention simultaneously.
