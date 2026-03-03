BIP: XXX
Layer: Consensus (soft fork)
Title: Satoshi-First Quantum Mitigation Roadmap (SF-QMR)
Author: domeffe
Status: Draft
Type: Standards Track
Created: 2026-03-03
License: BSD-2-Clause 
Version: 1.0

## Abstract
This proposal defines a phased transition to neutralize the systemic risk posed by ECDSA (secp256k1) vulnerabilities in the presence of a Cryptographically Relevant Quantum Computer (CRQC). It mandates a "Protocol-Level Vaulting" of Satoshi-era outputs (Blocks 0-100k) and a mandatory migration period for pre-2013 legacy outputs. Unlike previous "full opt-in" proposals, this BIP (“partial opt-in”) prioritizes network fungibility and the 21M supply integrity over the indefinite validity of obsolete script types. Bitcoin must remain immutable only if it stays resistant to cryptographic attacks.

## Motivation
As observed by the community, the continued existence of ~4 million BTC in ECDSA-only, non-hashed public key outputs (P2PK) represents a "Cryptographic Overflow Bug." If a CRQC moves these coins, the narrative of Bitcoin as a secure store of value will suffer a Narrative Collapse.

We distinguish between two types of risk:

* **Systemic Risk (Satoshi/Legacy):** Concentrated supply that, if looted, destroys market confidence and fungibility. It’s critical to note that deceased users (or “lost keys”) are unable to opt-in.
* **Individual Risk (Post-2013):** Negligent users who fail to migrate despite having the tools (Hashed addresses like P2PKH/SegWit provide a first-layer defense until a public key is revealed).

It is mathematically and socially implausible to save 100% of the supply. This BIP proposes to save the Integrity of the Network by freezing the most dangerous attack vectors.

## Specification

### 1. The Satoshi-Block Freeze (T + 12 Months)
After a 12-month activation window, all outputs in blocks 0 to 100,000 that utilize `OP_CHECKSIG` with raw public keys (P2PK) shall be considered invalid for spending.

* **Mechanism:** A soft-fork rule that makes any transaction attempting to spend these UTXOs invalid.
* **Target:** Approximately 1.1 million BTC.

### 2. The Legacy Purge (T + 36 Months)
All P2PK and un-hashed legacy outputs created before January 1, 2013, must be migrated to a Quantum-Resistant (QR) scheme (e.g., SLH-DSA or a hybrid Taproot leaf).

* **Deadline:** 36 months from activation.
* **Effect:** After the deadline, the `secp256k1` `OP_CHECKSIG` and `OP_CHECKSIGVERIFY` opcodes are disabled for these specific UTXOs.

### 3. Acceptance of the "Entropy Tax"
Post-2013 outputs (P2PKH, SegWit, Taproot) are not frozen by this BIP. While they remain vulnerable once a public key is revealed, the hashing of the public key (RIPEMD160(SHA256(P))) provides a temporary "Quantum Shield." We accept the loss of the ~4-5% of supply held by negligent post-2013 users (opt-in) as an unavoidable "Entropy Tax" that does not threaten the systemic floor of the network.

## Rationale
* **Why Freeze?** Following the 2010 Value Overflow precedent, the protocol must intervene when a technical flaw threatens the total supply.
* **Why these dates?** 12 months for Satoshi coins is a safety firebreak. 36 months for legacy is a reasonable window for any active, sentient holder to perform a single transaction.
* **Fungibility:** By removing the "Satoshi Sword of Damocles," we ensure that the remaining 95% of the supply is perceived as secure, preserving Bitcoin’s status as Hard Money. If we don’t freeze the legacy coins (pre‑2013), the narrative that Bitcoin was “hacked” will dominate, leading to an irreversible loss of trust in the integrity of the network. Explaining to the public the difference between hacked-vulnerable coins and quantum‑secure coins would be difficult, further weakening trust if legacy coins remain unfrozen and available to be stolen.

## Backward Compatibility
This is a soft fork. Old nodes will see these transactions as valid (if they follow old rules), but new nodes will reject them, eventually forcing the network to follow the more secure chain.

## Deployment (SF-QMR Protocol)
**Activation Method: User Activated Soft Fork (UASF)** This BIP shall be implemented via a User Activated Soft Fork (UASF). The activation is driven by the economic layer and full-node consensus, independent of hash-rate signaling (Miner-Independent Activation).

**Flag Day: January 1, 2031 (00:00:00 UTC)** On this date, the consensus rules defined in this BIP (The Satoshi-Block Freeze) will become mandatory for all validating nodes.

* **Enforcement:** Any block containing a spend attempt from P2PK outputs within blocks 0-100,000 after this timestamp will be rejected as invalid by the network.
* **Legacy Transition:** This fixed date provides the necessary window for the community to achieve social consensus and for any active legacy holders to perform a migration to Quantum-Resistant schemes before the subsequent "Legacy Purge" deadline.

## Rationale for UASF over BIP9
Quantum computing represents an external, systemic threat to the cryptographic foundation of the network. Relying on Miner Signaling (BIP9) introduces a strategic vulnerability where miners under external pressure could veto essential security upgrades. A UASF ensures that the economic majority—those with the most capital at risk—determines the security threshold and the preservation of the 21M supply integrity.

This approach mirrors the successful SegWit UASF of 2017, where economic nodes enforced security upgrades against miner resistance.

## Comparison and Policy

Alternative recovery schemes like those proposed by BitMEX Research require excessive protocol complexity and node burden; therefore this BIP opts for a simple, enforceable freeze for the most vulnerable and ancient coins.

This is not a confiscation, because an ultimatum period allows anyone holding those very old, cryptographically vulnerable pre‑2013 coins to move them. Only coins that remain unmoved after the deadline would be frozen, making the process voluntary rather than coercive. Taproot spending vulnerabilities and post-2013 address reuse thefts are strictly out of scope for this BIP.

Temporary freezes are unsustainable, because once quantum technology can break private keys there is no reliable way to distinguish the legitimate owner from the thief. At that point, any attempt to ‘unfreeze’ coins becomes arbitrary and technically impossible to administer.

Freezing only very old coins (pre‑2013) that are already cryptographically vulnerable prevents any future narrative that governments or developers could arbitrarily freeze inconvenient addresses today, which are still cryptographically protected. It removes the narrative weapon used by “full‑opt‑in” maximalists who argue that any precedent could later be applied to modern and secure addresses. 

## Alternative to an Indefinite Freeze (The 50‑Year Security Vault)

### Mechanism
- **Vulnerable outputs** (pre‑2013, non‑quantum‑resistant) are frozen for a **fixed period of 50 solar years**.
- During this period, they cannot be spent, transferred, or re‑activated.

### Automatic / Consensus Renewal
- At the end of the 50‑year term, the protocol performs an **automatic cryptographic status check**.
- If the risk of **Indistinguishability** persists — meaning it is still impossible to reliably differentiate the original owner from a quantum attacker — the freeze is **extended for another 50 years** via soft‑fork.
- This renewal cycle repeats until the cryptographic environment guarantees that ownership can be proven without ambiguity.

### Political Rationale
- This option **preserves long‑term theoretical property rights**, since the freeze is not permanent but periodically re‑evaluated.
- It **neutralizes the immediate systemic risk** posed by quantum‑vulnerable legacy coins.
- It prevents sudden **inflationary pressure** from the potential reactivation of Satoshi‑era coins.
- It ensures **market stability across generations**, while avoiding any narrative of arbitrary or politically motivated intervention.


**In summary:** Bitcoin must remain immutable only if it stays resistant to cryptographic attacks. A partially opt-in quantum patch represents the right compromise to balance immutability with the preservation of network integrity.


