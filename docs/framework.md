# Techstream Framework Portfolio

## Overview

This document provides a comprehensive reference for all nine frameworks in the Techstream DevSecOps Suite. For each framework, it describes the purpose, target audience, key capabilities, integration points, and when to use it. This document is the go-to reference when you need to understand which framework addresses a specific need, or when you want to understand the complete scope of what the Techstream suite covers.

---

## The Techstream DevSecOps Philosophy

Before examining individual frameworks, it is worth articulating the shared philosophy that runs through all of them:

**Security as Engineering**: Security is not a separate discipline applied to engineering from the outside. It is an engineering discipline itself—one that requires the same rigor, automation, and continuous improvement mindset as any other part of software engineering. Techstream frameworks are written by engineers, for engineers.

**Automation Over Policy**: A security policy that relies on humans to consistently follow it will fail at scale. Every control that can be automated must be automated. Human judgment is reserved for decisions that genuinely require it—novel situations, risk acceptance trade-offs, strategic planning.

**Developer Experience is Non-Negotiable**: Security controls that degrade developer experience will be routed around. Every Techstream framework explicitly considers the developer experience implications of its recommendations and prioritizes approaches that provide fast, actionable, in-flow feedback.

**Measurement Drives Improvement**: Security programs that cannot articulate their current posture, trend, and ROI cannot make the case for continued investment or demonstrate continuous improvement. Every framework includes measurable KPIs.

**Principles that Scale, Implementations that Adapt**: The principles in Techstream frameworks are stable because they reflect fundamental security realities. The specific tooling recommendations are time-bound and will evolve as the ecosystem evolves. We clearly distinguish between stable principles and current implementation guidance.

---

## Framework 1: DevSecOps Framework

**Repository**: `devsecops-framework`

### Summary

The DevSecOps Framework is the foundational document of the Techstream suite. It establishes the principles, practices, cultural norms, and organizational structures that define DevSecOps as Techstream understands and practices it. All other frameworks derive their core assumptions from this one.

The framework addresses the "what" and "why" of DevSecOps: what it means to have security genuinely integrated into software delivery, and why that integration is more effective than the traditional model of security as a separate gate.

### Target Audience

- Engineering leadership responsible for establishing DevSecOps programs
- Security team leaders transitioning from traditional security operations to DevSecOps
- Architecture and platform teams designing organizational security models
- New team members who need to understand the foundational principles before working with more specific frameworks

### Key Capabilities

- **DevSecOps Definition and Scope**: A precise, actionable definition of DevSecOps that distinguishes it from security theater
- **Cultural Principles**: The beliefs and norms that must exist for technical controls to be effective
- **Organizational Model**: How security responsibilities are distributed across development, platform, and security teams
- **SDLC Integration Map**: How security integrates at each stage of the software development lifecycle
- **Governance Model**: How security decisions are made, communicated, and continuously improved
- **Anti-Patterns**: Common mistakes organizations make when adopting DevSecOps

### When to Use It

- At the start of any DevSecOps initiative to establish shared understanding and vocabulary
- When onboarding new team members to the security program
- When resolving disputes about security ownership or responsibility
- When evaluating whether security practices are genuinely integrated or merely superficially attached

### Integration with Other Frameworks

This framework is referenced by all others. Key integration points:
- **devsecops-methodology**: Uses the framework's organizational model to design transformation steps
- **devsecops-maturity-model**: Measures maturity against the principles defined here
- **All technical frameworks**: Reference the security principles defined here when making design decisions

---

## Framework 2: Secure CI/CD Reference Architecture

**Repository**: `secure-ci-cd-reference-architecture`

### Summary

The Secure CI/CD Reference Architecture provides definitive architectural patterns for building secure software delivery pipelines. It addresses the design of the pipeline as a security system in itself: ensuring that the pipeline cannot be compromised, cannot be bypassed, and enforces security gates at the appropriate stages.

This framework covers pipeline security across major platforms (GitHub Actions, GitLab CI, Jenkins, CircleCI, Azure DevOps, Tekton) and provides reference architectures for each stage: source control, build, test, package, and deploy.

### Target Audience

- Platform engineers designing or rebuilding CI/CD infrastructure
- Security engineers integrating security tooling into existing pipelines
- Architects evaluating CI/CD platform choices from a security perspective
- Engineering managers responsible for pipeline governance

### Key Capabilities

- **Pipeline Security Architecture**: Multi-stage security control placement diagrams for each major CI/CD platform
- **Credential Security**: Patterns for managing pipeline credentials without long-lived secrets
- **Pipeline Integrity**: Ensuring the pipeline itself has not been tampered with
- **Stage Gates**: Security gate design, failure behavior, and bypass prevention
- **Environment Isolation**: How to isolate build, test, and production environments
- **OIDC Integration**: Using OpenID Connect for credential-free cloud authentication from pipelines
- **Runner/Agent Security**: Hardening build agents against supply chain attacks
- **Audit Trail**: What pipeline events must be logged and how

### When to Use It

- When designing a new CI/CD system from scratch
- When evaluating whether an existing pipeline provides adequate security assurances
- When a pipeline breach or compromise has occurred and a redesign is needed
- When integrating additional security tooling and needing guidance on where in the pipeline to place it

### Integration with Other Frameworks

- **secure-pipeline-templates**: Provides reusable implementations of this architecture
- **cloud-security-devsecops**: Pipeline integration with cloud security controls (IaC scanning, image scanning)
- **software-supply-chain-security-framework**: Supply chain security controls placed at build and package stages
- **compliance-automation-framework**: Evidence collection from pipeline stages
- **release-orchestration-framework**: Pipeline gates that govern environment promotion

---

## Framework 3: Release Orchestration Framework

**Repository**: `release-orchestration-framework`

### Summary

The Release Orchestration Framework governs the process of promoting software from built artifact to production system. It addresses the security and governance of deployment: ensuring that only approved, verified artifacts are deployed; that deployments are auditable; that change control requirements are met; and that rollback is possible when needed.

This framework is particularly important for regulated industries where change control requirements are prescriptive, and for organizations operating at scale where deployment failures have significant business impact.

### Target Audience

- Platform and release engineering teams
- Change advisory boards (CABs) seeking to automate change control
- Security engineers designing deployment governance
- Compliance teams responsible for change management evidence

### Key Capabilities

- **Deployment Gate Design**: Automated and manual gates with clear criteria for each environment
- **Approval Workflows**: Integration with ticketing and approval systems
- **Deployment Strategies**: Security implications of blue-green, canary, rolling, and feature-flag deployments
- **Rollback Procedures**: Automated rollback triggers and procedures
- **Audit Trail**: Release records that satisfy change management audit requirements
- **Environment Promotion Policy**: What must be true before promoting from dev → staging → production
- **Emergency Change Procedures**: Break-glass processes for emergency production changes

### When to Use It

- When establishing formal change control for production deployments
- When a deployment failure or security incident was caused by inadequate release governance
- When compliance requirements mandate documented change management processes
- When multi-team deployments require coordination and governance

### Integration with Other Frameworks

- **secure-ci-cd-reference-architecture**: Releases are the downstream output of the CI/CD pipeline
- **compliance-automation-framework**: Release records serve as compliance evidence
- **cloud-security-devsecops**: Cloud-specific deployment patterns and controls

---

## Framework 4: Software Supply Chain Security Framework

**Repository**: `software-supply-chain-security-framework`

### Summary

The Software Supply Chain Security Framework addresses the security of everything that goes into a software artifact. The SolarWinds, Log4Shell, and XZ Utils attacks demonstrated that a compromise of a single dependency or build tool can result in the compromise of thousands of downstream organizations. This framework provides systematic guidance for reducing supply chain risk.

The framework covers the full supply chain: source code and its dependencies, build systems and their tools, artifacts and their distribution, and the verification mechanisms that allow consumers to trust what they receive.

### Target Audience

- Security engineers responsible for dependency and artifact security
- Platform engineers managing build infrastructure and artifact distribution
- Software architects making decisions about third-party component usage
- Compliance teams managing SBOM requirements (increasingly mandated by regulation)

### Key Capabilities

- **SLSA Framework Implementation**: Guidance for achieving Supply Levels for Software Artifacts (SLSA) Levels 1–4
- **SBOM Generation and Management**: Automated SBOM generation (CycloneDX, SPDX), distribution, and lifecycle management
- **Dependency Vulnerability Management**: Automated scanning (Dependabot, Snyk, OWASP DC), remediation workflows, and exception management
- **Artifact Signing and Verification**: Sigstore/Cosign integration for signing and verifying artifacts, container images, and attestations
- **Build System Hardening**: Securing build agents, runners, and build tooling from compromise
- **Dependency Confusion Prevention**: Controls for preventing substitution attacks via public package registries
- **Third-Party Component Vetting**: Process for evaluating and approving new dependencies

### When to Use It

- When implementing SBOM requirements (increasingly common in regulated industries and government contracts)
- After any supply chain security incident or near-miss
- When adopting new open-source dependencies at scale
- When certifying software for sensitive deployment environments

### Integration with Other Frameworks

- **secure-ci-cd-reference-architecture**: Build pipeline is the primary implementation surface
- **secure-pipeline-templates**: Templates for SCA scanning and artifact signing in pipelines
- **cloud-security-devsecops**: Container image supply chain, registry security, image signing

---

## Framework 5: DevSecOps Maturity Model

**Repository**: `devsecops-maturity-model`

### Summary

The DevSecOps Maturity Model provides a structured framework for assessing and improving DevSecOps program maturity. It defines five maturity levels across all DevSecOps domains, providing clear criteria for each level, evidence requirements that support audit use cases, and a roadmap structure that guides organizations from their current state to their target state.

Unlike simple checklists, the maturity model acknowledges that DevSecOps improvement is a journey, that different organizations start from different places, and that realistic progress requires prioritization. It provides both the assessment tools and the planning framework needed to make continuous, measurable progress.

### Target Audience

- Security and engineering leadership responsible for program planning and investment decisions
- Security engineers conducting internal assessments or preparing for external audits
- Compliance teams seeking to demonstrate security program maturity to auditors and customers
- Consultants and advisors conducting DevSecOps program assessments

### Key Capabilities

- **Domain Coverage**: Maturity levels for 10+ DevSecOps domains: threat modeling, SAST, DAST, SCA, secrets management, IaC security, container security, compliance automation, incident response, and more
- **Assessment Questionnaires**: Structured questions for each domain with scoring guidance
- **Evidence Requirements**: Per-level evidence requirements that satisfy audit expectations
- **Roadmap Templates**: Structured planning templates for Level N → Level N+1 improvements
- **KPI Catalog**: Measurement definitions for security KPIs across all domains
- **Benchmark Data**: Industry benchmark data for common organizational profiles

### When to Use It

- At the start of any DevSecOps initiative to establish a credible baseline
- Quarterly or semi-annually to track program progress
- Before external audits to identify and address gaps
- When communicating security program status to leadership or customers
- When prioritizing security investment decisions

### Integration with Other Frameworks

- **devsecops-framework**: Assessment criteria are derived from the principles and controls defined here
- **All technical frameworks**: Each domain in the maturity model maps to one or more technical frameworks
- **compliance-automation-framework**: Maturity levels provide structure for compliance program design

---

## Framework 6: Compliance Automation Framework

**Repository**: `compliance-automation-framework`

### Summary

The Compliance Automation Framework transforms compliance from a periodic, manual, audit-driven activity into a continuous, automated, evidence-generating capability. It provides the patterns, tooling integrations, and process designs needed to automatically collect, organize, and present compliance evidence from across the DevSecOps pipeline and cloud environment.

The framework supports multiple compliance regimes (SOC 2, ISO 27001, PCI DSS, HIPAA, FedRAMP) and provides mappings from technical controls to specific compliance framework requirements, enabling organizations to demonstrate compliance as a byproduct of their engineering practices rather than as a separate audit exercise.

### Target Audience

- Compliance and GRC teams modernizing compliance programs
- Security engineers integrating compliance evidence collection into pipelines
- Engineering teams subject to compliance requirements who need to minimize audit burden
- Auditors and assessors evaluating cloud-native compliance programs

### Key Capabilities

- **Compliance-as-Code Patterns**: Expressing compliance requirements as code that can be version-controlled, reviewed, and automatically enforced
- **Evidence Collection Automation**: Automated extraction of compliance evidence from CI/CD pipelines, cloud environments, and infrastructure
- **Control Mapping**: Detailed mapping from technical controls to SOC 2 Trust Service Criteria, ISO 27001 Annex A, PCI DSS requirements, and other frameworks
- **Continuous Compliance Dashboard**: Real-time compliance posture across all applicable frameworks
- **Audit Package Generation**: Automated generation of audit evidence packages for auditor consumption
- **Exception Management**: Process for documenting, approving, and tracking compliance exceptions
- **Policy-as-Code Templates**: Reusable policies for common compliance control areas

### When to Use It

- When preparing for initial SOC 2, ISO 27001, or other certification audits
- When compliance audit cycles are causing significant operational disruption
- When expanding to a new compliance framework and needing to identify control gaps
- When customer due diligence requests require documented, demonstrable compliance evidence

### Integration with Other Frameworks

- **secure-ci-cd-reference-architecture**: Pipeline is the primary source of compliance evidence
- **cloud-security-devsecops**: Cloud controls are a major component of most compliance frameworks
- **devsecops-maturity-model**: Compliance maturity levels align with overall DevSecOps maturity
- **release-orchestration-framework**: Release records serve as change management evidence

---

## Framework 7: Secure Pipeline Templates

**Repository**: `secure-pipeline-templates`

### Summary

The Secure Pipeline Templates framework provides ready-to-use, production-quality pipeline components for implementing security controls across major CI/CD platforms. Where the Secure CI/CD Reference Architecture describes what a secure pipeline should look like architecturally, these templates provide the actual code that implements that architecture.

Templates are parameterized for different use cases, risk profiles, and technology stacks. They are designed to be adopted quickly—a team should be able to integrate a template into their existing pipeline within hours, not weeks.

### Target Audience

- Platform engineers building or maintaining CI/CD infrastructure
- Development teams retrofitting security into existing pipelines
- Security engineers standardizing security controls across multiple teams or repositories
- Organizations adopting GitHub Actions, GitLab CI, or other supported platforms

### Key Capabilities

- **SAST Templates**: Ready-to-use workflows for CodeQL, Semgrep, Bandit, SpotBugs, and others
- **DAST Templates**: ZAP and Nuclei integration with ephemeral environment provisioning
- **SCA Templates**: Dependency scanning with Dependabot, Snyk, and OWASP Dependency-Check
- **Secret Detection Templates**: detect-secrets, GitLeaks, and TruffleHog integrations
- **IaC Security Templates**: Checkov and TFSec for Terraform, CloudFormation, and Kubernetes manifests
- **Container Security Templates**: Trivy and Grype image scanning, Cosign signing, SBOM generation
- **Compliance Evidence Templates**: Automated evidence collection and formatting for common compliance frameworks
- **Multi-platform Support**: GitHub Actions, GitLab CI, Jenkins, CircleCI, Azure DevOps

### When to Use It

- When implementing security scanning in a new repository or project
- When standardizing security tooling across multiple teams
- When onboarding a new security tool and needing a production-ready integration
- When a security review has identified specific pipeline gaps to close

### Integration with Other Frameworks

- **secure-ci-cd-reference-architecture**: Templates implement this architecture
- **All technical frameworks**: Templates implement the pipeline-level controls defined in each domain framework

---

## Framework 8: DevSecOps Methodology

**Repository**: `devsecops-methodology`

### Summary

The DevSecOps Methodology framework is the organizational change playbook for DevSecOps transformation. Where the DevSecOps Framework describes what DevSecOps is, and the technical frameworks describe how to implement specific controls, the Methodology framework addresses the human, organizational, and change management dimensions of transformation.

DevSecOps transformation is as much an organizational change initiative as a technical one. Teams that invest only in tooling without addressing culture, ownership, and communication consistently underachieve. This framework provides the structure for addressing both dimensions.

### Target Audience

- Engineering and security leadership driving transformation programs
- Change management practitioners supporting DevSecOps initiatives
- Program managers responsible for transformation timelines and milestones
- Security team leaders transitioning their teams from traditional to DevSecOps models

### Key Capabilities

- **Transformation Phases**: A phased approach to DevSecOps transformation with clear milestones
- **Stakeholder Engagement**: Strategies for gaining and maintaining executive sponsorship, engineering buy-in, and compliance team partnership
- **Organizational Design**: Models for security team structure in DevSecOps-mature organizations
- **Security Champion Programs**: How to identify, train, and empower security champions within development teams
- **Communication Frameworks**: How to communicate security requirements, findings, and progress to different audiences
- **Change Resistance Patterns**: Common forms of resistance to DevSecOps adoption and how to address them
- **Training and Enablement**: Upskilling programs for development, platform, and security teams

### When to Use It

- At the start of a significant DevSecOps program
- When a technical DevSecOps implementation has stalled due to cultural or organizational issues
- When reorganizing security teams to better support DevSecOps practices
- When launching a security champion program

### Integration with Other Frameworks

- **devsecops-framework**: Methodology implements the organizational model defined here
- **devsecops-maturity-model**: Methodology uses maturity assessments to track transformation progress
- **All technical frameworks**: Methodology provides the organizational context for technical implementation

---

## Framework 9: Cloud Security DevSecOps

**Repository**: `cloud-security-devsecops`

### Summary

The Cloud Security DevSecOps Framework provides comprehensive guidance for integrating cloud security controls into DevSecOps pipelines and practices. It covers security across the full cloud stack—identity, network, workload, container, Kubernetes, data, and compliance—with specific guidance for AWS, Azure, and Google Cloud Platform.

This framework recognizes that cloud environments introduce specific security challenges—misconfigurations, identity-based attacks, ephemeral workloads, shared responsibility—that require cloud-native security approaches. It provides the reference architectures, control catalog, implementation guidance, and runbooks needed to achieve a mature cloud security posture.

### Target Audience

- Cloud platform engineers responsible for cloud security architecture
- Security engineers implementing cloud security controls
- DevOps engineers integrating cloud security tooling into pipelines
- Organizations migrating workloads to cloud and needing to establish security foundations

### Key Capabilities

- **Cloud Security Reference Architecture**: Multi-layer security architecture for AWS, Azure, and GCP
- **Landing Zone Design**: Secure account/subscription structure with inherited security baselines
- **IAM Security Controls**: Least-privilege patterns, workload identity, PAM integration
- **Network Security**: VPC design, security group automation, WAF-as-code, private endpoints
- **Kubernetes Security**: CIS benchmark compliance, Pod Security Standards, RBAC hardening, admission control, runtime security
- **Container Security**: Base image standards, image scanning, signing, registry security
- **Secrets Management**: Vault deployment, dynamic secrets, rotation automation
- **Cloud Compliance**: Prowler/ScoutSuite integration, CSPM setup, Policy-as-Code
- **Incident Response Runbooks**: Cloud-specific playbooks for common attack scenarios

### When to Use It

- When establishing cloud security foundations for a new cloud environment
- When hardening an existing cloud environment against known misconfiguration patterns
- When integrating cloud security controls into CI/CD pipelines
- When preparing for a cloud security audit or CSPM implementation

### Integration with Other Frameworks

- **secure-ci-cd-reference-architecture**: Cloud security controls are enforced via the pipeline (IaC scanning, image scanning)
- **secure-pipeline-templates**: Templates for IaC scanning, container scanning, and cloud security testing
- **compliance-automation-framework**: Cloud security findings (Prowler, CSPM) serve as compliance evidence
- **software-supply-chain-security-framework**: Container image supply chain is a cloud security concern

---

## Cross-Framework Principles

The following principles apply consistently across all Techstream frameworks:

### Principle 1: Security by Default

Controls should be configured to the secure state by default. Teams should have to consciously opt out of security controls (with documentation and justification), not opt in. This is the "paved road" principle: make the secure path the easy path.

### Principle 2: Fail Secure

When a security control fails or is unavailable, the system should default to the more secure state. A pipeline step that cannot complete its security scan should block the deployment, not allow it. An admission controller that loses connectivity to its policy server should deny admission, not allow it.

### Principle 3: Separation of Duties

No single person or automated system should be able to both make and approve sensitive changes. This applies to code changes (author ≠ reviewer), infrastructure changes (engineer ≠ pipeline), and privilege escalation (requester ≠ approver).

### Principle 4: Immutability

Artifacts—container images, infrastructure configurations, release packages—should be immutable once produced. If a change is needed, a new artifact should be produced, not the existing artifact modified. Immutability provides auditability, reproducibility, and resistance to tampering.

### Principle 5: Least Privilege by Design

Every component, service, user, and process should have only the permissions required to perform its specific function. This applies to IAM roles, Kubernetes RBAC, network policies, and application permissions equally. Over-privilege is a design flaw, not an acceptable default.

### Principle 6: Observable Security

Security state must be continuously observable. Organizations should be able to answer, at any moment: What is our current security posture? What changed in the last 24 hours? What are our current open risks? Security that cannot be observed cannot be improved.

---

## The Continuous Improvement Philosophy

Techstream's approach to DevSecOps improvement is explicitly iterative. We do not believe in "big bang" transformations that attempt to implement everything simultaneously. We believe in:

**Small, frequent improvements**: Each improvement cycle should be achievable within a sprint or quarter, demonstrating measurable progress and building organizational confidence.

**Measurement-driven prioritization**: Improvements should be prioritized by risk reduction per unit of effort, not by technical interest or vendor preference.

**Learning and adaptation**: Security programs must learn from incidents, near-misses, and new threat intelligence. Frameworks must be treated as living documents that evolve with the threat landscape.

**Community knowledge sharing**: The most effective improvements often come from learning what others have experienced. Techstream actively facilitates knowledge sharing across its community.

---

## Open Source Commitment

All Techstream frameworks are published under the Apache License 2.0. This is not simply a licensing choice—it reflects a commitment to the principle that security knowledge should be freely available. Organizations should not have to pay for access to the foundational knowledge that enables them to build secure software.

Techstream's commercial activities (advisory services, training, enterprise support) fund the continuous development and maintenance of these open-source frameworks. We believe this model creates better alignment between Techstream's commercial success and the security of the broader industry than a proprietary knowledge model would.

We actively encourage organizations that benefit from these frameworks to contribute improvements back. The cumulative knowledge of a large community will always exceed what any single organization can produce.
