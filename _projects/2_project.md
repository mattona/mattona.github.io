---
layout: page
title: Wallet Pipeline & Key Derivation
description: Research on how Ethereum wallets handle hashing, seeds, and cryptographic primitives.
img: assets/img/hmw_2.jpg
importance: 2
category: Statistics
---

For this second assignment, I looked into the actual architecture of an Ethereum wallet to understand how it goes from a basic 12-word seed phrase to a working public address. 

When you set up something like MetaMask or Rabby, you might assume there's just one big encryption algorithm handling everything. In reality, it’s a patchwork of different cryptographic standards and primitives, some of which are decades old, while others were built specifically for modern blockchains.

### Historical Timeline

Putting these technologies in chronological order really helps to see how the system evolved. Blockchain cryptography didn't come out of nowhere; it's mostly built on older math.

For instance, RSA has been around since 1977, which is basically ancient history in computer science. It laid the foundation for public-key cryptography. Later on, in 2001, NIST standardized the SHA-2 family for hashing. But the really interesting stuff started happening when Bitcoin got popular. 

Before 2012, managing wallet keys was a nightmare because you had to back up every single new private key you generated. Then BIP-32 came along, introducing Hierarchical Deterministic (HD) wallets, which meant you could derive a virtually infinite tree of keys from a single master seed. A year later, BIP-39 replaced that raw binary seed with a human-readable list of words (the mnemonic phrase we all use today). By 2014, BIP-44 organized all these derived keys into standard folder paths. 

Around the same time, SHA-3 was officially standardized (2015), and shortly after that, Vitalik Buterin proposed EIP-55 (2016) to add a checksum to Ethereum addresses so people would stop losing funds because of simple copy-paste errors.

---

### The Cryptographic Pipeline

I tried to map out the exact sequence of events that happens under the hood when a wallet processes a seed phrase. Every step alters the data significantly.

<div class="d-flex flex-column align-items-center mt-4 mb-4">
    <div class="card p-2 text-center z-depth-1 w-75" style="background-color: rgba(52, 152, 219, 0.1); border: 1px solid #3498db;">
        <strong>Mnemonic Phrase</strong><br>
        <span style="font-size: 0.85em; color: #888;">Initial entropy generation</span>
    </div>
    <div class="my-2" style="font-size: 0.8em;">↓ <i>BIP-39 (PBKDF2 Hashing)</i> ↓</div>
    
    <div class="card p-2 text-center z-depth-1 w-75" style="background-color: rgba(46, 204, 113, 0.1); border: 1px solid #2ecc71;">
        <strong>Binary Master Seed</strong>
    </div>
    <div class="my-2" style="font-size: 0.8em;">↓ <i>BIP-32 / BIP-44 (Deterministic Derivation)</i> ↓</div>
    
    <div class="card p-2 text-center z-depth-1 w-75" style="background-color: rgba(155, 89, 182, 0.1); border: 1px solid #9b59b6;">
        <strong>Private Key</strong><br>
        <span style="font-size: 0.85em; color: #888;">The 256-bit secret integer</span>
    </div>
    <div class="my-2" style="font-size: 0.8em;">↓ <i>secp256k1 (Elliptic Curve Math)</i> ↓</div>
    
    <div class="card p-2 text-center z-depth-1 w-75" style="background-color: rgba(241, 196, 15, 0.1); border: 1px solid #f1c40f;">
        <strong>Public Key</strong>
    </div>
    <div class="my-2" style="font-size: 0.8em;">↓ <i>Keccak-256 (Hashing)</i> ↓</div>
    
    <div class="card p-2 text-center z-depth-1 w-75" style="background-color: rgba(230, 126, 34, 0.1); border: 1px solid #e67e22;">
        <strong>Raw Ethereum Address</strong><br>
        <span style="font-size: 0.85em; color: #888;">Last 20 bytes of the hash</span>
    </div>
    <div class="my-2" style="font-size: 0.8em;">↓ <i>EIP-55</i> ↓</div>
    
    <div class="card p-2 text-center z-depth-1 w-75" style="background-color: rgba(231, 76, 60, 0.1); border: 1px solid #e74c3c;">
        <strong>Checksummed Address</strong><br>
        <span style="font-size: 0.85em; color: #888;">Final format with mixed capitalization</span>
    </div>
</div>

---

### Untangling the Concepts

While doing this homework, I realized how easy it is to confuse some of these terms. For example, key *generation* and key *derivation* are conceptually opposite. Generation is all about rolling the dice and getting true randomness (like creating the initial 12 words). Derivation, on the other hand, is strict, predictable math. If I know your master seed, the BIP-32 algorithm will spit out the exact same child keys every single time. It's totally deterministic.

Another thing that took me a minute to grasp was the difference between hashing and public-key cryptography. I used to put them in the same bucket. Hashing (like Keccak) is basically a digital blender: you throw data in, and you get a fixed-size string out. It destroys all mathematical relationships, which is great for creating irreversible fingerprints. Public-key cryptography (like the secp256k1 curve from Homework 1) is completely different because it *preserves* a mathematical structure. That algebraic link between the private and public key is the only reason we can digitally sign transactions. 

Also, encoding has nothing to do with encryption. Hexadecimal encoding just translates raw binary into characters we can read on a screen. The EIP-55 standard takes that hex string and applies a checksum to it by running it through a hash and capitalizing specific letters based on the result. It's a clever trick: if you mistype even one letter, the checksum fails and the wallet refuses to send the transaction.

### Why not just use one algorithm?

Looking at the whole pipeline, it makes sense why Ethereum relies on a mix of different tools rather than a single master algorithm. Every primitive handles a specific vulnerability. 

BIP-39 and BIP-32 are strictly there for us humans—nobody can memorize a 512-bit binary string, but anyone can write down 12 words and derive thousands of accounts from them. The `secp256k1` curve does the actual heavy lifting for asymmetric security on the network. Then Keccak-256 comes in to compress those clunky public keys into standard, uniform addresses. 

Speaking of Keccak, I found a weird historical quirk during my research. Ethereum uses Keccak-256, but this is actually not the same thing as the official NIST SHA3-256 standard. Apparently, Ethereum developers adopted Keccak before NIST finalized the SHA-3 standard in 2015. NIST ended up changing the domain separation padding at the last minute. Because Ethereum had already committed to the original Keccak design, they just kept it. So, if you hash the exact same word using standard SHA-3 and Ethereum's Keccak, you'll get two totally different hashes.
