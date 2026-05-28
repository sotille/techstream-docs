# TechStream Framework Index

The TechStream DevSecOps framework portfolio is a public, Apache 2.0-licensed collection of ten interconnected reference frameworks for secure software delivery.

## The ten frameworks

| # | Framework | Purpose |
|---|---|---|
| 1 | [devsecops-framework](https://github.com/sotille/devsecops-framework) | Core DevSecOps lifecycle (8 phases) + security controls across the pipeline |
| 2 | [secure-ci-cd-reference-architecture](https://github.com/sotille/secure-ci-cd-reference-architecture) | Reference architecture for securing CI/CD pipelines |
| 3 | [software-supply-chain-security-framework](https://github.com/sotille/software-supply-chain-security-framework) | SBOM, Sigstore/Cosign, SLSA, supply chain integrity |
| 4 | [compliance-automation-framework](https://github.com/sotille/compliance-automation-framework) | Automated compliance (SOC2/ISO/PCI/NIST 800-53) via OPA/Kyverno |
| 5 | [devsecops-methodology](https://github.com/sotille/devsecops-methodology) | 4-phase transformation methodology (Assess/Design/Implement/Optimize) |
| 6 | [devsecops-maturity-model](https://github.com/sotille/devsecops-maturity-model) | TechStream Maturity Model: 5 levels, 37 questions, 8 domains |
| 7 | [release-orchestration-framework](https://github.com/sotille/release-orchestration-framework) | Enterprise release management (blue/green, canary, rollback automation) |
| 8 | [techstream-docs](https://github.com/sotille/techstream-docs) | Master documentation portal (this repository) |
| 9 | [ai-devsecops-framework](https://github.com/sotille/ai-devsecops-framework) | AI/agentic systems security (prompt injection, agent authz, Auditor Agent pattern) |
| 10 | [forensics-and-incident-response-framework](https://github.com/sotille/forensics-and-incident-response-framework) | DFIR for ephemeral CI/CD + AI/agentic systems |

## Framework relationships

```
                  techstream-docs (you are here)
                          |
        +-----------------+------------------+
        |                                     |
   FOUNDATION                          ADVANCED / EMERGING
        |                                     |
+-------+-------+              +--------------+-------------+
|               |              |                            |
devsecops-     devsecops-      ai-devsecops-       forensics-and-
framework      methodology     framework            incident-response-
   |              |             ↕ (Auditor Agent)   framework
secure-ci-cd-    devsecops-                              ↑
reference-     maturity-model                            |
architecture                                             |
   |                                                     |
software-      compliance-       release-                |
supply-        automation-       orchestration-          |
chain-         framework         framework               |
security-                                                |
framework                                                |
   ↕ (incident response feeds back into supply chain)----+
```

## Federal-standards alignment (portfolio-level)

| Standard | Primarily addressed by |
|---|---|
| Executive Order 14028 | software-supply-chain-security-framework, secure-ci-cd-reference-architecture |
| Executive Order 14306 | (all frameworks — extends 14028) |
| NIST SP 800-218 (SSDF) | devsecops-framework, devsecops-methodology |
| NIST SP 1800-44 (DevSecOps Practices, preliminary draft) | (all frameworks) |
| DoD DevSecOps Fundamentals v2.5 | devsecops-framework, devsecops-methodology |
| NIST CSF 2.0 | release-orchestration-framework, forensics-and-incident-response-framework |
| NIST SP 800-53, FedRAMP | compliance-automation-framework |
| NIST SP 800-86 | forensics-and-incident-response-framework |
| NIST AI 100-1 (AI RMF), OWASP LLM Top 10, EU AI Act | ai-devsecops-framework |
| SLSA framework, CISA Secure-by-Design | software-supply-chain-security-framework |

## Adoption sequences by organizational profile

See [adoption-sequences.md](./adoption-sequences.md) for recommended sequences based on organizational starting point.

## Publication series

Practitioner-focused articles distilling the methodologies in this portfolio are published on Medium:

- [The 4-Phase DevSecOps Transformation: A 90-Day Journey from Policy to Practice](https://medium.com/@fsotille/the-4-phase-devsecops-transformation-b9df2ef2e051) (April 2026)
- [The Four Layers of Software Supply Chain Integrity: Why Most SBOMs Are Theater](https://medium.com/@fsotille/the-four-layers-of-software-supply-chain-integrity-995824dbead0) (May 2026)
- "Why Your AI Agent Is the Next SolarWinds: Supply Chain Security for the Agentic Era" (Medium, May 2026)
