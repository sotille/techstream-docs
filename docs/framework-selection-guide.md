# Framework Selection Guide

This guide helps practitioners identify which Techstream frameworks to adopt, in what order, and how to scope the initial engagement based on organization size, maturity, and delivery context.

---

## How to Use This Guide

Start by identifying your primary driver — the most pressing organizational need that brought you here. Then follow the recommended sequencing for your context. Each path leads to a concrete starting point and a natural progression through the ecosystem.

---

## Primary Driver Decision Tree

```
What is your most urgent need?
│
├─ We need to pass a compliance audit (SOC 2, PCI-DSS, ISO 27001, FedRAMP)
│   └─ Start with: compliance-automation-framework
│       Then add: devsecops-maturity-model → devsecops-framework
│
├─ We had (or nearly had) a supply chain incident
│   └─ Start with: software-supply-chain-security-framework
│       Then add: secure-pipeline-templates → secure-ci-cd-reference-architecture
│
├─ We want to embed security into our CI/CD pipelines right now
│   └─ Start with: secure-pipeline-templates
│       Then add: secure-ci-cd-reference-architecture → devsecops-framework
│
├─ We are starting a formal DevSecOps transformation program
│   └─ Start with: devsecops-methodology
│       Then add: devsecops-maturity-model → devsecops-framework
│
├─ We need to understand where we are before we can plan
│   └─ Start with: devsecops-maturity-model
│       Then add: devsecops-methodology → devsecops-framework
│
├─ We are hardening a Kubernetes or multi-cloud platform
│   └─ Start with: cloud-security-devsecops
│       Then add: secure-ci-cd-reference-architecture → compliance-automation-framework
│
└─ We want to improve release governance and reduce deployment risk
    └─ Start with: release-orchestration-framework
        Then add: secure-pipeline-templates → devsecops-framework
```

---

## Sequencing by Organization Type

### Startup (< 50 engineers, pre-Series B)

**Goal:** Establish security hygiene without slowing down product delivery.

**Recommended sequence:**

| Step | Framework | Focus |
|------|-----------|-------|
| 1 | `secure-pipeline-templates` | Deploy the GitHub Actions template; adopt SAST, SCA, and secrets scanning immediately |
| 2 | `software-supply-chain-security-framework` | Enable SBOM generation and dependency pinning |
| 3 | `devsecops-framework` | Establish shared vocabulary, coding standards, and a lightweight security champions program |
| 4 | `compliance-automation-framework` | Automate evidence collection early to simplify future SOC 2 audits |

**Milestone:** In 90 days, a startup should have all builds gated on CRITICAL/HIGH vulnerabilities, artifacts signed, and a basic compliance evidence trail.

---

### Scale-Up (50–500 engineers, multiple product teams)

**Goal:** Standardize security practices across teams; reduce security-as-bottleneck patterns.

**Recommended sequence:**

| Step | Framework | Focus |
|------|-----------|-------|
| 1 | `devsecops-maturity-model` | Assess current state; identify gaps across teams |
| 2 | `devsecops-methodology` | Run a structured transformation program; assign security champions |
| 3 | `secure-ci-cd-reference-architecture` | Design a standard pipeline architecture; retire ad hoc pipelines |
| 4 | `secure-pipeline-templates` | Provide reference pipelines that teams can adopt |
| 5 | `release-orchestration-framework` | Add release governance as the team count grows |
| 6 | `cloud-security-devsecops` | Harden cloud infrastructure as platform complexity increases |

**Milestone:** At 6 months, all product pipelines should use the standard template, maturity assessment scores should be trending upward, and a release governance model should be in place.

---

### Enterprise (500+ engineers, regulated industry or multi-cloud)

**Goal:** Operate a coherent, measurable, audit-ready DevSecOps program at scale.

**Recommended sequence:**

| Step | Framework | Focus |
|------|-----------|-------|
| 1 | `devsecops-methodology` | Executive sponsor alignment, pilot team selection, transformation governance |
| 2 | `devsecops-maturity-model` | Baseline assessment across business units; track improvement quarterly |
| 3 | `devsecops-framework` | Establish organization-wide policies, toolchain standards, and governance |
| 4 | `secure-ci-cd-reference-architecture` | Reference architecture for approved pipeline patterns; review board process |
| 5 | `compliance-automation-framework` | Automate evidence across all applicable compliance frameworks |
| 6 | `software-supply-chain-security-framework` | SBOM program, SLSA enforcement, vendor assessment |
| 7 | `cloud-security-devsecops` | Cloud-specific hardening and CSPM integration |
| 8 | `release-orchestration-framework` | Release governance, CAB integration, DORA instrumentation |
| 9 | `secure-pipeline-templates` | Centrally maintained templates rolled out across BUs |

**Milestone:** At 12 months, DORA metrics should be improving, compliance evidence collection should be continuous, and the maturity model should show measurable progression.

---

### Regulated Industry (financial services, healthcare, government)

**Goal:** Meet mandatory compliance requirements while modernizing delivery practices.

**Recommended sequence:**

| Step | Framework | Focus |
|------|-----------|-------|
| 1 | `compliance-automation-framework` | Map current requirements; automate evidence collection immediately |
| 2 | `devsecops-maturity-model` | Identify gaps relative to regulatory minimum controls |
| 3 | `software-supply-chain-security-framework` | SBOM, SLSA, and provenance to address EO 14028 / EU CRA requirements |
| 4 | `secure-ci-cd-reference-architecture` | Validated pipeline architecture aligned with FedRAMP / PCI-DSS controls |
| 5 | `secure-pipeline-templates` | Auditable, reproducible pipeline configurations |
| 6 | `release-orchestration-framework` | Change management integration, audit trails, separation of duties |
| 7 | `cloud-security-devsecops` | Cloud control alignment with CIS Benchmarks, NIST 800-53 |
| 8 | `devsecops-framework` | Formalize security architecture governance |

---

## Framework Scope Boundaries

Understanding where each framework begins and ends prevents duplication of effort and overlapping tool deployments.

| Framework | In Scope | Out of Scope |
|-----------|----------|--------------|
| **devsecops-framework** | Principles, controls catalog, toolchain reference, lifecycle model | Specific pipeline configuration files |
| **secure-ci-cd-reference-architecture** | Pipeline architecture patterns, threat model, IAM, network zones | Cloud infrastructure architecture outside the pipeline |
| **secure-pipeline-templates** | Ready-to-deploy pipeline configurations (YAML, Groovy) | Organization-specific customization beyond documented parameters |
| **software-supply-chain-security-framework** | SBOM, SLSA, artifact signing, dependency security, vendor assessment | Runtime workload security |
| **release-orchestration-framework** | Approval workflows, environment promotion, rollback, DORA metrics | Developer workflow (pre-merge) |
| **devsecops-maturity-model** | Capability assessment, scoring, roadmap generation | Prescriptive tool mandates |
| **devsecops-methodology** | Transformation program, change management, training | Technical architecture decisions |
| **compliance-automation-framework** | Policy as Code, evidence collection, multi-framework mapping | Legal interpretation of regulatory requirements |
| **cloud-security-devsecops** | Cloud-specific IAM, network, workload, container, Kubernetes security | Application-level security (covered by devsecops-framework) |
| **techstream-docs** | Framework index, integration scenarios, selection guidance | Individual framework depth |

---

## When NOT to Use a Framework

Some frameworks require organizational readiness that may not exist yet. Forcing adoption before the prerequisites are met creates friction without benefit.

| Framework | Minimum Prerequisites |
|-----------|-----------------------|
| `compliance-automation-framework` | At least one defined compliance target; IaC-managed infrastructure |
| `software-supply-chain-security-framework` | Working CI/CD pipelines with artifact promotion |
| `release-orchestration-framework` | Multiple deployment environments; more than one product team deploying independently |
| `devsecops-maturity-model` | Engineering leadership willing to act on assessment findings |
| `cloud-security-devsecops` | Cloud infrastructure already in use; IAM administrator access for control deployment |

If prerequisites are missing, start with a lighter-weight framework and revisit once the foundational layer is in place.

---

## Cross-Framework Interaction Map

Some frameworks produce outputs that are consumed by other frameworks. Understanding these data flows prevents integration blind spots.

```
devsecops-framework
    ↓ principles and controls catalog
    ↓
    ├─→ secure-ci-cd-reference-architecture
    │       ↓ pipeline architecture decisions
    │       ↓
    │       └─→ secure-pipeline-templates (pipeline configuration)
    │               ↓ signed artifacts and attestations
    │               ↓
    │               ├─→ software-supply-chain-security-framework (SBOM, SLSA)
    │               └─→ release-orchestration-framework (promotion gates)
    │
    ├─→ cloud-security-devsecops
    │       ↓ cloud control outputs (audit logs, scan results)
    │       ↓
    │       └─→ compliance-automation-framework (evidence ingestion)
    │
    └─→ devsecops-methodology
            ↓ transformation program
            ↓
            └─→ devsecops-maturity-model (assessment baseline and tracking)
```

---

## Adoption Patterns by Role

### For a CISO or VP of Engineering

- Start with `devsecops-maturity-model` to establish baseline.
- Use `devsecops-methodology` to scope and fund the transformation.
- Use `compliance-automation-framework` to address audit commitments.
- Report on DORA metrics from `release-orchestration-framework`.

### For a Platform Engineering Team

- Adopt `secure-pipeline-templates` as the standard build platform.
- Use `secure-ci-cd-reference-architecture` as the authoritative design reference.
- Integrate `cloud-security-devsecops` for Kubernetes and cloud infrastructure hardening.
- Use `software-supply-chain-security-framework` to build SBOM and signing infrastructure.

### For an Application Security Team

- Use `devsecops-framework` to define controls and toolchain policy.
- Use `secure-ci-cd-reference-architecture` to review pipeline designs.
- Contribute to `compliance-automation-framework` policy libraries.
- Run assessments using `devsecops-maturity-model`.

### For a Development Lead

- Start with `secure-pipeline-templates` to adopt security gates immediately.
- Use `devsecops-framework` best practices for secure coding guidance.
- Use `software-supply-chain-security-framework` implementation guide for dependency management.
- Consult `release-orchestration-framework` for deployment and rollback patterns.

---

## Related Documents

- [Ecosystem Architecture](architecture.md) — Framework dependency map and platform reference architecture
- [Integration Scenarios](integration-scenarios.md) — End-to-end walkthroughs for six organizational contexts
- [techstream-docs README](../README.md) — Quick start and framework index
