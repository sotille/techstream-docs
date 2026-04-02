# Techstream Official Documentation Portal

> The central reference for Techstream's DevSecOps frameworks, methodologies, and implementation guidance.

---

## About Techstream

Techstream is a DevSecOps engineering and advisory organization dedicated to helping software organizations deliver secure software at the speed that modern business demands. Techstream develops and maintains a suite of open-source frameworks, reference architectures, and implementation guides that enable engineering teams to integrate security deeply into their development, delivery, and operations practices—without sacrificing velocity.

This documentation portal is the authoritative entry point for all Techstream frameworks and resources. Whether you are starting a DevSecOps journey from scratch, seeking to mature an existing security program, or looking for specific technical implementation guidance, this portal will direct you to the right resource.

---

## What Techstream Does

Techstream operates at the intersection of security engineering and software delivery. Our work spans three primary areas:

**Framework Development**: We develop and maintain open-source frameworks that encode security best practices as structured, implementable guidance. These frameworks distill lessons from hundreds of real-world implementations into reusable patterns that any engineering organization can adopt.

**Advisory and Implementation**: We work directly with engineering organizations to assess their current state, design tailored DevSecOps programs, and implement the controls and tooling that close the gap between current and desired security posture.

**Community and Education**: We contribute to the broader DevSecOps community through open-source tooling, technical content, conference participation, and active engagement with industry standards bodies.

---

## Framework Repository Index

The following repositories comprise the Techstream Framework Suite:

| Repository | Description | Status |
|---|---|---|
| [devsecops-framework](https://github.com/sotille/devsecops-framework) | Core DevSecOps principles, practices, and organizational model | Active |
| [devsecops-methodology](https://github.com/sotille/devsecops-methodology) | Step-by-step methodology for DevSecOps transformation | Active |
| [devsecops-maturity-model](https://github.com/sotille/devsecops-maturity-model) | Maturity assessment model for DevSecOps programs | Active |
| [secure-ci-cd-reference-architecture](https://github.com/sotille/secure-ci-cd-reference-architecture) | Reference architecture for secure CI/CD pipelines | Active |
| [secure-pipeline-templates](https://github.com/sotille/secure-pipeline-templates) | Reusable pipeline templates for common security patterns | Active |
| [software-supply-chain-security-framework](https://github.com/sotille/software-supply-chain-security-framework) | Comprehensive guidance for software supply chain security | Active |
| [compliance-automation-framework](https://github.com/sotille/compliance-automation-framework) | Automated compliance controls and evidence collection | Active |
| [release-orchestration-framework](https://github.com/sotille/release-orchestration-framework) | Secure release management and deployment orchestration | Active |
| [cloud-security-devsecops](https://github.com/sotille/cloud-security-devsecops) | Cloud security integrated with DevSecOps practices | Active |
| [techstream-docs](https://github.com/sotille/techstream-docs) | This documentation portal | Active |

---

## How to Navigate the Documentation

### By Role

**Engineering Leadership / Architects**
Start with [Introduction](docs/introduction.md) to understand the Techstream philosophy, then review [Architecture](docs/architecture.md) to understand how frameworks interconnect. Use [Roadmap](docs/roadmap.md) for program planning and investment guidance.

**Security Engineers**
Begin with [Framework](docs/framework.md) for the complete framework portfolio, then navigate to the specific framework repositories that address your immediate domain (e.g., [cloud-security-devsecops](https://github.com/sotille/cloud-security-devsecops) for cloud security, [software-supply-chain-security-framework](https://github.com/sotille/software-supply-chain-security-framework) for supply chain).

**Platform / DevOps Engineers**
Start with [secure-ci-cd-reference-architecture](https://github.com/sotille/secure-ci-cd-reference-architecture) and [secure-pipeline-templates](https://github.com/sotille/secure-pipeline-templates) for immediate, practical pipeline guidance. Reference [Architecture](docs/architecture.md) for system-level integration context.

**Development Teams**
Start with [devsecops-framework](https://github.com/sotille/devsecops-framework) for the foundational principles, then [secure-pipeline-templates](https://github.com/sotille/secure-pipeline-templates) for integration with your existing development workflows.

**Compliance / GRC Teams**
Focus on [compliance-automation-framework](https://github.com/sotille/compliance-automation-framework) and the compliance sections of [cloud-security-devsecops](https://github.com/sotille/cloud-security-devsecops). Use [devsecops-maturity-model](https://github.com/sotille/devsecops-maturity-model) for audit-ready maturity assessment.

### By Use Case

| Use Case | Start Here |
|---|---|
| Starting a DevSecOps program from scratch | [devsecops-methodology](https://github.com/sotille/devsecops-methodology) |
| Assessing current DevSecOps maturity | [devsecops-maturity-model](https://github.com/sotille/devsecops-maturity-model) |
| Securing CI/CD pipelines | [secure-ci-cd-reference-architecture](https://github.com/sotille/secure-ci-cd-reference-architecture) |
| Implementing cloud security controls | [cloud-security-devsecops](https://github.com/sotille/cloud-security-devsecops) |
| Automating compliance | [compliance-automation-framework](https://github.com/sotille/compliance-automation-framework) |
| Securing the software supply chain | [software-supply-chain-security-framework](https://github.com/sotille/software-supply-chain-security-framework) |
| Managing secure releases | [release-orchestration-framework](https://github.com/sotille/release-orchestration-framework) |
| Understanding the full framework ecosystem | [Framework](docs/framework.md) (this portal) |

---

## Documentation Structure

```
techstream-docs/
├── README.md                    ← This file (portal entry point)
├── LICENSE                      ← Apache 2.0
└── docs/
    ├── introduction.md          ← About Techstream, mission, values, philosophy
    ├── architecture.md          ← Framework ecosystem map, integration patterns
    ├── framework.md             ← Complete framework portfolio guide
    ├── implementation.md        ← Adoption guidance, engagement model
    ├── best-practices.md        ← Meta best practices for framework adoption
    └── roadmap.md               ← Future roadmap, glossary, documentation index
```

---

## Contributing

Techstream frameworks are open-source and community contributions are welcome. To contribute:

1. Review the [Best Practices](docs/best-practices.md) guide for framework contribution guidelines
2. Open an issue describing the proposed change or addition before submitting a pull request
3. Ensure all contributions include appropriate documentation
4. Follow the established writing style: professional, precise, and free of marketing language
5. Include rationale for all recommendations — opinions without justification are not acceptable in Techstream frameworks

For significant contributions (new sections, new frameworks, architectural changes), engage with the Techstream community through the GitHub Discussions forum before investing significant effort.

---

## License

Copyright 2024 Techstream

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for the full license text.

All Techstream framework repositories are licensed under the Apache License 2.0 unless explicitly noted otherwise.
