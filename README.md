# Keystone Contract

> **The on-chain protocol layer for KeystoneGrid, built for Stellar and Soroban.**

`Keystone-Contract` contains the smart-contract infrastructure that powers the KeystoneGrid protocol.

KeystoneGrid is an open-source infrastructure project for representing and managing verified real-world assets on the Stellar network.

The contract layer is responsible for enforcing **on-chain ownership, authorization, asset state, investment rules, settlement, and other protocol guarantees**.

It is intentionally separated from the KeystoneGrid backend and frontend.

---

## Table of Contents

* [Overview](#overview)
* [Role in KeystoneGrid](#role-in-keystonegrid)
* [Design Philosophy](#design-philosophy)
* [Architecture](#architecture)
* [Responsibilities](#responsibilities)
* [Non-Responsibilities](#non-responsibilities)
* [Technology Stack](#technology-stack)
* [Protocol Model](#protocol-model)
* [Asset Model](#asset-model)
* [Property Lifecycle](#property-lifecycle)
* [Issuer Model](#issuer-model)
* [Investor Model](#investor-model)
* [Compliance Model](#compliance-model)
* [Ownership Model](#ownership-model)
* [Investment Model](#investment-model)
* [Revenue Distribution](#revenue-distribution)
* [Authorization](#authorization)
* [Events](#events)
* [Errors](#errors)
* [Storage](#storage)
* [Security](#security)
* [Testing](#testing)
* [Repository Structure](#repository-structure)
* [Development Environment](#development-environment)
* [Local Development](#local-development)
* [Building](#building)
* [Testing](#testing)
* [Formatting and Linting](#formatting-and-linting)
* [Deployment](#deployment)
* [Contract Configuration](#contract-configuration)
* [Integration With Backend](#integration-with-backend)
* [Integration With Frontend](#integration-with-frontend)
* [Contributing](#contributing)
* [Protocol Changes](#protocol-changes)
* [Security-Sensitive Changes](#security-sensitive-changes)
* [Roadmap](#roadmap)
* [Status](#status)
* [License](#license)

---

# Overview

The KeystoneGrid contract layer provides the blockchain-enforced part of the KeystoneGrid protocol.

The fundamental principle is:

> **If the system claims that something is true because the blockchain guarantees it, that guarantee belongs in the contract layer.**

Examples include:

* Ownership
* Token balances
* Authorized protocol actions
* Asset state
* Investment settlement
* Distribution accounting
* Contract permissions
* Important lifecycle transitions

The contract should **not** attempt to become a database, document management system, KYC provider, or property-data warehouse.

---

# Role in KeystoneGrid

KeystoneGrid consists of three primary technical layers.

```text
                         USERS
                           │
                           ▼
                 ┌──────────────────┐
                 │ Keystone Frontend│
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Keystone Backend │
                 │                  │
                 │ API              │
                 │ Verification    │
                 │ Indexing         │
                 │ Metadata         │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ Keystone Contract│
                 │                  │
                 │ Asset State      │
                 │ Ownership        │
                 │ Authorization    │
                 │ Settlement       │
                 └────────┬─────────┘
                          │
                          ▼
                      STELLAR
```

The contract is therefore one component of the larger system.

It should remain independently understandable and independently testable.

---

# Design Philosophy

## 1. Blockchain as the source of truth for ownership

The contract is the authoritative source for on-chain ownership.

The backend may index ownership.

The frontend may display ownership.

Neither should independently redefine it.

```text
Blockchain
    │
    ▼
Authoritative ownership state
    │
    ├── Backend indexes it
    │
    └── Frontend displays it
```

---

## 2. Minimize trust assumptions

Whenever practical, important financial state should be enforced by deterministic contract logic rather than by a centralized backend.

For example:

Bad:

```text
Backend database:
user.balance = 1000
```

Better:

```text
Blockchain:
user → 1000 ownership units
```

The backend can then derive:

```text
indexed_balance = blockchain_balance
```

---

## 3. Do not put unnecessary personal information on-chain

Real-world assets may involve sensitive information.

The contract should not store:

* Government ID numbers
* Passport information
* Addresses
* KYC documents
* Personal financial documents
* Private legal records

Instead, the contract should store only the minimum information required to enforce protocol rules.

Where necessary, cryptographic references or non-sensitive identifiers may be used.

---

## 4. Prefer Stellar/Soroban primitives

The protocol should be designed around Soroban and Stellar rather than attempting to reproduce Ethereum architecture.

Do not introduce EVM concepts merely because they existed in the previous BOTEstate implementation.

For example:

```text
Old architecture:
ERC-1155
ERC-20
Solidity
OpenZeppelin
EVM wallets
```

should not automatically become:

```text
Soroban contract
+
"ERC-1155-like" abstraction
```

The correct question is:

> **What is the best Stellar-native representation of this requirement?**

---

# Responsibilities

The contract layer is responsible for enforcing protocol-level state.

Depending on the final protocol specification, this may include:

## Asset registry

* Asset identifier
* Asset type
* Issuer
* Asset status
* Tokenization status
* Verification reference
* Supply configuration

---

## Ownership

* Ownership units
* Ownership balances
* Ownership restrictions
* Eligible holders
* Transfer rules

---

## Investment

* Investment authorization
* Purchase conditions
* Asset availability
* Pricing rules
* Supply limits
* Settlement

---

## Compliance enforcement

The contract should enforce protocol-level eligibility requirements.

For example:

```text
Investor
   │
   ▼
Eligibility credential/reference
   │
   ▼
Contract authorization
   │
   ▼
Investment allowed
```

The contract should **not** perform the complete identity-verification process itself.

---

## Distribution

Where assets generate eligible revenue, the contract may enforce:

* Distribution accounting
* Ownership-based allocation
* Claiming
* Distribution state
* Distribution events

---

## Administrative controls

Depending on the final governance model:

* Protocol administration
* Issuer permissions
* Compliance permissions
* Emergency controls
* Asset lifecycle controls

Administrative powers must be explicitly documented.

---

# Non-Responsibilities

The contract should not become responsible for:

### KYC document processing

Handled off-chain.

### Property document storage

Handled off-chain.

### Property images

Handled off-chain.

### Search

Handled by the backend.

### Analytics

Handled by the backend/application layer.

### Email/SMS notifications

Handled off-chain.

### Large metadata

Stored off-chain with appropriate references.

### User interface

Handled by `Keystone-Frontend`.

---

# Technology Stack

The contract layer is built around:

| Technology          | Purpose                       |
| ------------------- | ----------------------------- |
| Stellar             | Blockchain network            |
| Soroban             | Smart-contract platform       |
| Rust                | Contract programming language |
| WebAssembly         | Contract execution target     |
| Stellar SDK/tooling | Development and integration   |
| Rust test tooling   | Unit and integration testing  |

Soroban contracts are written in Rust and compiled to WebAssembly for execution on Stellar. [Stellar Soroban Smart Contracts Documentation](https://developers.stellar.org/docs/build/smart-contracts/overview?utm_source=chatgpt.com)

---

# Protocol Model

The protocol should conceptually represent:

```text
Asset
 │
 ├── Issuer
 ├── Verification
 ├── Ownership
 ├── Investment
 ├── Revenue
 └── Lifecycle
```

An asset is not considered merely a token.

It is a protocol-level representation of a verified real-world economic asset.

---

# Asset Model

A tokenized asset should have a unique protocol identity.

A conceptual asset record may contain:

```text
Asset
├── asset_id
├── asset_type
├── issuer
├── status
├── total_units
├── available_units
├── price
├── payment_asset
├── verification_reference
└── metadata_reference
```

The exact storage representation is an implementation decision and must be defined in the protocol specification before production deployment.

---

# Property Lifecycle

Real estate is the first target asset class.

A property may move through:

```text
DRAFT
  ↓
SUBMITTED
  ↓
UNDER_REVIEW
  ↓
VERIFIED
  ↓
TOKENIZED
  ↓
ACTIVE
  ↓
CLOSED
```

Failure states may include:

```text
REJECTED
SUSPENDED
CANCELLED
```

The contract should enforce only the lifecycle transitions that require blockchain guarantees.

The backend manages the broader application workflow.

---

# Issuer Model

An issuer is the entity responsible for bringing an asset into KeystoneGrid.

The protocol must distinguish between:

```text
Someone who submits an asset
```

and:

```text
An authorized issuer
```

A future issuer model may require:

* Issuer verification
* Issuer authorization
* Asset-specific permissions
* Issuer revocation
* Administrative review

The exact model should be defined before the first production deployment.

---

# Investor Model

An investor interacts with the protocol through a Stellar account.

A conceptual flow is:

```text
Stellar Account
      │
      ▼
Eligibility Verification
      │
      ▼
Protocol Authorization
      │
      ▼
Investment
      │
      ▼
Ownership
```

The contract should never assume that:

> connected wallet = eligible investor.

Eligibility is a separate protocol concern.

---

# Compliance Model

Compliance is deliberately divided between off-chain verification and on-chain enforcement.

```text
                 OFF-CHAIN
┌──────────────────────────────────┐
│ Identity verification            │
│ Document verification            │
│ Risk screening                   │
│ Jurisdiction checks              │
│ Eligibility decision             │
└─────────────────┬────────────────┘
                  │
                  ▼
            Eligibility
             credential
                  │
                  ▼
                 ON-CHAIN
┌──────────────────────────────────┐
│ Contract checks eligibility      │
│ Contract enforces restrictions   │
│ Contract records required state  │
└──────────────────────────────────┘
```

This separation minimizes sensitive data exposure while allowing protocol-level enforcement.

---

# Ownership Model

Ownership is one of the most important components of KeystoneGrid.

The final implementation must answer:

* What exactly does one ownership unit represent?
* Is ownership transferable?
* Under what conditions?
* Can ownership be frozen?
* Can an asset be redeemed?
* How are fractional units represented?
* What happens when an asset closes?
* How does compliance affect transferability?

These decisions must be explicitly specified before the protocol is considered production-ready.

---

# Investment Model

The basic conceptual flow is:

```text
Investor
   │
   ▼
Select asset
   │
   ▼
Check eligibility
   │
   ▼
Check availability
   │
   ▼
Authorize transaction
   │
   ▼
Transfer payment
   │
   ▼
Receive ownership units
```

The contract must ensure that:

* An investor cannot purchase more than available supply.
* Unauthorized investors cannot execute restricted operations.
* Payment and ownership changes occur atomically where supported by the architecture.
* Invalid states are rejected.
* Financial calculations are deterministic.

---

# Revenue Distribution

Income-producing assets may generate revenue.

Examples:

* Rent
* Lease income
* Revenue-sharing agreements

The protocol may allow authorized parties to deposit eligible revenue.

Conceptually:

```text
Property
   │
   ▼
Revenue generated
   │
   ▼
Issuer deposits revenue
   │
   ▼
Protocol accounting
   │
   ▼
Ownership-based allocation
   │
   ▼
Investor claim
```

The distribution implementation must carefully handle:

* Multiple holders
* Partial ownership
* Repeated distributions
* New investors
* Investors who exit
* Precision
* Rounding
* Dust
* Unclaimed distributions
* Asset closure

Revenue accounting is considered **security-critical protocol logic**.

---

# Authorization

Soroban provides host-managed authorization mechanisms.

Contracts should use Soroban's authorization model rather than implementing unnecessary custom signature-verification systems.

For example, Soroban supports authorization methods such as `require_auth` and `require_auth_for_args`. [Soroban Authorization Documentation](https://developers.stellar.org/docs/learn/fundamentals/contract-development/authorization?utm_source=chatgpt.com)

Authorization design should distinguish between:

### User

Can perform operations on their own behalf.

### Issuer

Can manage authorized assets.

### Compliance authority

Can manage eligibility-related state where permitted.

### Protocol administrator

Can perform narrowly defined administrative actions.

### Contract

Enforces protocol rules independently of the frontend or backend.

---

# Events

Events are an important integration mechanism.

Contract events should allow the backend to index meaningful protocol activity.

Potential events include:

```text
AssetCreated
AssetVerified
AssetActivated
AssetSuspended
AssetClosed

IssuerAuthorized
IssuerRevoked

InvestorAuthorized
InvestorRevoked

InvestmentMade
OwnershipTransferred

RevenueDeposited
RevenueClaimed

AssetPaused
AssetUnpaused
```

Events should be:

* Meaningful
* Stable
* Documented
* Consistent
* Easy to index

Changing event semantics should be treated as a potentially breaking change for downstream applications.

---

# Errors

Contract errors should be explicit and predictable.

Examples:

```text
Unauthorized
AssetNotFound
AssetAlreadyExists
InvalidAssetState
InvalidIssuer
InvestorNotEligible
InsufficientSupply
InvalidAmount
InvalidPaymentAsset
TransferNotAllowed
DistributionNotAvailable
InvalidVerification
AssetPaused
```

Avoid generic errors where a specific error can communicate the actual failure.

Error names are part of the developer experience.

---

# Storage

Soroban storage should be designed deliberately.

Storage decisions should consider:

* Cost
* Access patterns
* Mutability
* Data size
* Lifecycle
* Query frequency
* Migration complexity

Do not store large documents or unnecessary metadata directly in contract storage.

Prefer references where appropriate.

---

# Security

KeystoneGrid handles ownership and financial state.

Contract security therefore has the highest priority.

## Authorization

Every privileged operation must have a clear authorization requirement.

Questions to answer:

* Who can call it?
* Why?
* What prevents unauthorized access?
* Can authorization be revoked?
* Is the permission scoped?

---

## State transitions

Every state-changing function must validate the current state.

For example:

```text
DRAFT → VERIFIED
```

may be valid.

But:

```text
CLOSED → VERIFIED
```

should not happen unless explicitly supported by the protocol.

---

## Financial calculations

Financial calculations must account for:

* Integer arithmetic
* Rounding
* Precision
* Overflow/underflow considerations
* Dust
* Repeated distributions

Do not rely on floating-point arithmetic.

---

## Reentrancy and external interactions

Any function that interacts with external contracts or assets must be reviewed for:

* Call ordering
* State updates
* Unexpected callbacks
* Authorization
* Failure handling

---

## Administrative powers

Administrative functions are potentially dangerous.

Every administrative capability must have:

1. A documented purpose.
2. A clearly defined authority.
3. A defined scope.
4. A defined emergency procedure.
5. Tests covering unauthorized access.

---

## Upgradeability

If upgradeability is introduced, it must be treated as a major security decision.

Documentation must explain:

* Who can upgrade?
* How is an upgrade authorized?
* Can users opt out?
* What happens to stored state?
* How are upgrades announced?
* Is there a timelock?
* What happens if an upgrade fails?

The default assumption should not be:

> "We can always upgrade later."

---

# Testing

Testing is mandatory for protocol changes.

At minimum, contract contributions should consider:

### Unit tests

Test individual functions.

### Integration tests

Test interactions between contract components.

### Authorization tests

Test both:

```text
authorized → succeeds
unauthorized → fails
```

### State-transition tests

Test valid and invalid transitions.

### Financial tests

Test:

* zero values
* minimum values
* maximum values
* repeated distributions
* multiple investors
* rounding
* partial ownership
* exhausted supply

### Regression tests

Every discovered security or correctness bug should receive a regression test where practical.

---

# Repository Structure

The repository should evolve toward a structure similar to:

```text
Keystone-Contract/
│
├── contracts/
│   ├── asset_registry/
│   ├── ownership/
│   ├── compliance/
│   ├── investment/
│   └── distribution/
│
├── tests/
│
├── scripts/
│
├── docs/
│
├── Cargo.toml
├── Cargo.lock
├── Makefile
└── README.md
```

The final structure may differ depending on the protocol architecture.

Do not create separate contracts merely to make the repository look modular.

Contract boundaries should exist because they provide meaningful security, ownership, upgrade, or composability boundaries.

---

# Development Environment

Recommended tooling:

* Rust toolchain
* Cargo
* Soroban CLI/tooling
* Stellar CLI/tooling
* Docker where required
* Git
* GitHub

Developers should use the versions documented by the repository's toolchain configuration.

Do not assume that the latest version is always compatible with the project.

---

# Local Development

Clone the repository:

```bash
git clone https://github.com/KeystoneGrid/Keystone-Contract.git
cd Keystone-Contract
```

Install the required Rust/Soroban tooling according to the current Stellar documentation and the repository's toolchain configuration.

Then build:

```bash
cargo build
```

Run tests:

```bash
cargo test
```

The exact commands may evolve as the contract architecture develops.

Repository scripts should be preferred once standardized.

---

# Building

The standard Rust build process is:

```bash
cargo build
```

For optimized builds:

```bash
cargo build --release
```

Soroban contract artifacts must be built using the configuration appropriate to the project's deployment workflow.

The generated WASM artifact should be treated as a build output rather than manually edited.

---

# Testing

Run all tests:

```bash
cargo test
```

Run a specific test:

```bash
cargo test <test_name>
```

Before opening a pull request, contributors should run the complete test suite.

For protocol changes, contributors should also explain what new test coverage was introduced.

---

# Formatting and Linting

Format Rust code:

```bash
cargo fmt
```

Check formatting:

```bash
cargo fmt -- --check
```

Run Clippy where configured:

```bash
cargo clippy
```

CI should eventually enforce formatting, linting, and tests automatically.

---

# Deployment

KeystoneGrid should use separate environments.

Conceptually:

```text
Development
     │
     ▼
Stellar Testnet
     │
     ▼
Security Review
     │
     ▼
Production Deployment
```

Production deployment must never be treated as simply:

```text
cargo build
↓
deploy
```

Before production:

* Contract addresses must be recorded.
* WASM artifacts must be reproducible.
* Configuration must be documented.
* Administrative accounts must be identified.
* Security review must be completed.
* Deployment transactions should be recorded.
* Frontend/backend configuration must be updated.
* Rollback/emergency procedures must be documented.

---

# Contract Configuration

The deployment environment should define configuration such as:

```text
Network
Contract IDs
Administrative addresses
Compliance authority
Supported payment assets
Environment
Protocol version
```

Secrets must never be committed to Git.

Do not place:

* Private keys
* Seed phrases
* API secrets
* Deployment credentials

inside the repository.

Use environment-specific secret management.

---

# Integration With Backend

`Keystone-Backend` is responsible for indexing and exposing contract state to applications.

Conceptually:

```text
Soroban Contract
       │
       ▼
Contract Events / State
       │
       ▼
Keystone Backend
       │
       ├── Index
       ├── Normalize
       ├── Store
       └── Expose API
       │
       ▼
Keystone Frontend
```

The backend should not silently mutate protocol state to compensate for contract behavior.

If a protocol rule is wrong, the protocol should be fixed.

---

# Integration With Frontend

The frontend interacts with the contract through Stellar wallet and transaction infrastructure.

A typical transaction flow:

```text
User
 ↓
Frontend
 ↓
Build transaction
 ↓
Wallet authorization
 ↓
Submit to Stellar
 ↓
Network confirmation
 ↓
Backend indexes result
 ↓
Frontend refreshes state
```

The frontend should not assume that:

```text
wallet request accepted
```

means:

```text
transaction succeeded
```

Transaction confirmation must be handled explicitly.

---

# Contributing

KeystoneGrid is open source and welcomes contributors.

Before contributing to the contract layer:

1. Read this README.
2. Read the project architecture documentation.
3. Check existing issues.
4. Check open pull requests.
5. Discuss significant architectural changes before implementation.
6. Add tests.
7. Update documentation where necessary.

---

# Protocol Changes

Changes to core protocol behavior require additional care.

Examples:

* Ownership model
* Asset lifecycle
* Distribution logic
* Compliance rules
* Authorization
* Transferability
* Issuer permissions
* Upgradeability

These changes should normally begin as an architectural discussion or RFC.

A contributor should not implement a major protocol change solely from a short feature request.

---

# Security-Sensitive Changes

The following areas require special review:

```text
Ownership
Authorization
Payments
Distributions
Transfers
Compliance
Upgrades
Administrative controls
Storage migration
External contract calls
```

Security-sensitive PRs should explain:

### Threat model

What could go wrong?

### Attack surface

Which functions/state are affected?

### Mitigation

How does the implementation prevent the issue?

### Testing

What tests demonstrate the mitigation?

---

# Roadmap

## Phase 1 — Protocol Foundation

* [ ] Establish Soroban workspace
* [ ] Define asset model
* [ ] Define asset lifecycle
* [ ] Define issuer model
* [ ] Define ownership model
* [ ] Define authorization model
* [ ] Define event model
* [ ] Define error model

---

## Phase 2 — Core Asset Protocol

* [ ] Asset registry
* [ ] Issuer authorization
* [ ] Asset verification reference
* [ ] Asset lifecycle management
* [ ] Ownership implementation
* [ ] Investment mechanism
* [ ] Payment settlement

---

## Phase 3 — Compliance

* [ ] Eligibility model
* [ ] Compliance authority
* [ ] Restricted operations
* [ ] Transfer restrictions
* [ ] Compliance state transitions
* [ ] Compliance-related events

---

## Phase 4 — Revenue

* [ ] Revenue deposits
* [ ] Ownership-based accounting
* [ ] Distribution claims
* [ ] Multiple distribution cycles
* [ ] Precision/rounding tests
* [ ] Distribution events

---

## Phase 5 — Advanced Protocol Features

Potential future capabilities:

* [ ] Controlled secondary market
* [ ] Asset redemption
* [ ] Oracle integrations
* [ ] Valuation references
* [ ] Multi-asset support
* [ ] Governance
* [ ] Advanced compliance credentials
* [ ] Protocol composability

These features are subject to protocol research and security review.

---

# Development Rules

The following rules should guide contract contributions.

### Rule 1

**Never sacrifice financial correctness for convenience.**

### Rule 2

**Never trust frontend validation as protocol security.**

### Rule 3

**Never treat the backend database as authoritative ownership state.**

### Rule 4

**Never put sensitive personal information on-chain unless there is a compelling, explicitly reviewed reason.**

### Rule 5

**Every financial calculation requires tests.**

### Rule 6

**Every privileged operation requires authorization tests.**

### Rule 7

**Every new protocol state requires a documented lifecycle.**

### Rule 8

**Breaking protocol changes require explicit discussion.**

### Rule 9

**Security assumptions must be documented.**

### Rule 10

**Keep the contract as simple as reasonably possible.**

---

# Status

`Keystone-Contract` is currently under active development.

The contract architecture is being redesigned from the previous Ethereum-based BOTEstate implementation for Stellar/Soroban.

The existing BOTEstate implementation should be treated as a **reference for prior business logic**, not as the final KeystoneGrid protocol specification.

The current implementation should be considered experimental until the project explicitly declares a production release.

**Do not use experimental contracts with real funds or real-world financial assets.**

---

# Related Repositories

### Documentation

`Keystone-Docs`

https://github.com/KeystoneGrid/Keystone-Docs

### Backend

`Keystone-Backend`

https://github.com/KeystoneGrid/Keystone-Backend

### Frontend

`Keystone-Frontend`

https://github.com/KeystoneGrid/Keystone-Frontend

### Organization

`KeystoneGrid`

https://github.com/KeystoneGrid

---

# License

KeystoneGrid is an open-source project.

The applicable license is defined by the repository's license file.

Contributors should review the project's license and contribution terms before submitting code.

---

# Final Principle

The contract layer exists to provide guarantees that should not depend on a company's database, API, or frontend.

The goal is not to put the entire KeystoneGrid application on-chain.

The goal is to put the **right guarantees on-chain**.

> **KeystoneGrid contracts should make ownership, settlement, authorization, and protocol state verifiable without requiring users to trust the application layer.**
