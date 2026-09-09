# Contributing to KeystoneGrid

Thank you for your interest in contributing to KeystoneGrid.

KeystoneGrid is an open-source infrastructure project for building secure, verifiable, and composable real-world asset applications on Stellar.

We welcome contributions in smart contracts, backend infrastructure, frontend applications, documentation, testing, security, developer tooling, and protocol research.

## Our Development Model

KeystoneGrid follows an RFC-driven, issue-based development process.

```text
RFC / Specification
        ↓
GitHub Issue
        ↓
Contributor Assignment
        ↓
Implementation
        ↓
Tests
        ↓
Pull Request
        ↓
Review
        ↓
Merge
```

Protocol behavior should be defined before implementation begins.

Contributors should not introduce major architectural or protocol changes through an implementation PR without first discussing the change through the appropriate RFC or issue.

## Before You Start

Please:

1. Read the repository README.
2. Review the relevant RFCs and specifications in `Keystone-Docs`.
3. Search existing issues and pull requests.
4. Check whether the issue is already assigned.
5. Comment on an issue if you would like to work on it.
6. Wait for maintainer assignment when the issue requires coordination.

For larger changes, discuss the proposed approach with maintainers before beginning implementation.

## Issues

Issues are the primary unit of contributor work.

A good implementation issue should define:

* Problem
* Context
* Scope
* Dependencies
* Relevant RFCs
* Acceptance criteria
* Testing requirements
* Security considerations

Contributors are expected to implement the requirements described by the assigned issue.

If implementation reveals that the specification is incomplete or incorrect, stop and discuss the discrepancy rather than silently changing protocol behavior.

## Assignment

Please do not begin substantial work on an issue that is already assigned to another contributor.

For issues marked `good first issue`, contributors may express interest directly in the issue.

Maintainers may assign issues based on:

* Contributor interest
* Relevant experience
* Previous contributions
* Issue complexity
* Current project priorities

## Pull Requests

Every pull request should:

* Address a specific issue.
* Explain what changed.
* Reference the relevant issue.
* Reference relevant RFCs or specifications.
* Include appropriate tests.
* Update documentation when required.
* Avoid unrelated changes.
* Pass all required CI checks.

Use a focused PR rather than combining several unrelated features.

## Commit Guidelines

Write clear and meaningful commit messages.

Prefer:

```text
Add asset registration validation
Fix distribution claim authorization
Add lifecycle transition tests
```

Avoid vague messages such as:

```text
update
changes
fix stuff
work
```

## Testing

Contributors are responsible for testing their changes.

Depending on the repository, this may include:

* Unit tests
* Integration tests
* Contract tests
* API tests
* Frontend tests
* Security tests
* Invariant tests
* Regression tests

Changes affecting protocol behavior should include tests demonstrating both successful and rejected operations.

## Security

Security-sensitive changes require additional care.

Do not include:

* Private keys
* Seed phrases
* Credentials
* API secrets
* Personal information
* Production secrets

in source code, commits, issues, or pull requests.

If you discover a security vulnerability, do not disclose it publicly through a normal GitHub issue. Follow the repository's `SECURITY.md` policy.

## Architecture and RFCs

KeystoneGrid uses RFCs to establish protocol-level decisions.

RFCs are authoritative for accepted protocol behavior.

If your implementation requires a change to:

* Ownership
* Asset lifecycle
* Compliance
* Verification
* Investment
* Settlement
* Distribution
* Token behavior
* Secondary markets
* Security assumptions

raise the architectural issue before implementing the change.

## Review Process

All contributions are reviewed before merging.

Maintainers may request:

* Code changes
* Additional tests
* Documentation
* Security analysis
* Architectural clarification
* Performance improvements

Approval does not guarantee immediate merge. Maintainers may prioritize work according to project milestones and dependencies.

## Contributor Expectations

Contributors are expected to:

* Communicate clearly.
* Respect other contributors.
* Keep changes focused.
* Follow project architecture.
* Write tests.
* Respond to review feedback.
* Avoid unnecessary breaking changes.
* Respect the project's Code of Conduct.

## Good First Contributions

New contributors can start with:

* Documentation improvements
* Test coverage
* Developer tooling
* Small bug fixes
* Issue reproduction
* Examples
* Good first issues

Look for issues labelled:

`good first issue`

or

`help wanted`

## License

By contributing to KeystoneGrid, you agree that your contributions are provided under the license specified by the repository.
