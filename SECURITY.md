# Security Policy

## Security at KeystoneGrid

Security is a core requirement of KeystoneGrid.

The project includes smart contracts, ownership logic, investment and settlement workflows, compliance enforcement, and revenue distribution. Vulnerabilities in these systems may result in loss of funds, incorrect ownership state, unauthorized actions, or other serious consequences.

We therefore ask security researchers and contributors to report vulnerabilities responsibly.

## Supported Versions

Security fixes are generally prioritized for the current development branch and actively maintained releases.

Because KeystoneGrid is under active development, contributors should assume that unreleased protocol and application components may change.

## Reporting a Vulnerability

Please do not report security vulnerabilities through public GitHub issues.

Instead, report vulnerabilities privately through the security contact or private security reporting mechanism configured for the KeystoneGrid organization.

A useful report should include:

* A clear description of the vulnerability.
* Affected repository and component.
* Affected version or commit.
* Steps to reproduce.
* Proof of concept where appropriate.
* Potential impact.
* Suggested mitigation, if known.

Do not include private keys, credentials, personal information, or other secrets in the report.

## Smart Contract Vulnerabilities

Smart contract reports should additionally include, where applicable:

* Contract function involved.
* Relevant transaction or invocation sequence.
* Preconditions required for exploitation.
* Expected protocol behavior.
* Actual behavior.
* Potential financial or ownership impact.
* Whether the issue can be reproduced on a local test environment.

Critical contract vulnerabilities should be treated as high priority.

## Severity

Security reports may be classified approximately as:

### Critical

Issues that can result in significant unauthorized control, large-scale fund loss, systemic ownership corruption, or protocol compromise.

### High

Issues that can cause substantial financial loss, unauthorized asset operations, privilege escalation, or serious integrity violations.

### Medium

Issues with meaningful but constrained impact, including limited authorization bypasses or denial-of-service conditions.

### Low

Issues with limited security impact, hardening opportunities, or defense-in-depth improvements.

Severity may be adjusted after technical investigation.

## Responsible Disclosure

Please allow maintainers reasonable time to investigate and address a vulnerability before publicly disclosing technical exploitation details.

Maintainers may coordinate disclosure timing with affected contributors and researchers.

## Security Principles

Contributors should follow these principles:

* Never trust user-controlled input.
* Validate authorization at the protocol boundary.
* Do not treat backend state as authoritative for on-chain ownership.
* Protect against replay and duplicate operations.
* Validate asset lifecycle transitions.
* Enforce supply constraints.
* Prevent unauthorized distribution claims.
* Avoid leaking sensitive information.
* Never commit credentials or private keys.
* Test failure and adversarial paths, not only successful paths.

## Security-Sensitive Changes

Changes involving the following areas require additional review:

* Authorization
* Asset issuance
* Ownership
* Transfers
* Investment settlement
* Payment handling
* Distribution claims
* Administrative controls
* Compliance enforcement
* Contract upgrades
* Emergency controls

## Disclosure

After a vulnerability has been addressed, the project may publish a security advisory describing the issue, impact, affected versions, and remediation.

Sensitive exploit details will not be disclosed unnecessarily.

## Contact

Use the private security reporting mechanism configured on the KeystoneGrid GitHub organization for vulnerability reports.
