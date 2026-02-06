# Zero Knowledge Terminology

A guide to fundamental concepts in zero-knowledge proofs.

## Core Concepts

### 1. Claim / Statement

A **claim** (or **statement**) is an assertion that something is true. In the context of zero-knowledge proofs (ZKPs), it refers to the property being proven without revealing additional information. It is the "claim" the prover is making about the "witness".

**Example:** "I know a number x such that SHA256(x) = y" where y is publicly known.

### 2. Inputs

#### 2(a). Private Input

**Private inputs** are inputs to the system which are only known to the prover and not the verifier. These values remain hidden throughout the proof process, preserving privacy while still allowing verification.

**Example:** A user's password, secret key, or personal data.

#### 2(b). Public Input

**Public inputs** are inputs known to both the prover and the verifier. These are shared values that both parties can reference during the proof verification process.

**Example:** A hash value, public parameters, or transaction amount.

### 3. Constraint

A **constraint** is a mathematical condition which must be satisfied in order for the claim to be valid. Constraints define the rules the inputs must follow. These are typically expressed as polynomial equations or logical assertions.

**Example:** `x * y = z` or `x + y < 100`

### 4. Circuit

A system of constraints makes up the **circuit**. It consists of a series of mathematical relations and operations. The circuit defines how the constraints work together to verify the claim.

The circuit can be thought of as a computational graph where:
- Inputs (public and private) flow through gates
- Each gate applies an operation (addition, multiplication, etc.)
- Outputs must satisfy all constraints

**Common circuit types:**
- **Arithmetic circuits:** Use addition and multiplication gates
- **Boolean circuits:** Use logical gates (AND, OR, NOT)
- **R1CS (Rank-1 Constraint System):** A standard format for representing circuits in many ZK systems

### 5. Witness

The **witness** is the set of private values that allow a prover to demonstrate that their claim or statement is valid/true. The witness includes the private inputs but also can include intermediate calculations. The witness must satisfy the constraints of the circuit.

**Key properties:**
- Contains all information needed to satisfy the circuit constraints
- Remains hidden from the verifier
- Must be computationally infeasible to derive from the public inputs alone

**Example:** In proving "I know x such that x² = 9", the witness is x = 3 (or x = -3).

### 6. Prover and Verifier

#### Prover

The **prover** is the entity that generates the proof of computation to demonstrate knowledge of the witness while satisfying the circuit constraints. The prover:
- Has access to both public and private inputs
- Performs the computation
- Generates a cryptographic proof

#### Verifier

The **verifier** is the entity that checks whether the proof is valid. The verifier:
- Has access only to public inputs and the proof
- Does not learn anything about the private inputs (zero-knowledge property)
- Can efficiently verify the proof (typically in logarithmic or constant time)

### 7. Trusted Setup

A **trusted setup** is a procedure used particularly in ZKP systems like SNARKs (e.g., Groth16 and PLONK). It is a ceremony that is done once to generate some data that must be used every time the cryptographic (ZK in this instance) protocol is run.

#### Key Components

##### Toxic Waste

During the trusted setup, random values are generated. If these values are leaked or compromised, an attacker could forge invalid proofs that would pass verification. In order to maintain security, these random values must be securely destroyed. This sensitive data is referred to as "toxic waste."

##### Common Reference String (CRS)

A set of public parameters that both the prover and verifier use in the proof generation and verification processes. This is the cryptographic parameter that is created during the trusted setup.

##### Structured Reference String (SRS)

A specific type of CRS. Instead of generic parameters, it contains structured data with a specific format (e.g., elliptic curve points: points on an elliptic curve, where these points are derived from a secret value).

##### Multi-Party Computation (MPC)

MPC in the context of ZKPs is a security-enhancing process where multiple parties sequentially contribute to creating a final parameter. The process works as follows:
1. Each participant receives the current parameter
2. They apply their own secret transformation
3. They contribute a tiny bit of randomness
4. They pass on this parameter to the next participant
5. They then destroy their contribution

**Security guarantees:** To reconstruct the final random value, it would require all participants to keep their secret. This means that even if there's only one honest party that destroys their contribution, the secret cannot be reconstructed and the system remains secure, regardless of whether some parties were malicious and kept their contribution.

##### Powers of Tau

Used specifically in SNARKs like Groth16 and PLONK. A series of elliptic curve points generated using multi-party computation. These are the encoded powers of τ (tau): g^τ, g^(τ²), g^(τ³), etc.

##### Polynomial Commitment

A cryptographic method that allows you to commit to a specific polynomial while keeping the coefficients of this polynomial secret.

**Example:** KZG Commitment - "This is going to be a polynomial that I am going to use," without revealing the coefficients of said polynomial.

#### Trusted Setup Examples

##### 1. Circuit-Specific Setup (e.g., Groth16)

The cryptographic parameters need to be regenerated for every circuit. This involves a two-phase setup:

**Phase 1: Powers of Tau**
- Generates a general-purpose SRS for circuits of a certain size
- This step is not circuit-specific and can be reused
- This is where the structured reference string (SRS) containing the encoded powers of τ as elliptic curve points is generated
- These are the g^τ, g^(τ²), etc. elements

**Phase 2: Circuit-Specific**
- The powers of tau are combined with the circuit's constraints to produce an extended SRS (with its own toxic waste)
- This generates:
  1. A proving key
  2. A verification key

##### 2. Universal Setup (e.g., PLONK)

The cryptographic parameters can be reused for circuits up to a certain size.

**PLONK Setup:**
- Universal and updatable SRS
- Multiple circuits can share the same setup as long as they fit within a size constraint
- The Powers of Tau process generates the SRS, which is then used for polynomial commitments
- PLONK commonly uses the KZG commitment scheme, where polynomials are committed using the Powers of Tau and evaluated at a specific challenge point

## Additional Concepts

### Setup Phase

Many ZK proof systems require a **setup phase** that generates public parameters (sometimes called a "common reference string" or CRS) used by both prover and verifier. This can be:
- **Trusted setup:** Requires trust that toxic waste was destroyed
- **Transparent setup:** No trusted parties needed

### Soundness

It must be practically impossible for a dishonest prover to convice an honest verfier with an invalid witness.

### Completeness

If the statement is valid, a prover must always be able to convice a verifier if they have knowledge of the witness.

### Zero-Knowledge

The verifier must learn nothing other than the provers knowledge of a witness to the statement.

## Common ZK Proof Systems

- **SNARKs** (Succinct Non-interactive ARguments of Knowledge): Compact proofs, fast verification
- **STARKs** (Scalable Transparent ARguments of Knowledge): No trusted setup, post-quantum secure
- **Bulletproofs**: Short proofs without trusted setup
- **PLONK**: Universal and updateable setup

---

*This document provides foundational terminology for understanding zero-knowledge proof systems.*
