# Cross-Framework Terminology Glossary

This glossary defines terms used across the Techstream framework ecosystem with a consistent, precise meaning. When a term is used differently in an external standard or tool vendor's documentation, the Techstream definition is given here and the external usage is noted.

Terms are grouped by domain. Within each group, definitions are given in logical reading order rather than alphabetical order to allow progressive understanding of related concepts.

---

## Software Delivery

**Pipeline**
The automated sequence of stages that transforms source code into a deployed, validated artifact. Synonyms in common use: workflow (GitHub Actions), job (GitLab CI), build (Jenkins). In Techstream documentation, "pipeline" refers to the end-to-end automation sequence from code commit to production deployment.

**Stage**
A discrete, named phase within the pipeline with a defined input, action, and output. Stages run sequentially unless parallelized explicitly. In Techstream pipeline templates, stages follow a canonical sequence: Source → Build → SAST → SCA → Container Build → Container Scan → Secrets Scan → Sign → Deploy Staging → DAST → Approval → Deploy Production.

**Gate / Security Gate**
A decision point in the pipeline that allows or blocks progression based on automated criteria. Gates can be hard (block on any failure), soft (warn but continue), or approval-based (require human decision). Synonyms: quality gate (SonarQube terminology), policy check. In Techstream, "security gate" specifically means a gate that evaluates security-relevant criteria.

**Runner / Agent**
The compute environment that executes pipeline jobs. GitHub Actions uses "runner"; GitLab CI and Jenkins use "agent". Techstream uses "runner" generically. See also: Ephemeral Runner.

**Ephemeral Runner**
A runner that is provisioned fresh for each pipeline job and terminated when the job completes. No state persists between jobs. Contrast with a persistent runner, which is shared across jobs and retains filesystem state.

**Artifact**
The output of a build stage — a container image, binary, package, or archive. Artifacts should be immutable once built: the same artifact is promoted through environments rather than rebuilt. Contrast with a deployment configuration (e.g., Helm values), which may vary per environment.

**Immutable Artifact**
An artifact that cannot be modified after creation. Container images become immutable when addressed by digest (sha256 hash) rather than a mutable tag (e.g., `latest`). Immutability is a prerequisite for artifact signing and SLSA compliance.

**Promotion**
The act of moving an artifact from one environment to the next (e.g., staging → production) after it passes required gates. Promotion does not rebuild; it passes the same artifact with updated configuration. See also: Environment Promotion.

**Environment**
A deployment target with a defined purpose, configuration, and access control policy. Common environments in Techstream: development (DEV), test/QA, staging (pre-production), and production (PROD). Each environment has a distinct security posture with production having the most controls.

---

## Security Testing

**SAST (Static Application Security Testing)**
Analysis of source code, bytecode, or binary for security vulnerabilities without executing the code. SAST runs early in the pipeline (typically before or at the build stage) and provides fast feedback to developers. Common tools: Semgrep, CodeQL, SonarQube, Checkmarx.

**SCA (Software Composition Analysis)**
Scanning of third-party dependencies (libraries, packages, frameworks) for known vulnerabilities (CVEs) and license compliance issues. SCA operates on dependency manifests (package.json, requirements.txt, go.mod) or by inspecting compiled artifacts. Common tools: Trivy, Grype, Snyk, OWASP Dependency-Check.

**DAST (Dynamic Application Security Testing)**
Testing a running application by sending crafted inputs and observing behavior. DAST requires a deployed application and is run against a staging or pre-production environment. It finds runtime issues that static analysis cannot detect (authentication bypass, session handling, server-side request forgery). Common tools: OWASP ZAP, Burp Suite Enterprise.

**IAST (Interactive Application Security Testing)**
Security instrumentation embedded in a running application that monitors execution behavior during functional testing. IAST instruments are typically libraries loaded at runtime. Fewer organizations use IAST compared to SAST and DAST due to language support constraints and instrumentation overhead.

**Secrets Scanning**
Detection of credentials, private keys, tokens, and other secrets in source code, commit history, and pipeline configuration. Operates in two modes: detect mode (scans existing code) and protect mode (pre-commit hook prevents new secrets from being committed). Common tools: Gitleaks, TruffleHog, GitHub Secret Scanning.

**IaC Scanning**
Static analysis of Infrastructure as Code definitions (Terraform, CloudFormation, Helm charts, Kubernetes manifests, Bicep) for security misconfigurations. Common tools: Checkov, tfsec, Terrascan, kics.

**CSPM (Cloud Security Posture Management)**
Continuous assessment of cloud infrastructure configuration against security best practices and compliance benchmarks. CSPM tools query cloud provider APIs to detect misconfigurations such as public S3 buckets, overpermissive security groups, and disabled logging. Common tools: Prowler, ScoutSuite, Wiz, Orca Security. Cloud-native: AWS Security Hub, Azure Defender for Cloud, GCP Security Command Center.

**Penetration Testing**
A structured, human-conducted security assessment that simulates attacker techniques to identify vulnerabilities not detected by automated tools. Penetration tests are conducted periodically (typically annually or semi-annually) by qualified testers with defined scope and rules of engagement. Contrast with automated security testing, which runs continuously in the pipeline.

**Threat Modeling**
A structured analysis of a system to identify potential threats, their likelihood and impact, and appropriate mitigations. In Techstream, threat modeling is performed during the design phase using the STRIDE methodology. Output: a threat model document listing identified threats and the controls that address them.

---

## Vulnerability Management

**CVE (Common Vulnerabilities and Exposures)**
A unique identifier assigned to a publicly disclosed security vulnerability by the MITRE CVE program. Format: `CVE-YEAR-NUMBER` (e.g., CVE-2021-44228 is the Log4Shell vulnerability). CVEs are the primary identifier used by SCA tools and vulnerability databases.

**CVSS (Common Vulnerability Scoring System)**
A standardized framework for scoring the severity of a vulnerability. CVSS scores range from 0.0 to 10.0. The base score is calculated from exploitability metrics (attack vector, complexity, privileges required, user interaction) and impact metrics (confidentiality, integrity, availability). CVSS v3.1 is the current standard version.

**Severity Levels**
In Techstream frameworks, severity levels follow the CVSS convention:

| Level | CVSS Range | Pipeline Action |
|-------|-----------|----------------|
| Critical | 9.0–10.0 | Block immediately; 7-day SLA for remediation |
| High | 7.0–8.9 | Block in production gates; 30-day SLA |
| Medium | 4.0–6.9 | Warn; 90-day SLA; exception required for acceptance |
| Low | 0.1–3.9 | Inform; 180-day SLA; accepted with documentation |

Note: Many organizations adjust these ranges based on exploitability context. A CVSS 9.0 vulnerability with no known exploit path and compensating controls may be treated differently than a CVSS 7.0 with active exploitation.

**False Positive**
A security finding reported by a tool that does not represent an actual vulnerability or security risk in the specific context of the application being analyzed. High false positive rates are the primary cause of developer distrust in security tooling. Techstream's developer experience guidance provides methods for measuring and reducing false positive rates.

**Vulnerability SLA**
The maximum time allowed from vulnerability discovery to remediation or accepted exception. Techstream default SLAs: Critical 7 days, High 30 days, Medium 90 days, Low 180 days. SLAs begin when the vulnerability is confirmed as a true positive, not when the scanner first reports it.

**VEX (Vulnerability Exploitability eXchange)**
A machine-readable document that communicates whether a specific vulnerability in a specific product is exploitable given the product's configuration and context. VEX statements supplement SBOM data by indicating that, for example, Log4Shell does not affect a product because the vulnerable JNDI lookup is not reachable. See also: [VEX and SBOM Lifecycle](../../software-supply-chain-security-framework/docs/vex-and-sbom-lifecycle.md).

---

## Supply Chain Security

**SBOM (Software Bill of Materials)**
A complete, structured list of all components (libraries, packages, frameworks, operating system packages) that make up a piece of software, including their versions, licenses, and dependency relationships. SBOMs enable rapid identification of affected systems when a new vulnerability is disclosed. Two primary formats: CycloneDX and SPDX.

**SLSA (Supply Chain Levels for Software Artifacts)**
Pronounced "salsa." A framework of security requirements for the software build process, organized into four levels of increasing rigor. SLSA requirements address: build scripting (L1), build provenance signing (L2), hardened build environment (L3), and two-party review with hermetic builds (L4). See: [SLSA Level Advancement Guide](../../software-supply-chain-security-framework/docs/slsa-level-advancement.md).

**Provenance**
A verifiable record of how a software artifact was produced: from which source code, by which build system, at what time, with what inputs. SLSA provenance is an in-toto attestation signed by the build system's identity. Provenance enables consumers to verify that an artifact was built by an expected, trustworthy process.

**Attestation**
A signed statement about a software artifact. An attestation binds an artifact (identified by digest) to a claim about it (e.g., "this image passed SAST with no high-severity findings" or "this image was built from commit abc123 using SLSA L3 build process"). Attestations are stored in artifact registries alongside the artifacts they describe.

**Artifact Signing**
The process of creating a cryptographic signature over an artifact digest using a private key or ephemeral key bound to an identity. Signing enables consumers to verify that an artifact was produced by a trusted party and has not been modified since signing. Techstream uses Cosign with Sigstore's keyless signing model (OIDC-based ephemeral keys) as the standard.

**Keyless Signing**
An artifact signing approach that uses short-lived signing keys generated from OIDC identity tokens rather than persistent key pairs. Keyless signing eliminates the need to manage and protect a long-lived private key. Sigstore's Fulcio certificate authority issues the short-lived key; Rekor (the transparency log) records the signing event. Recommended over key-based signing for CI/CD environments.

**Transparency Log**
An append-only, cryptographically verifiable log of signing events. Rekor is the primary transparency log used with Cosign. Once a signing event is recorded in a transparency log, it cannot be removed or altered, providing an audit trail of all signing activity.

**Hermetic Build**
A build in which all inputs are pre-fetched and verified before the build starts, and network access is blocked during the build itself. Hermetic builds ensure that the build cannot fetch unexpected dependencies or be influenced by external state at build time. Required for SLSA Level 4.

**Reproducible Build**
A build that, given identical inputs, produces bit-for-bit identical output. Reproducibility enables independent verification that an artifact was built from the claimed source. Distinct from hermetic builds (hermetic restricts inputs; reproducible describes output determinism).

---

## Secrets and Credentials

**Secret**
Any credential, key, token, certificate, or sensitive configuration value that grants access to a system or resource. Secrets must never be stored in source code, container images, or build logs. Broad category including: API keys, database passwords, OAuth tokens, private keys, AWS access keys.

**Long-Lived Credential**
A credential with no expiration or a long expiration (months to years). Long-lived credentials are high-risk because a single compromise gives persistent access. IAM access keys with no rotation policy are the most common example in cloud environments.

**Short-Lived Credential / Ephemeral Credential**
A credential issued for a specific, bounded duration (minutes to hours). Short-lived credentials limit the blast radius of credential compromise — even if stolen, they expire quickly. OIDC-federated tokens (see OIDC Federation) are the primary mechanism for short-lived credentials in CI/CD.

**Dynamic Secret**
A credential generated on-demand for a specific consumer with a short TTL, and automatically revoked when the TTL expires or when explicitly revoked. HashiCorp Vault's database secrets engine is the primary implementation: each application request receives a unique username/password pair that expires in minutes. Contrast with a static secret shared among all consumers.

**Secrets Manager**
A centralized service for storing, retrieving, rotating, and auditing secrets. Provides access control (only authorized identities can read specific secrets), audit logging (all secret access is recorded), and rotation automation. Common implementations: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager.

**OIDC Federation**
A mechanism for exchanging a CI/CD platform's OIDC JWT token for short-lived cloud provider credentials. Eliminates the need for long-lived credentials in CI/CD systems. See: [OIDC Federation Guide](../../secure-ci-cd-reference-architecture/docs/oidc-federation-guide.md).

---

## Identity and Access

**IAM (Identity and Access Management)**
The system and policies that control who (identity) can access what (resource) and how (action). In cloud contexts, IAM encompasses human user accounts, service accounts/workload identity, roles, policies, and permission boundaries. Each cloud provider has a native IAM system (AWS IAM, Azure Entra ID, GCP IAM).

**Least Privilege**
The principle that an identity should have only the permissions required to perform its defined function — no more. Least privilege is a foundational zero trust principle. In practice: CI pipeline roles should not have production database access; read-only roles should not have write permissions; developer accounts should not have production admin access.

**RBAC (Role-Based Access Control)**
An access control model where permissions are assigned to roles, and roles are assigned to identities. RBAC is coarse-grained: all members of a role have the same permissions. Kubernetes uses RBAC for API access control; most IAM systems provide RBAC as the primary model.

**ABAC (Attribute-Based Access Control)**
An access control model where access decisions are based on attributes of the subject (identity attributes), object (resource attributes), and environment (time, location, device health). ABAC is fine-grained and dynamic — the same identity may have access under some conditions and not others. AWS IAM Condition keys and Azure Conditional Access are ABAC implementations.

**Service Account**
A non-human identity used by applications, services, and pipelines to authenticate to other services. Service accounts should be unique per service (not shared), have minimal permissions, and have their credentials managed as dynamic or OIDC-federated credentials where possible.

**Workload Identity**
The identity assigned to a workload (container, function, VM) that enables it to authenticate to cloud services without managing credentials in the application. Implemented as AWS IRSA, Azure Workload Identity, or GCP Workload Identity Federation in Kubernetes environments.

---

## Compliance and Governance

**Policy as Code**
The practice of defining security, compliance, and operational policies as machine-readable, version-controlled code that can be automatically evaluated against infrastructure and application configurations. Tools: OPA (Open Policy Agent), Kyverno, Rego. Contrast with manual policy documentation that cannot be automatically enforced.

**Compliance Control**
A specific requirement from a regulatory framework (SOC 2, PCI-DSS, ISO 27001, NIST 800-53) that an organization must satisfy. Controls can be preventive (prevent a violation), detective (detect violations when they occur), or corrective (restore compliance after a violation).

**Evidence**
Documentation that demonstrates a control is in place and operating effectively. For automated controls, evidence is generated automatically (scan result reports, access logs, pipeline audit records). For manual controls, evidence is produced by humans (signed attestations, meeting minutes, training completion records). See: [Evidence Collection Automation](../../compliance-automation-framework/docs/evidence-collection-automation.md).

**Continuous Compliance**
An operational model where compliance is assessed continuously in real time rather than during point-in-time audits. Continuous compliance requires automated policy enforcement, continuous monitoring, and automated evidence collection. Contrast with periodic audit-based compliance, which assesses a sample of controls at a point in time.

**Compliance Drift**
The gap between a desired compliant state and the actual current state of a system. Drift occurs when manually applied changes, emergency modifications, or external configuration changes cause a resource to fall out of its expected compliant configuration. IaC drift detection and CSPM continuous scanning are the primary tools for detecting compliance drift.

**Exception**
A formal, approved deviation from a required security control. Exceptions must have: a documented rationale, an identified owner, a compensating control (where applicable), an expiry date, and an approval from a designated authority. Exceptions without expiry dates become permanent debt.

---

## Architecture

**Zero Trust**
An architectural strategy based on the principle "never trust, always verify." Zero trust eliminates implicit trust based on network location (being inside the corporate network). Every access request — regardless of source — must be authenticated, authorized, and verified against policy. See: [Zero Trust Architecture Guide](../../cloud-security-devsecops/docs/zero-trust-architecture.md).

**Defense in Depth**
A security strategy that uses multiple, independent layers of controls so that if one layer fails, subsequent layers continue to provide protection. No single control is relied upon as the sole protection against a threat. Synonyms: layered security. The six toolchain layers in the DevSecOps Framework are a defense-in-depth architecture.

**Shift Left**
Moving security activities earlier in the software development lifecycle, closer to the developer's daily workflow. The goal is to detect and fix security issues when they are cheapest to address — in the IDE or PR, not after deployment. Contrast with shift right (adding security monitoring to production). Both are necessary; shift left is insufficient alone.

**mTLS (Mutual TLS)**
An extension of TLS in which both the client and server authenticate each other using certificates. Standard TLS authenticates only the server (the client trusts the server's certificate). mTLS authenticates both parties, providing cryptographic identity verification for service-to-service communication. Required for zero trust east-west traffic within a service mesh.

**Service Mesh**
An infrastructure layer that handles service-to-service communication concerns (mTLS, load balancing, observability, circuit breaking) transparently, without requiring changes to application code. Common implementations: Istio, Linkerd, Consul Connect. Service meshes enforce mTLS at the infrastructure layer, making zero trust network controls operational without per-application development effort.

**Admission Control**
A Kubernetes mechanism that intercepts API server requests before resources are persisted and applies validation or mutation policies. Admission controllers can reject non-compliant resources (e.g., pods without security context) or modify resources to add required fields. Implementation: OPA Gatekeeper, Kyverno.

---

## Metrics and Measurement

**DORA Metrics**
Four key metrics validated by the DevOps Research and Assessment program as predictors of software delivery performance: Deployment Frequency, Lead Time for Changes, Change Failure Rate, and Failed Deployment Recovery Time (formerly MTTR). See: [DORA Metrics Guide](dora-metrics-guide.md).

**MTTD (Mean Time to Detect)**
The average time from when a security incident or vulnerability becomes exploitable to when it is detected by the organization. A key security operations metric. Target: < 15 minutes for runtime threats with automated detection.

**MTTR (Mean Time to Recover / Restore)**
The average time from incident detection to service restoration. In DORA terminology, MTTR is specifically "Failed Deployment Recovery Time" — the time to recover from a deployment-induced service failure. In security operations, MTTR measures time to recover from any security incident.

**False Positive Rate**
The percentage of security tool findings that, on investigation, do not represent actual vulnerabilities in context. High false positive rates cause alert fatigue and reduce developer trust in security tooling. Target: < 20% false positive rate for production SAST rulesets.

**Maturity Level**
In the DevSecOps Maturity Model, a maturity level (L1–L5) describes the capability of an organization in a specific domain. L1 is ad hoc and reactive; L5 is continuously optimizing and industry-leading. Maturity levels are domain-specific — an organization can be L4 in CI/CD security and L1 in runtime monitoring.

---

## Tools Reference

| Term | Full Name | Primary Use |
|------|-----------|------------|
| Cosign | Container Signing (Sigstore project) | Artifact signing and attestation |
| Falco | Falco Runtime Security | Kernel syscall monitoring, container runtime security |
| Checkov | — | IaC misconfiguration scanning |
| Trivy | — | Container image, filesystem, and IaC scanning (all-in-one) |
| Grype | — | Container and filesystem vulnerability scanning |
| Gitleaks | — | Secrets detection in code and commit history |
| Syft | — | SBOM generation (CycloneDX, SPDX) |
| Semgrep | — | SAST (fast, multi-language, custom rules) |
| OPA | Open Policy Agent | Policy as Code engine (Rego language) |
| Kyverno | — | Kubernetes-native policy engine (YAML-based policies) |
| Prowler | — | AWS/Azure/GCP security posture assessment |
| Sigstore | — | Ecosystem of signing, transparency, and verification tools (Cosign, Rekor, Fulcio) |
| Rekor | — | Transparency log for signing events |
| Fulcio | — | Certificate authority for keyless signing (OIDC-based) |
| slsa-verifier | — | CLI tool for verifying SLSA provenance attestations |
| Dependency-Track | — | SBOM management and vulnerability tracking platform |
| Falcosidekick | — | Falco alert routing to multiple destinations (Slack, PagerDuty, SIEM) |
| Tetragon | — | eBPF-based process and network monitoring with enforcement capability |
| Hubble | — | Network observability layer for Cilium |
| Cilium | — | eBPF-based Kubernetes networking and security (replaces kube-proxy) |

---

## Related Documents

- [Framework Selection Guide](framework-selection-guide.md) — Choosing which frameworks to adopt
- [Integration Scenarios](integration-scenarios.md) — End-to-end walkthroughs using multiple frameworks
- [DevSecOps Framework: Glossary](../../devsecops-framework/docs/introduction.md) — Domain-specific glossary in the core framework (25+ terms)
- [Software Supply Chain: Framework](../../software-supply-chain-security-framework/docs/framework.md) — Supply chain terminology in depth
