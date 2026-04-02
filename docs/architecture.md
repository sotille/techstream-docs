# Techstream Documentation Architecture and Framework Ecosystem

## Overview

The Techstream Framework Suite is not a collection of independent documents. It is a carefully interconnected ecosystem where each framework builds on, integrates with, and references others. Understanding the relationships between frameworks is essential for effective adoption: implementing frameworks in the wrong sequence creates gaps, and implementing them in isolation misses the integration points that multiply their effectiveness.

This document maps the framework ecosystem, describes the relationships between frameworks, and provides sequencing guidance for different organizational profiles.

---

## Framework Ecosystem Map

The Techstream frameworks are organized into four functional layers:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         DOCUMENTATION PORTAL                            │
│                        techstream-docs (this repo)                      │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
            ┌───────────────────────┼───────────────────────┐
            │                       │                       │
            ▼                       ▼                       ▼
┌───────────────────┐   ┌───────────────────┐   ┌───────────────────────┐
│    CORE LAYER     │   │  METHODOLOGY LAYER│   │  MEASUREMENT LAYER    │
│                   │   │                   │   │                       │
│  devsecops-       │   │  devsecops-       │   │  devsecops-           │
│  framework        │   │  methodology      │   │  maturity-model       │
│                   │   │                   │   │                       │
│  Foundational     │   │  Transformation   │   │  Assessment &         │
│  principles,      │   │  playbook,        │   │  KPI framework        │
│  culture, and     │   │  change           │   │  for all domains      │
│  organizational   │   │  management       │   │                       │
│  model            │   │                   │   │                       │
└────────┬──────────┘   └────────┬──────────┘   └──────────┬────────────┘
         │                       │                          │
         └───────────────────────┴──────────────────────────┘
                                 │
         ┌───────────────────────┼──────────────────────────┐
         │                       │                          │
         ▼                       ▼                          ▼
┌───────────────────┐  ┌──────────────────────┐  ┌──────────────────────┐
│  TECHNICAL LAYER  │  │  TECHNICAL LAYER      │  │  TECHNICAL LAYER     │
│                   │  │                       │  │                      │
│  secure-ci-cd-    │  │  secure-pipeline-     │  │  cloud-security-     │
│  reference-       │  │  templates            │  │  devsecops           │
│  architecture     │  │                       │  │                      │
│                   │  │  Ready-to-use         │  │  Cloud security      │
│  CI/CD security   │  │  pipeline             │  │  controls for        │
│  design patterns  │  │  components           │  │  AWS, Azure, GCP     │
└───────────────────┘  └──────────────────────┘  └──────────────────────┘

┌───────────────────────────────────────────────────────────────────────┐
│                       GOVERNANCE LAYER                                 │
│                                                                        │
│  ┌─────────────────────────────────┐  ┌─────────────────────────────┐ │
│  │  compliance-automation-         │  │  release-orchestration-     │ │
│  │  framework                      │  │  framework                  │ │
│  │                                 │  │                             │ │
│  │  Automated compliance controls  │  │  Secure release management  │ │
│  │  and evidence collection        │  │  and deployment governance  │ │
│  └─────────────────────────────────┘  └─────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────────────┐
│                      SUPPLY CHAIN LAYER                                │
│                                                                        │
│              software-supply-chain-security-framework                  │
│                                                                        │
│     Dependency security, artifact integrity, build system security,    │
│     SLSA compliance, SBOM generation, signing and verification         │
└───────────────────────────────────────────────────────────────────────┘
```

---

## Framework Descriptions and Relationships

### Core Layer: devsecops-framework

**Role in Ecosystem**: The philosophical and organizational foundation. All other frameworks derive their core principles from this one.

**What it provides**:
- The definition of DevSecOps as Techstream understands and practices it
- The organizational model for DevSecOps teams (responsibilities, structures, interfaces)
- The cultural principles that make technical controls effective
- The governance model for continuous security improvement
- The integration touchpoints with SDLC stages

**Depends on**: Nothing. This is the root framework.

**Required by**: All other technical and governance frameworks reference the principles established here.

**When to read it first**: Always. Before implementing any other framework, teams should have shared understanding of the foundational principles.

---

### Methodology Layer: devsecops-methodology

**Role in Ecosystem**: The transformation playbook. While the devsecops-framework describes what DevSecOps is, this framework describes how to get there.

**What it provides**:
- Organizational change management guidance for DevSecOps transformation
- Stakeholder engagement patterns (engineering, security, leadership, compliance)
- Phase-by-phase transformation roadmap
- Team structure and role definition during transformation
- Measurement and communication frameworks for the transformation journey

**Depends on**: devsecops-framework (principles and organizational model)

**Integrates with**: devsecops-maturity-model (for baseline assessment and milestone tracking), all technical frameworks (for phased implementation planning)

**When to use it**: When starting a DevSecOps program from organizational scratch, or when a program needs significant restructuring.

---

### Measurement Layer: devsecops-maturity-model

**Role in Ecosystem**: The assessment and measurement framework. Provides a consistent vocabulary and scoring system for evaluating DevSecOps maturity across all domains.

**What it provides**:
- Five-level maturity scale for each DevSecOps domain
- Assessment questionnaires and scoring rubrics
- KPI definitions and measurement guidance
- Maturity-to-investment correlation (what level requires what resources)
- Evidence requirements for each maturity level (supporting audit/compliance use cases)

**Depends on**: devsecops-framework (defines the domains being measured)

**Integrates with**: All frameworks (each technical framework maps to specific maturity model domains)

**When to use it**: At the start of any DevSecOps initiative (baseline), at regular intervals during implementation (progress tracking), and for external reporting (audits, customer assessments, leadership reporting).

---

### Technical Layer: secure-ci-cd-reference-architecture

**Role in Ecosystem**: The CI/CD security design reference. Provides the architectural patterns for building a secure software delivery pipeline from scratch or hardening an existing one.

**What it provides**:
- Reference architecture diagrams for common CI/CD platforms (GitHub Actions, GitLab, Jenkins, CircleCI, Azure DevOps)
- Security control placement at each pipeline stage
- Pipeline credential security patterns
- Pipeline integrity verification
- Environment isolation and promotion patterns
- Branch protection and code review requirements

**Depends on**: devsecops-framework (principles), devsecops-methodology (for phased implementation context)

**Integrates with**:
- secure-pipeline-templates (provides reusable components that implement this architecture)
- cloud-security-devsecops (cloud-specific pipeline controls)
- software-supply-chain-security-framework (supply chain controls in the pipeline)
- compliance-automation-framework (compliance evidence collection from the pipeline)

---

### Technical Layer: secure-pipeline-templates

**Role in Ecosystem**: Ready-to-use pipeline components. While secure-ci-cd-reference-architecture describes how pipelines should be designed, this framework provides actual, implementable pipeline templates.

**What it provides**:
- Reusable GitHub Actions workflows for common security gates
- GitLab CI templates for security scanning stages
- Pre-built pipeline components for SAST, DAST, SCA, IaC scanning, image scanning, secret detection
- Template parameterization for different environments and risk profiles
- Integration patterns with common security tooling

**Depends on**: secure-ci-cd-reference-architecture (templates implement the reference architecture)

**Integrates with**: All technical frameworks (templates implement the pipeline-level controls defined in each framework)

**When to use it**: When building new pipelines or retrofitting security into existing ones. Teams can use templates directly and customize for their context.

---

### Technical Layer: cloud-security-devsecops

**Role in Ecosystem**: The cloud-specific security framework. Provides the cloud security controls, architectures, and implementations that are required for cloud-hosted workloads.

**What it provides**:
- Cloud security reference architectures (AWS, Azure, GCP)
- IAM and identity security controls and patterns
- Network security architecture (VPC, security groups, WAF)
- Kubernetes security hardening
- Container security (scanning, signing, runtime protection)
- Secrets management deployment and operation
- Cloud-specific compliance automation (Prowler, CSPM)
- Cloud incident response runbooks

**Depends on**: devsecops-framework (principles), secure-ci-cd-reference-architecture (pipeline integration points)

**Integrates with**:
- compliance-automation-framework (cloud compliance automation)
- software-supply-chain-security-framework (container image supply chain)
- secure-pipeline-templates (IaC scanning, container scanning templates)

---

### Governance Layer: compliance-automation-framework

**Role in Ecosystem**: The compliance automation framework. Transforms compliance from a periodic, manual, audit-driven activity into a continuous, automated, evidence-generating capability.

**What it provides**:
- Compliance-as-code patterns for common frameworks (SOC 2, ISO 27001, PCI DSS, HIPAA, FedRAMP)
- Automated evidence collection from CI/CD pipelines, cloud environments, and infrastructure
- Policy-as-Code implementations for compliance controls
- Audit trail design for continuous compliance evidence
- Mapping from technical controls to compliance framework requirements

**Depends on**: devsecops-framework (principles), secure-ci-cd-reference-architecture (pipeline integration)

**Integrates with**:
- cloud-security-devsecops (cloud compliance controls)
- devsecops-maturity-model (compliance maturity levels)
- release-orchestration-framework (compliance gates in release process)

---

### Governance Layer: release-orchestration-framework

**Role in Ecosystem**: The secure release management framework. Ensures that the transition from built artifact to running production system is governed, auditable, and reversible.

**What it provides**:
- Release gating patterns (security gates, approval workflows)
- Deployment strategy security considerations (blue-green, canary, feature flags)
- Rollback procedures and criteria
- Release audit trail requirements
- Change advisory board (CAB) integration for regulated industries
- Environment promotion security controls

**Depends on**: secure-ci-cd-reference-architecture (pipeline design)

**Integrates with**:
- compliance-automation-framework (release evidence for compliance)
- cloud-security-devsecops (cloud deployment security)

---

### Supply Chain Layer: software-supply-chain-security-framework

**Role in Ecosystem**: The software supply chain security framework. Addresses the security of all components that go into a software artifact: dependencies, build tools, base images, and the build process itself.

**What it provides**:
- SLSA (Supply Levels for Software Artifacts) framework implementation guidance
- Software Bill of Materials (SBOM) generation and management
- Dependency vulnerability management
- Build system security hardening
- Artifact signing and verification (Sigstore/Cosign)
- Third-party component vetting
- Dependency confusion attack prevention

**Depends on**: devsecops-framework (principles), secure-ci-cd-reference-architecture (build pipeline context)

**Integrates with**:
- cloud-security-devsecops (container image supply chain, registry security)
- secure-pipeline-templates (SCA scanning, image signing templates)
- compliance-automation-framework (SBOM as compliance evidence)

---

## Techstream DevSecOps Platform Reference Architecture

The following diagram depicts how all frameworks combine to form the Techstream DevSecOps Platform Reference Architecture—the end-state architecture that full framework adoption produces:

```
DEVELOPER ENVIRONMENT
┌─────────────────────────────────────────────────────────┐
│  IDE Security Plugins                                    │
│  Pre-commit hooks (secret scanning, IaC lint)            │
│  Local container scan                                    │
└──────────────────────────┬──────────────────────────────┘
                           │ Code Push
                           ▼
SOURCE CONTROL (GitHub / GitLab / Bitbucket)
┌─────────────────────────────────────────────────────────┐
│  Branch protection rules                                 │
│  Required code review (security champions)              │
│  CODEOWNERS for security-sensitive paths                 │
│  Secrets scanning (push protection)                      │
└──────────────────────────┬──────────────────────────────┘
                           │ PR/MR Trigger
                           ▼
CI PIPELINE  (secure-ci-cd-reference-architecture + secure-pipeline-templates)
┌─────────────────────────────────────────────────────────┐
│  Build                                                   │
│  ├── SAST (CodeQL, Semgrep, Bandit)                      │
│  ├── SCA (Dependabot, Snyk, OWASP Dependency-Check)      │
│  ├── Secret scanning (detect-secrets, GitLeaks)          │
│  └── License compliance check                           │
│                                                          │
│  Test                                                    │
│  ├── DAST (ZAP, Nuclei) on ephemeral environment        │
│  └── Security unit and integration tests                │
│                                                          │
│  Package                                                 │
│  ├── IaC scan (Checkov, TFSec)                          │
│  ├── Container image build                               │
│  ├── Image scan (Trivy, Grype)                          │
│  ├── Image sign (Cosign)                                │
│  └── SBOM generation (Syft, CycloneDX)                  │
└──────────────────────────┬──────────────────────────────┘
                           │ Artifacts + Attestations
                           ▼
ARTIFACT REGISTRY  (software-supply-chain-security-framework)
┌─────────────────────────────────────────────────────────┐
│  Access-controlled registry (ECR / ACR / Artifact Reg.)  │
│  Continuous registry scanning                           │
│  Signature verification on pull                         │
│  SBOM storage and indexing                              │
└──────────────────────────┬──────────────────────────────┘
                           │ Promotion Gate
                           ▼
RELEASE ORCHESTRATION  (release-orchestration-framework)
┌─────────────────────────────────────────────────────────┐
│  Security gate: all scans passed                        │
│  Compliance gate: evidence collected                    │
│  Approval workflow (for production)                     │
│  Deployment strategy selection                          │
└──────────────────────────┬──────────────────────────────┘
                           │ Deploy
                           ▼
CLOUD ENVIRONMENT  (cloud-security-devsecops)
┌─────────────────────────────────────────────────────────┐
│  Infrastructure (IaC - Terraform)                       │
│  ├── VPC with segmentation                              │
│  ├── IAM least-privilege roles                          │
│  ├── Security groups (automated)                        │
│  └── Secrets management (Vault / cloud-native)         │
│                                                          │
│  Kubernetes Cluster                                     │
│  ├── Admission controllers (OPA Gatekeeper / Kyverno)   │
│  ├── Pod Security Standards (restricted)                │
│  ├── Network policies (default-deny)                    │
│  ├── Falco runtime security                             │
│  └── Workload identity (IRSA / Workload Identity)       │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
DETECTION AND RESPONSE  (cross-framework)
┌─────────────────────────────────────────────────────────┐
│  Cloud-native threat detection (GuardDuty / Defender)   │
│  Centralized SIEM (CloudTrail + app logs + K8s audit)   │
│  CSPM continuous scanning (Prowler)                     │
│  Alert routing (PagerDuty / OpsGenie)                   │
│  SOAR playbooks (automated response)                    │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
COMPLIANCE AND GOVERNANCE  (compliance-automation-framework + devsecops-maturity-model)
┌─────────────────────────────────────────────────────────┐
│  Automated evidence collection (from all above)         │
│  Compliance posture dashboard                           │
│  KPI tracking (MTTD, MTTR, compliance score, etc.)     │
│  Audit-ready reporting                                  │
└─────────────────────────────────────────────────────────┘
```

---

## Framework Dependency Map

```
devsecops-framework (root, no dependencies)
    │
    ├── devsecops-methodology
    │       └── devsecops-maturity-model
    │
    ├── secure-ci-cd-reference-architecture
    │       ├── secure-pipeline-templates
    │       ├── cloud-security-devsecops
    │       │       ├── compliance-automation-framework
    │       │       └── software-supply-chain-security-framework
    │       ├── compliance-automation-framework
    │       │       └── release-orchestration-framework
    │       └── software-supply-chain-security-framework
    │
    └── devsecops-maturity-model (measures all above)
```

**Reading the dependency map**: A framework that appears below another depends on the one above. For example, `cloud-security-devsecops` depends on `secure-ci-cd-reference-architecture`, which depends on `devsecops-framework`. This means you should understand the pipeline architecture before implementing cloud-specific pipeline controls.

---

## Implementation Sequencing Guidance

Different organizational profiles should implement frameworks in different sequences based on their starting point, organizational structure, and delivery model.

### Sequence A: Startup / Early-Stage Organization

Organizations with small engineering teams, minimal security tooling, and greenfield infrastructure.

```
Phase 1 (Month 1-2):  devsecops-framework (principles alignment)
                      + devsecops-maturity-model (baseline assessment)
Phase 2 (Month 2-4):  secure-ci-cd-reference-architecture (pipeline design)
                      + secure-pipeline-templates (immediate pipeline tooling)
Phase 3 (Month 3-5):  cloud-security-devsecops (cloud security baseline)
Phase 4 (Month 5-8):  software-supply-chain-security-framework
Phase 5 (Month 7-10): compliance-automation-framework
Phase 6 (Month 9-12): release-orchestration-framework
```

**Rationale**: Startups benefit most from getting foundational controls right from the start. Pipeline security and cloud security are the highest-impact investments at this stage.

---

### Sequence B: Scale-Up / Growth-Stage Organization

Organizations with established but inconsistent security practices, multiple teams with different tooling, and mixed cloud and on-premises infrastructure.

```
Phase 1 (Month 1-2):  devsecops-maturity-model (critical: assess current state)
                      + devsecops-methodology (org change planning)
Phase 2 (Month 2-4):  devsecops-framework (principles alignment across teams)
Phase 3 (Month 3-6):  secure-ci-cd-reference-architecture (standardize pipelines)
                      + secure-pipeline-templates (fill gaps in existing pipelines)
Phase 4 (Month 5-8):  cloud-security-devsecops (cloud security hardening)
Phase 5 (Month 7-10): software-supply-chain-security-framework
Phase 6 (Month 9-12): compliance-automation-framework + release-orchestration-framework
```

**Rationale**: Scale-ups often have inconsistency as their core problem. Starting with assessment and methodology helps identify the highest-impact gaps. Framework standardization across teams is more important than implementing any single advanced framework.

---

### Sequence C: Enterprise Organization

Large organizations with existing security programs, compliance requirements, multiple business units with different technology stacks, and legacy systems.

```
Phase 1 (Month 1-3):  devsecops-maturity-model (enterprise-wide assessment)
                      + devsecops-methodology (transformation program design)
Phase 2 (Month 2-5):  compliance-automation-framework (compliance is typically urgent)
                      + devsecops-framework (principles alignment across BUs)
Phase 3 (Month 4-8):  secure-ci-cd-reference-architecture (reference arch for teams)
                      + cloud-security-devsecops (cloud migration security)
Phase 4 (Month 7-12): secure-pipeline-templates (standardize across BUs)
                      + release-orchestration-framework (governance requirements)
Phase 5 (Month 10-18):software-supply-chain-security-framework
```

**Rationale**: Enterprises often have compliance as a primary driver. Starting with compliance automation creates immediate value and builds organizational support for the broader transformation.

---

### Sequence D: Regulated Industry (Financial Services, Healthcare, Government)

Organizations subject to strict regulatory requirements (PCI DSS, HIPAA, FedRAMP, SOX, GDPR) where compliance evidence is a primary concern.

```
Phase 1 (Month 1-2):  devsecops-maturity-model (with compliance mapping)
                      + compliance-automation-framework (immediate compliance need)
Phase 2 (Month 2-5):  devsecops-framework + devsecops-methodology (program structure)
                      + release-orchestration-framework (change control requirements)
Phase 3 (Month 4-8):  secure-ci-cd-reference-architecture + cloud-security-devsecops
Phase 4 (Month 7-12): secure-pipeline-templates + software-supply-chain-security-framework
```

**Rationale**: Regulated industries face immediate compliance audit pressure. Leading with compliance automation provides immediate audit value while building toward full DevSecOps maturity.

---

## Integration Patterns Between Frameworks

### Pipeline → Cloud Integration

The CI/CD pipeline must integrate with cloud security controls at the deployment stage:

```yaml
# Example: Pipeline deploys via Terraform with security gates
deploy-to-production:
  needs: [security-scan, compliance-check, image-sign]
  environment: production
  steps:
    - name: IaC security scan
      run: checkov -d terraform/ --framework terraform --soft-fail-on LOW
    - name: Terraform plan with drift detection
      run: terraform plan -detailed-exitcode
    - name: Approval gate
      uses: trstringer/manual-approval@v1
    - name: Terraform apply
      run: terraform apply -auto-approve
```

### Supply Chain → Kubernetes Integration

Container image signing must integrate with Kubernetes admission control to form an end-to-end supply chain verification:

```
CI Pipeline: build → scan → sign with Cosign → push
                                                  │
                                                  ▼
Kubernetes Admission (OPA/Kyverno): verify Cosign signature
                                                  │
                                    PASS          │  FAIL
                                                  │
                                    Deploy        │  Block + Alert
```

### Compliance → All Frameworks Integration

The compliance framework collects evidence from all other frameworks automatically. This integration pattern ensures compliance evidence is always current:

```
Evidence Sources → Compliance Framework → Audit Reports
├── CI/CD pipeline logs (SAST/DAST/SCA scan results)
├── IaC scan results (Checkov findings)
├── Container scan results (Trivy/Grype findings)
├── Cloud posture data (Prowler/CSPM results)
├── Kubernetes audit logs
├── Change management records (PRs, approvals)
└── Deployment records (what deployed when, who approved)
```
