# Contributing to Techstream Documentation (Central Portal)

Thank you for your interest in contributing to the Techstream Documentation portal. This repository serves as the central documentation hub and navigation layer for the entire Techstream Framework Suite. It provides the ecosystem overview, adoption guidance, implementation sequences, professional services information, and the cross-framework glossary and index. Contributions that improve navigation, clarify framework relationships, and keep the adoption guidance current are welcome.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [What We Welcome](#what-we-welcome)
- [What We Do Not Accept](#what-we-do-not-accept)
- [How to Contribute](#how-to-contribute)
- [Documentation Standards](#documentation-standards)
- [Scope Boundary](#scope-boundary)
- [Review Process](#review-process)
- [License](#license)

---

## Code of Conduct

All contributors are expected to engage professionally and constructively. Contributions that are dismissive, personal, or unprofessional will not be reviewed.

---

## What We Welcome

- **Navigation and discoverability improvements** — the portal's primary purpose is helping readers find the right framework for their need. Structural improvements, better role-based navigation, and clearer entry points are valuable.
- **Adoption sequence updates** — as new frameworks are added to the suite or existing ones evolve, updates to the recommended adoption sequences for different organizational profiles may be needed.
- **Cross-framework glossary additions** — terms used consistently across the Techstream suite that are not yet defined in the glossary.
- **Implementation scenario additions** — concrete end-to-end implementation scenarios (e.g., "regulated financial services startup implementing in 12 months") that synthesize multiple frameworks.
- **Professional services and training content** — updates to the engagement model, training curriculum descriptions, or community resource information.
- **Roadmap updates** — as the Techstream suite evolves, the roadmap section needs to reflect current priorities and planned framework additions.
- **Index and documentation index improvements** — new entries in the documentation index, corrections to existing entries, or improvements to the organization of the index.

---

## What We Do Not Accept

- **Framework-specific technical content** — technical implementation details belong in the specific framework repository, not in the portal. The portal should link to frameworks, not duplicate their content.
- **Vendor promotional content** — all tool and service references must be accurate and balanced.
- **Major navigation restructuring** without prior issue discussion and consensus — the portal navigation structure affects all users' experience of the entire suite.

---

## Scope Boundary

This repository covers: ecosystem overview, framework relationships, adoption guidance, professional services, training, community, and the cross-framework glossary and index.

Technical content (implementation guides, configuration examples, toolchain specifications) belongs in the specific framework repositories:
- CI/CD security → secure-ci-cd-reference-architecture
- Cloud security → cloud-security-devsecops
- Supply chain security → software-supply-chain-security-framework
- Compliance automation → compliance-automation-framework
- Release governance → release-orchestration-framework
- Pipeline templates → secure-pipeline-templates
- Transformation methodology → devsecops-methodology
- Maturity assessment → devsecops-maturity-model
- Foundational principles → devsecops-framework

---

## How to Contribute

### Reporting Issues

Use GitHub Issues to report: broken links to framework repositories, outdated adoption sequence guidance, framework relationship inaccuracies, glossary gaps or errors, or documentation index omissions.

### Submitting Pull Requests

1. Fork and branch from `main` with a descriptive name.
2. Verify that all cross-repository links are working (use relative paths where possible, full GitHub URLs where necessary).
3. Ensure new content does not duplicate content that exists in a specific framework repository — link instead of copy.
4. Open a pull request with a description of what changed, why, and the intended audience for the change.

---

## Documentation Standards

- Accessible tone — the portal is often the first point of contact for practitioners new to the Techstream suite. Write for practitioners who may not yet know which framework they need.
- Mermaid diagrams for ecosystem maps, framework dependency graphs, and adoption sequence timelines.
- Role-based navigation language (CISO, Security Architect, DevSecOps Lead, Platform Engineer, Compliance Officer).
- ATX headers, relative internal links, fenced code blocks with language identifiers.

---

## Review Process

Pull requests are reviewed for scope alignment, accuracy of framework relationship descriptions, link validity, and documentation standards. Initial responses within 5 business days.

---

## License

By contributing, you agree your contributions will be licensed under the [Apache License 2.0](LICENSE).
