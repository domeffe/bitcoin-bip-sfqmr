*BIP: XXX · Layer: Consensus (soft fork) · Title: Satoshi-First Quantum Mitigation Roadmap (SF-QMR) · Author: domeffe · Status: Draft · Type: Standards Track · Created: 2026-03-03 · License: BSD-2-Clause · Version: 1.0*

## Abstract
This proposal defines a phased transition to neutralize the systemic risk posed by ECDSA (secp256k1) vulnerabilities in the presence of a Cryptographically Relevant Quantum Computer (CRQC). It mandates a "Protocol-Level Vaulting" of Satoshi-era outputs (Blocks 0-100k) and a mandatory migration period for pre-2013 legacy outputs. Unlike previous "full opt-in" proposals, this BIP (“partial opt-in”) prioritizes network fungibility and the 21M supply integrity over the indefinite validity of obsolete script types. The proposal offers two mutually exclusive governance paths for long‑term handling of vulnerable outputs: an Indefinite Freeze or a long‑term "Hibernation" requiring explicit consensus renewal. Only one of these models can be activated, and the community must select which path to adopt during the activation process. Hibernation could, in theory, allow funds to be recovered by leveraging HD seeds and proving ownership through Zero‑Knowledge proofs. Bitcoin must remain fully immutable only if it stays resistant to cryptographic attacks.

## Motivation
As observed by the community, the continued existence of ~4 million BTC in ECDSA-only, non-hashed public key outputs (P2PK) represents a "Cryptographic Overflow Bug." If a CRQC moves these coins, the narrative of Bitcoin as a secure store of value will suffer a Narrative Collapse. The exposure of 1.1M BTC in P2PK outputs represents a risk to economic‑consensus stability, where a loss of trust in mathematical scarcity triggers a collapse of the network’s thermodynamic security.
This proposal introduces a class of UTXOs that become invalid even if a signature is provided. Quantum‑broken signatures are not ‘valid’ proofs of ownership, because once a key can be reproduced by anyone, the signature no longer represents authorization. There is no property without cryptographic security. 
Solar years are used in the main text for clarity, while exact Median-Time-Past are specified in the Appendix at the end of the document.

We distinguish between two types of risk:

* **Systemic Risk (Satoshi/Legacy):** Concentrated supply that, if looted, destroys market confidence and fungibility. It’s critical to note that deceased users (or “lost keys”) are unable to opt-in.
* **Individual Risk (Post-2013):** Negligent users who fail to migrate despite having the tools (Hashed addresses like P2PKH/SegWit provide a first-layer defense until a public key is revealed).

After fixing the "Systemic Risk", the community must decide: move to Tier 3 (and secure the remaining supply) or accept an "Entropy Tax" (probably 4-5% of the total supply that's still open to exploitation). This BIP proposes to save the Integrity of the Network by freezing the most dangerous attack vectors.
Crucially, SF‑QMR does not ignore the vulnerability of post‑2013 outputs; it simply recognizes a hierarchy of risk. While Tier 1 (0–100k) and Tier 2 outputs represent a threat to the network’s collective trust and could trigger a collapse of confidence, post‑2013 outputs constitute an Individual‑Security risk. The protocol must prioritize the preservation of the Ledger over the protection of a single inattentive user. Once the ‘Core’ of the supply is secured, the network can debate extensions of SF‑QMR to later tiers without the pressure of an immediate systemic threat.

## Specification

### 1. The Satoshi-Block Freeze (T + 12 Months)
After a 12-month activation window, all outputs in blocks 0 to 100,000 that utilize `OP_CHECKSIG` with raw public keys (P2PK) shall be considered invalid for spending. The activation window is calculated with Median-Time-Past (see Appendix).

* **Mechanism:** A soft-fork rule that makes any transaction attempting to spend these UTXOs invalid.
* **Target:** Approximately 1.1 million BTC.

### 2. The Legacy Purge (T + 36 Months)
All P2PK and un‑hashed legacy outputs created before block height 210000 (approximately late 2012) must be migrated to a Quantum‑Resistant (QR) scheme (e.g., SLH‑DSA or a hybrid Taproot leaf or any future consensus-approved quantum-resistant scheme). Block height 210000 (≈ late 2012) marks the period in which the overwhelming majority of P2PK and un‑hashed outputs were created. These outputs expose raw public keys directly on‑chain, making them the most vulnerable class under quantum key‑recovery attacks.

* **Deadline:** 36 months from activation (T + 36).
* **Effect:** After the deadline, any transaction input spending a UTXO created at or before height 210,000 with a scriptPubKey matching the pattern [pubkey] OP_CHECKSIG SHALL be considered invalid by consensus, regardless of the validity of the provided ECDSA signature.

### 3. Acceptance of the "Entropy Tax"
Post-2013 outputs (P2PKH, SegWit, Taproot) are not frozen by this BIP. While they remain vulnerable once a public key is revealed, the hashing of the public key (RIPEMD160(SHA256(P))) provides a temporary "Quantum Shield." We accept the loss of the ~4-5% of supply held by negligent post-2013 users (opt-in) as an unavoidable "Entropy Tax" that does not threaten the systemic floor of the network.
We acknowledge that addressing post‑2013 quantum vulnerabilities will require deeper technical integration; our primary objective in this BIP is to remove the systemic risk to the network, and it is not feasible to solve the entire quantum‑security problem within a single proposal.

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
A quantum‑vulnerable output is comparable to an abandoned property with no door: the protective barrier is gone, and any thief can walk in and take possession. Securing such an output is equivalent to installing a proper door and sealing the entrance so that intruders cannot enter. This is not confiscation; it is the restoration of basic security. The objective is to prevent unauthorized access and ensure that only the legitimate owner could eventually reclaim control once post‑quantum authentication becomes available. Maintaining the Bitcoin infrastructure requires immense collective resources (energy, nodes, security). If the protocol allows a quantum thief to loot systemic supplies (like Satoshi’s coins), it creates a theft against the community. Forcing the network to support "broken locks" that can be opened by any attacker undermines the value of the entire ecosystem. Protecting the ledger is a duty to the stakeholders who sustain it.

Temporary freezes with unlocking through external procedures (such as KYC or legacy key verification) are unsustainable. Once quantum technology can break private keys there is no reliable way to distinguish the legitimate owner from the thief. At that point, any attempt to ‘unfreeze’ coins becomes arbitrary and technically impossible to administer.

Freezing only very old coins (pre‑2013) that are already cryptographically vulnerable prevents any future narrative that governments or developers could arbitrarily freeze inconvenient addresses today, which are still cryptographically protected. It removes the narrative weapon used by “full‑opt‑in” maximalists who argue that any precedent could later be applied to modern and secure addresses. 

## Alternative to an Indefinite Freeze (The 50‑Year Security Vault)

### Mechanism
- **Vulnerable outputs** (pre‑2013, non‑quantum‑resistant) are frozen for a **fixed period of 50 solar years**.
- During this period, they cannot be spent, transferred, or re‑activated.

### Automatic / Consensus Renewal
- After 50 years, the freeze (i.e., hibernation) must be reaffirmed by renewed network consensus. Without explicit consensus renewal, the rule cannot continue to apply.
- The "hibernation" can only continue if the network explicitly renews consensus after the 50‑year term.

### Legal Chaos Under Quantum Conditions: No KYC or Manual Recovery
Once private keys can be reproduced by anyone with quantum capabilities, the legal system becomes unworkable. Multiple actors could simultaneously claim ownership of the same pre‑2013 outputs, each presenting a perfectly valid signature generated through quantum key‑recovery. Courts would be flooded with parallel, contradictory claims, and no real authority would be able to determine who the legitimate owner was. This creates a state of complete legal chaos, where property rights become unenforceable and every dispute is technically irresolvable.

### Political Rationale
- This option **preserves long‑term theoretical property rights**, since the freeze is not permanent but periodically re‑evaluated.
- It **neutralizes the immediate systemic risk** posed by quantum‑vulnerable legacy coins.
- It prevents sudden **inflationary pressure** from the potential reactivation of Satoshi‑era coins.
- It ensures **market stability across generations**, while avoiding any narrative of arbitrary or politically motivated intervention.

### Tier 3 (Optional): 50‑Year Hibernation for All Remaining Vulnerable Addresses
Tier 3 is an optional extension of SF‑QMR that applies a 50‑year hibernation to all remaining vulnerable addresses, including post‑2013 outputs whose public keys have been revealed. This tier does not address systemic risk—already neutralized by Tier 1 and Tier 2—but offers an additional long‑term protection layer for the broader address space. Tier 3 is not activated by default; it requires explicit community approval and follows the same governance model chosen at activation (either Indefinite Freeze or 50‑Year hibernation with consensus renewal). In theory, after 50 years, holders might regain spendability through zero‑knowledge (ZK) proofs derived from their HD seed, allowing ownership to be demonstrated without revealing a quantum‑breakable public key.

**In summary:** Bitcoin must remain immutable only if it stays resistant to cryptographic attacks. A partially opt-in quantum patch represents the right compromise to balance immutability with the preservation of network integrity.

## Appendix

### 1. Satoshi‑Era Vault (Tier 1)
**Criteria** — Any UTXO created at `block_height <= 100000` with a `scriptPubKey` matching the P2PK definition.  
**Deadline** — `MTP >= T_activation + 31,536,000` seconds (≈ 1 year).  
**Effect** — Spending attempts **SHALL be invalid**, regardless of signature validity, since quantum‑broken signatures do not constitute proof of ownership.

---

### 2. Legacy Purge (Tier 2)
**Criteria** — Any UTXO created at `block_height <= 210000` with a `scriptPubKey` matching the P2PK definition.  
**Deadline** — `MTP >= T_activation + 94,608,000` seconds (≈ 3 years).  
**Effect** — Spending attempts **SHALL be invalid**, regardless of signature validity, for the same reason: broken signatures are not valid authorization. Tier 1 rules take precedence at T+12, Tier 2 extends restriction at T+36.

---

### 3. The 50‑Year Security Vault
**Duration** — `1,576,800,000` seconds (≈ 50 solar years).  
**Renewal** — After each 50‑year interval, the freeze **must be reaffirmed by renewed network consensus**. Without explicit consensus renewal, the rule cannot continue to apply.

---

### 4. Formal Definition of Targeted P2PK Outputs

For the purposes of this BIP, an output SHALL be classified as a targeted P2PK output if and only if its `scriptPubKey` matches the following exact byte‑level structure:

- **Pattern** — `[PushData] [OP_CHECKSIG]`
- **PushData** — A single canonical data push of:
  - **33 bytes** (compressed public key starting with `0x02` or `0x03`), or  
  - **65 bytes** (uncompressed public key starting with `0x04`)
- **OP_CHECKSIG** — The single byte `0xAC`

**Strictness** — Any `scriptPubKey` containing additional opcodes (e.g., `OP_DUP`, `OP_CHECKSIGVERIFY`), non‑canonical pushes, malformed key lengths, or any trailing data after `0xAC` is **EXCLUDED** from the automated freeze defined in this BIP.


















