# Techstream Framework Roadmap, Glossary, and Documentation Index

---

## Part 1: Techstream Framework Roadmap

### Current State and Strategic Direction

The Techstream Framework Suite has reached a point of strong foundational coverage: all nine core frameworks are established and actively maintained. The next phase of framework evolution focuses on three strategic themes:

1. **Depth over breadth**: Maturing existing frameworks with more detailed implementation guidance, more tooling integrations, and better coverage of edge cases and advanced scenarios
2. **Intelligence integration**: Incorporating AI/ML-assisted security capabilities as they mature into production-viable tooling
3. **Platform convergence**: Addressing the convergence of security tooling categories (CSPM + CWPP → CNAPP, SAST + SCA → unified code security platforms) with updated architectural guidance

---

### Industry Trends Driving Framework Evolution

#### AI/ML Security

Artificial intelligence is changing the security landscape in two directions simultaneously: AI is being used to attack software systems (AI-generated malware, AI-assisted phishing, AI-powered vulnerability discovery), and AI is being used to defend them (AI-assisted threat detection, AI-powered code review, AI-generated security recommendations).

**Framework Implications**:
- New threat modeling considerations for AI-powered attack capabilities
- Guidance for using AI-assisted security tooling effectively (and avoiding over-reliance)
- Security controls for AI/ML systems themselves (model poisoning, prompt injection, training data security)
- LLM security testing guidance for organizations deploying AI in applications

**Planned Framework Updates**:
- devsecops-framework: Add AI/ML security as a first-class domain
- cloud-security-devsecops: Add controls for cloud AI/ML services (SageMaker, Azure AI, Vertex AI)
- secure-ci-cd-reference-architecture: Add guidance for securing AI-assisted development pipelines

---

#### Platform Engineering

The platform engineering movement—building internal developer platforms (IDPs) that abstract infrastructure and developer tooling—has significant security implications. Well-designed platforms can be the most effective mechanism for distributing security capabilities to development teams at scale. Poorly designed platforms can create new attack surfaces or mask security problems.

**Framework Implications**:
- Platform security as a distinct domain: securing the IDP itself and the security capabilities it provides
- "Golden path" security: designing platform defaults to be secure by default
- Platform-as-security-enabler patterns: how platforms can automatically provide secrets management, service identity, and deployment security to workloads

**Planned Framework Updates**:
- devsecops-framework: Add platform engineering and IDPs as a key organizational pattern
- secure-pipeline-templates: Add platform-oriented templates that can be embedded in IDPs
- cloud-security-devsecops: Add guidance for platform engineering on Kubernetes

---

#### CNAPP (Cloud-Native Application Protection Platform)

The convergence of CSPM (Cloud Security Posture Management), CWPP (Cloud Workload Protection Platform), CIEM (Cloud Infrastructure Entitlement Management), and other cloud security categories into unified CNAPP platforms is changing the tooling landscape. Organizations that previously deployed multiple specialized tools may be able to achieve broader coverage with fewer, more integrated platforms.

**Framework Implications**:
- Updated tooling guidance reflecting CNAPP market evolution
- Integration patterns for CNAPP platforms (Wiz, Orca, Prisma Cloud, Microsoft Defender for Cloud)
- Evaluation criteria for choosing between CNAPP platforms and best-of-breed point solutions

**Planned Framework Updates**:
- cloud-security-devsecops: Add CNAPP platform selection and integration guidance
- compliance-automation-framework: Add CNAPP platforms as compliance evidence sources

---

#### eBPF Security

eBPF (Extended Berkeley Packet Filter) technology is enabling a new generation of security tooling that provides deep, low-overhead observability and enforcement at the Linux kernel level. eBPF-based tools (Cilium, Tetragon, Falco with eBPF driver) can provide network policy enforcement, runtime security monitoring, and threat detection with significantly lower overhead than traditional approaches.

**Framework Implications**:
- eBPF-based runtime security as the new default for Kubernetes environments
- Network policy enforcement through eBPF (Cilium) vs. iptables-based approaches
- eBPF security observability as a replacement for multiple point tools

**Planned Framework Updates**:
- cloud-security-devsecops: Add eBPF security tooling guidance (Tetragon, Cilium)
- devsecops-framework: Add eBPF as a key enabling technology for DevSecOps observability

---

#### Software Supply Chain Security (Continued Evolution)

The software supply chain security space continues to evolve rapidly, driven by executive orders, customer requirements, and new tooling. SLSA framework adoption is increasing. SBOM requirements are becoming mandated in regulated industries and government contracts. Sigstore adoption is growing as the default signing infrastructure for open-source projects.

**Planned Framework Updates**:
- software-supply-chain-security-framework: Add SLSA Level 3–4 implementation guidance
- software-supply-chain-security-framework: Add emerging supply chain security standards (OpenSSF Scorecard, OSS-Fuzz integration)
- secure-pipeline-templates: Add SBOM attestation templates for common compliance frameworks

---

### Research Agenda

Techstream conducts ongoing research to inform framework development. Current research priorities:

**Drift Detection at Scale**: How do large organizations detect and respond to infrastructure drift across hundreds of cloud accounts and thousands of resources? Current tooling is insufficient for this scale; research is exploring event-driven drift detection and ML-assisted drift prioritization.

**Security Metrics Benchmarking**: What do "good" DevSecOps security metrics look like for organizations of different sizes and industries? Techstream is developing benchmark datasets to help organizations contextualize their KPIs against industry peers.

**Developer Security Feedback Effectiveness**: What types of security feedback produce the most effective developer responses? Research is examining the relationship between feedback format, timing, and actionability and subsequent developer security behavior.

**AI-Assisted Threat Modeling**: Can AI models effectively assist with threat modeling in ways that scale to development team velocity? Research is exploring prompt engineering and fine-tuning approaches for threat modeling assistance.

---

### Community Contributions Roadmap

Techstream is building a more structured community contribution program that will enable community members to:
- Submit and maintain tooling integration guides for tools not currently covered by Techstream frameworks
- Contribute case studies documenting real-world framework adoption experiences
- Participate in framework working groups that develop new content areas
- Contribute translations of framework content into non-English languages

The community contribution program is expected to launch in Q2 2025, with initial working groups focused on GCP-specific guidance, GitLab CI template coverage, and non-English documentation.

---

### Version Management Strategy

Techstream frameworks follow semantic versioning:
- **Major versions** (X.0.0): Significant architectural changes, breaking changes to recommended implementations, or major scope expansions
- **Minor versions** (X.Y.0): New content, new tooling integrations, non-breaking guidance updates
- **Patch versions** (X.Y.Z): Corrections, clarifications, minor updates

**Stability commitments**:
- Patch and minor versions are published on a rolling basis as content is ready
- Major versions are published at most twice per year and include a 6-month deprecation notice for breaking changes
- Tooling version recommendations are updated on a quarterly review cycle

---

### Deprecation Policy

When Techstream deprecates a tool recommendation or implementation pattern:
1. A deprecation notice is published in the relevant framework document and in GitHub Discussions
2. The deprecated recommendation remains in documentation (marked as deprecated) for 12 months
3. A replacement recommendation is published simultaneously with the deprecation notice
4. After 12 months, deprecated content is archived in a legacy section

Organizations should treat tool deprecations from Techstream as signals to evaluate their current implementations, but not as mandates to change immediately. The business context, cost of change, and risk profile should drive the actual migration timeline.

---

### Long-Term Vision for DevSecOps Industry Leadership

Techstream's long-term vision for its role in the DevSecOps industry extends beyond framework documentation:

**Industry Standards Participation**: Techstream actively participates in industry standards bodies including OpenSSF, CNCF Security TAG, NIST SSDF working groups, and others. Framework content increasingly aligns with and contributes to emerging industry standards.

**Empirical Research**: Techstream aims to contribute empirical research on DevSecOps effectiveness—not just opinion and best practice, but data-driven findings about what security investments produce what outcomes. This research will be openly published.

**Education at Scale**: Techstream is developing freely available DevSecOps education resources—not just framework documentation, but structured learning paths, practical exercises, and certification programs—to address the critical shortage of DevSecOps-skilled practitioners.

**Ecosystem Influence**: By maintaining high-quality, widely adopted open-source frameworks, Techstream aims to influence the direction of the DevSecOps ecosystem: vendor tooling, cloud provider security features, and industry standards. The goal is a future where the practices described in these frameworks are the default, not the exception.

---

## Part 2: Comprehensive Glossary

### A

**Access Control List (ACL)**: A list of rules that specifies which users or systems are allowed or denied access to a resource. In cloud networking, NACLs (Network ACLs) are stateless firewall rules applied at the subnet level.

**Admission Controller**: A Kubernetes component that intercepts requests to the Kubernetes API server before object persistence. Admission controllers can validate, mutate, or reject API requests based on policy. OPA Gatekeeper and Kyverno are common admission controllers.

**Anomaly Detection**: Security monitoring that identifies activity deviating from established baselines. Unlike signature-based detection, anomaly detection can identify novel threats.

**AppArmor**: A Linux security module that provides mandatory access control by confining applications to a limited set of resources. Can be used to restrict container capabilities.

**ASPM (Application Security Posture Management)**: An emerging category of tools that provide unified visibility into application security risk across SAST, DAST, SCA, and other scanning sources.

**Artifact**: Any file produced by a build process intended for deployment, distribution, or further processing: container images, compiled binaries, deployment packages, etc.

**Attestation**: A digitally signed statement about an artifact—e.g., "this image was scanned by Trivy and has no CRITICAL vulnerabilities" or "this image was built from this specific source code commit."

**Audit Log**: A chronological record of system events for security and compliance purposes. Cloud audit logs (CloudTrail, Azure Activity Log, GCP Audit Logs) record all API calls made to cloud services.

### B

**Base Image**: The starting layer of a container image, from which additional layers are added. Base image security directly impacts the security of all derived images.

**Blast Radius**: The scope of impact when a security control fails or is compromised. Architecture decisions that minimize blast radius limit the damage from any single security failure.

**BeyondCorp**: Google's zero-trust network access model, which removes the concept of a privileged internal network in favor of per-request authentication and authorization regardless of network location.

**Branch Protection**: Source control policies that prevent direct pushes to protected branches, require pull request reviews, and can require status checks (including security scan results) to pass before merging.

### C

**CIEM (Cloud Infrastructure Entitlement Management)**: Tools that analyze and manage cloud IAM permissions to identify and remediate excessive or unused permissions.

**CI/CD (Continuous Integration / Continuous Deployment)**: Engineering practices and supporting tooling for automatically building, testing, and deploying software changes. Security integration into CI/CD is a core DevSecOps concern.

**CIS Benchmark**: Security configuration benchmarks published by the Center for Internet Security for common operating systems, cloud platforms, and infrastructure components. Widely used as a baseline security standard.

**CNAPP (Cloud-Native Application Protection Platform)**: A converged platform combining CSPM, CWPP, and other cloud security capabilities into a single product. Examples: Wiz, Orca Security, Prisma Cloud.

**Compliance-as-Code**: Expressing compliance requirements as machine-readable code that can be version-controlled, automatically evaluated, and enforced through policy engines.

**Container**: A standardized unit of software packaging that includes application code, runtime dependencies, and configuration. Docker and containerd are common container runtimes.

**Cosign**: An open-source tool for signing and verifying container images and other OCI artifacts. Part of the Sigstore project.

**CSPM (Cloud Security Posture Management)**: Tools that continuously assess cloud resource configurations against security best practices and compliance standards. Examples: Prowler, Wiz, AWS Security Hub, Microsoft Defender for Cloud.

**CWPP (Cloud Workload Protection Platform)**: Security tools that protect running workloads (VMs, containers, serverless) from threats at the compute layer.

### D

**DAST (Dynamic Application Security Testing)**: Security testing that evaluates a running application by sending inputs and analyzing responses. Identifies runtime vulnerabilities not visible in static code analysis.

**Defense in Depth**: A security architecture principle that layers multiple independent security controls so that the failure of any single control does not result in a complete breach.

**Dependency Confusion**: A supply chain attack where a public package is published with the same name as an internal package, causing build tools to preferentially use the public (potentially malicious) version.

**DevSecOps**: The integration of security practices and tooling into the DevOps methodology, making security a shared responsibility embedded throughout the software development lifecycle.

**DMARC**: Domain-based Message Authentication, Reporting, and Conformance. An email authentication protocol that protects against domain spoofing.

**Dynamic Secrets**: Credentials that are generated on-demand and expire automatically after a defined time period, eliminating the risk associated with long-lived static credentials.

### E

**eBPF (Extended Berkeley Packet Filter)**: A technology that allows programs to run in the Linux kernel without changing kernel source code, enabling powerful security observability and enforcement with low overhead.

**EDR (Endpoint Detection and Response)**: Security software that monitors endpoint behavior for malicious activity and provides response capabilities.

**Egress Control**: Network controls that restrict outbound connections from a network or workload. Egress controls limit data exfiltration and command-and-control communication.

### F

**Falco**: An open-source runtime security tool that uses eBPF or kernel module to monitor system calls and detect anomalous behavior in containers and Kubernetes workloads.

**FedRAMP (Federal Risk and Authorization Management Program)**: A US government program that provides a standardized approach to security assessment, authorization, and monitoring for cloud products and services used by federal agencies.

**Federation**: The practice of establishing trust relationships between identity providers, allowing users authenticated by one provider to access resources controlled by another.

### G

**GDPR (General Data Protection Regulation)**: EU regulation governing the collection, storage, and processing of personal data of EU residents. Applies to any organization processing EU resident data regardless of where the organization is located.

**GitOps**: An operational framework that uses Git as the single source of truth for declarative infrastructure and application configuration, with changes deployed through automated pipelines triggered by Git commits.

**GSSP (Google Security Vulnerability Submission Program)**: Google's responsible disclosure program for security vulnerabilities in Google products.

**GuardDuty**: AWS threat detection service that analyzes CloudTrail, VPC Flow Logs, DNS logs, and other data sources to identify malicious or unauthorized activity.

### H

**Hardening**: The process of reducing a system's attack surface by disabling unnecessary services, applying security configurations, and removing default credentials.

**HIPAA (Health Insurance Portability and Accountability Act)**: US regulation governing the privacy and security of protected health information (PHI).

**HSM (Hardware Security Module)**: A dedicated hardware device for cryptographic key storage and operations, providing higher security assurance than software key storage.

**Hub-and-Spoke**: A cloud network topology where a central hub network is connected to multiple spoke networks. Traffic between spokes passes through the hub, enabling centralized security inspection and control.

### I

**IAM (Identity and Access Management)**: The system governing authentication (who are you?) and authorization (what can you do?) for cloud resources.

**IaC (Infrastructure as Code)**: The practice of managing infrastructure through machine-readable configuration files, enabling version control, code review, and automated deployment of infrastructure.

**IMDS (Instance Metadata Service)**: A service available to cloud compute instances that provides metadata about the instance, including temporary credentials. A common target for SSRF-based credential theft.

**Immutable Infrastructure**: Infrastructure that is replaced rather than modified when changes are needed, preventing configuration drift and enabling consistent, reproducible deployments.

**IOC (Indicators of Compromise)**: Evidence that a security incident may have occurred, such as unusual network traffic, unexpected process execution, or unauthorized file modifications.

**IRSA (IAM Roles for Service Accounts)**: An AWS EKS feature that allows Kubernetes service accounts to assume IAM roles for fine-grained access control to AWS services.

**ISO 27001**: International standard for information security management systems. Provides a framework of policies and procedures including legal, physical, and technical controls involved in an organization's information risk management processes.

### J

**JIT (Just-in-Time) Access**: A privileged access model where elevated permissions are granted only when needed, for a limited time, after an approval process, rather than as standing access.

**JWKS (JSON Web Key Set)**: A set of public keys used to verify JSON Web Token (JWT) signatures. Used in OIDC-based authentication flows including cloud workload identity.

**JWT (JSON Web Token)**: A compact, URL-safe means of representing claims to be transferred between parties. Used in OIDC authentication and cloud workload identity.

### K

**KMS (Key Management Service)**: A cloud-managed service for creating and controlling cryptographic keys used to encrypt data.

**Kubernetes**: An open-source container orchestration platform for automating deployment, scaling, and management of containerized applications. A central platform in modern cloud-native security.

**Kyverno**: A Kubernetes-native policy engine that uses YAML-based policies for admission control, validation, mutation, and generation of Kubernetes resources.

### L

**Landing Zone**: A pre-configured, secure cloud environment that serves as the foundation for workload deployment. Typically includes account structure, network topology, IAM baseline, and security tooling.

**LGTM (Looks Good To Me)**: Informal code review approval. In security contexts, critical security changes should require explicit security review, not just LGTM approvals.

**Least Privilege**: The security principle that every subject (user, process, service) should have only the minimum permissions required to perform its intended function.

### M

**Managed Identity**: An Azure feature that provides a cloud service with an automatically managed identity in Azure Active Directory, eliminating the need for credentials in code.

**MITRE ATT&CK**: A globally accessible knowledge base of adversary tactics and techniques based on real-world observations. Used for threat modeling, detection development, and security program evaluation.

**MFA (Multi-Factor Authentication)**: Authentication that requires two or more verification factors: something you know (password), something you have (device), or something you are (biometric).

**mTLS (Mutual TLS)**: Transport Layer Security with client certificate authentication—both the client and server authenticate each other. Used for service-to-service authentication in zero-trust architectures.

### N

**NACL (Network Access Control List)**: A stateless, subnet-level network firewall in cloud VPCs that evaluates traffic based on rules applied in numeric order.

**NetworkPolicy**: A Kubernetes resource that specifies how pods are allowed to communicate with each other and with other network endpoints. Requires a CNI plugin that supports network policies.

**NIST (National Institute of Standards and Technology)**: US government agency that publishes widely used security standards and frameworks including NIST SP 800-53, NIST Cybersecurity Framework, and NIST SSDF.

### O

**OIDC (OpenID Connect)**: An identity layer built on top of OAuth 2.0 that enables authentication. Used for cloud workload identity, SSO, and cross-service authentication.

**OPA (Open Policy Agent)**: A general-purpose, open-source policy engine that enables unified policy enforcement across the stack. Used with Kubernetes (Gatekeeper), CI/CD pipelines, and API gateways.

**OWASP (Open Web Application Security Project)**: A nonprofit foundation that produces freely available articles, methodologies, documentation, tools, and technologies in the field of web application security.

### P

**PAM (Privileged Access Management)**: Controls and processes for managing, monitoring, and auditing elevated (privileged) access to sensitive systems and data.

**PCI DSS (Payment Card Industry Data Security Standard)**: A security standard for organizations that handle credit card data, maintained by the Payment Card Industry Security Standards Council.

**PIM (Privileged Identity Management)**: Azure AD feature (and equivalent capabilities in other platforms) that provides just-in-time privileged access with approval workflows and audit logging.

**Pod Security Standards (PSS)**: Kubernetes-native security profiles that define acceptable security configurations for pods: Privileged (unrestricted), Baseline (minimally restrictive), and Restricted (strongly restrictive).

**Policy-as-Code**: The practice of expressing security and compliance policies in machine-readable code that can be version-controlled, automatically evaluated, and enforced through policy engines.

**Prowler**: An open-source multi-cloud security assessment tool that checks cloud environments against CIS benchmarks, NIST, PCI DSS, ISO 27001, and other security frameworks.

### R

**RBAC (Role-Based Access Control)**: An authorization model where permissions are assigned to roles and roles are assigned to users or service accounts, rather than permissions being assigned directly.

**Runtime Security**: Security controls and monitoring that operate on running workloads to detect malicious behavior, policy violations, or anomalous activity.

### S

**SAML (Security Assertion Markup Language)**: An open standard for exchanging authentication and authorization data between identity providers and service providers. Used for enterprise SSO.

**SARIF (Static Analysis Results Interchange Format)**: A standard JSON format for reporting static analysis results. Supported by GitHub, GitLab, and other platforms for displaying security scan results in code reviews.

**SAST (Static Application Security Testing)**: Analysis of application source code, bytecode, or binary code to identify security vulnerabilities without executing the application.

**SBOM (Software Bill of Materials)**: A formal record of the components, libraries, and modules that make up a software artifact. Provides transparency into software supply chains and enables vulnerability tracking.

**SCA (Software Composition Analysis)**: Analysis of open-source and third-party components used in an application to identify known vulnerabilities and license compliance issues.

**SCPs (Service Control Policies)**: AWS Organizations policies that restrict what actions can be performed by IAM principals in member accounts, regardless of the permissions granted to those principals.

**SeccompProfile**: A security feature that filters system calls available to container processes, reducing the attack surface available to potential exploits.

**Security Group**: A stateful, instance-level virtual firewall in AWS (and equivalent concepts in other cloud providers) that controls inbound and outbound traffic for cloud resources.

**Shift Left**: The DevSecOps practice of moving security activities earlier in the development lifecycle to identify and remediate issues when they are cheapest to fix.

**SIEM (Security Information and Event Management)**: A system that collects, correlates, and analyzes security event logs from across an environment to identify threats and support incident investigation.

**Sigstore**: An open-source project providing free code signing infrastructure for open-source software. Includes Cosign (artifact signing), Fulcio (certificate authority), and Rekor (transparency log).

**SLSA (Supply Levels for Software Artifacts)**: A security framework published by Google and OpenSSF that defines levels of software supply chain security maturity, from basic build integrity to fully verifiable provenance.

**SOAR (Security Orchestration, Automation, and Response)**: Platforms that automate security response workflows, integrating with security tools to perform automated triage, enrichment, and remediation.

**SOC 2 (Service Organization Control 2)**: A framework developed by the American Institute of CPAs (AICPA) for managing customer data based on five trust service criteria: security, availability, processing integrity, confidentiality, and privacy.

**SSRF (Server-Side Request Forgery)**: A web application vulnerability that allows attackers to induce the server-side application to make HTTP requests to an arbitrary domain, including the cloud instance metadata service.

### T

**TFSec**: A static analysis tool for Terraform infrastructure as code that identifies security misconfigurations.

**Threat Modeling**: A structured approach to identifying and prioritizing potential security threats to a system, enabling proactive mitigation.

**Trivy**: An open-source vulnerability scanner for container images, filesystems, and IaC files.

**TLS (Transport Layer Security)**: A cryptographic protocol for secure communication over a network. Version 1.2 and 1.3 are current standards; 1.0 and 1.1 should be disabled.

### V

**VPC (Virtual Private Cloud)**: An isolated virtual network in a cloud environment. Equivalent concepts include Azure VNet and GCP VPC.

**VPC Flow Logs**: Network traffic logs captured for VPC network interfaces, providing visibility into traffic patterns for security monitoring and forensic investigation.

**Vault (HashiCorp Vault)**: An open-source secrets management tool that provides secure storage, dynamic secrets generation, key management, and identity-based access.

### W

**WAF (Web Application Firewall)**: A security appliance or service that monitors, filters, and blocks HTTP traffic to and from web applications, protecting against common web exploits.

**Workload Identity**: A mechanism that assigns verifiable identities to cloud workloads (pods, functions, instances) without requiring long-lived credentials. Examples: AWS IRSA, Azure Managed Identity, GCP Workload Identity Federation.

### Z

**Zero Trust**: A security architecture principle that assumes no request—whether from inside or outside the network—should be trusted by default. Every request must be authenticated, authorized, and validated.

**Zero-Day**: A vulnerability that is unknown to the software vendor and for which no patch is available, potentially exploited by attackers before the vendor becomes aware.

---

## Part 3: Documentation Index

The following index lists all documents across all Techstream framework repositories with descriptions.

### techstream-docs (This Repository)

| Document | Path | Description |
|---|---|---|
| Portal README | `README.md` | Entry point for all Techstream documentation; framework index and navigation guide |
| Introduction | `docs/introduction.md` | About Techstream, mission, vision, values, philosophy |
| Architecture | `docs/architecture.md` | Framework ecosystem map, integration patterns, reference architecture |
| Framework Portfolio | `docs/framework.md` | Complete guide to all nine frameworks |
| Implementation | `docs/implementation.md` | Adoption guidance, organizational profiles, professional services |
| Best Practices | `docs/best-practices.md` | Meta best practices for framework adoption and governance |
| Roadmap & Glossary | `docs/roadmap.md` | Future roadmap, industry trends, glossary, documentation index (this document) |

---

### devsecops-framework

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Framework overview, scope, quick start |
| Introduction | `docs/introduction.md` | DevSecOps definition, history, and industry context |
| Architecture | `docs/architecture.md` | Organizational architecture for DevSecOps teams |
| Framework | `docs/framework.md` | Core DevSecOps principles and practices |
| Implementation | `docs/implementation.md` | Implementation guidance for the core framework |
| Best Practices | `docs/best-practices.md` | DevSecOps best practices |
| Roadmap | `docs/roadmap.md` | Maturity roadmap and KPIs |

---

### secure-ci-cd-reference-architecture

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Architecture overview, scope, supported platforms |
| Introduction | `docs/introduction.md` | CI/CD security context, threat model, principles |
| Architecture | `docs/architecture.md` | Reference architecture diagrams for all supported CI/CD platforms |
| Framework | `docs/framework.md` | CI/CD security controls framework by stage |
| Implementation | `docs/implementation.md` | Platform-specific implementation guidance |
| Best Practices | `docs/best-practices.md` | CI/CD security best practices |
| Roadmap | `docs/roadmap.md` | Maturity roadmap and KPIs |

---

### release-orchestration-framework

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Framework overview, scope, use cases |
| Introduction | `docs/introduction.md` | Release security context, regulatory considerations |
| Architecture | `docs/architecture.md` | Release pipeline architecture and gate design |
| Framework | `docs/framework.md` | Release security controls framework |
| Implementation | `docs/implementation.md` | Deployment strategy implementation guidance |
| Best Practices | `docs/best-practices.md` | Release orchestration best practices |
| Roadmap | `docs/roadmap.md` | Maturity roadmap and KPIs |

---

### software-supply-chain-security-framework

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Framework overview, SLSA alignment, scope |
| Introduction | `docs/introduction.md` | Supply chain threat landscape, SolarWinds and Log4Shell context |
| Architecture | `docs/architecture.md` | Supply chain security architecture: source to deployment |
| Framework | `docs/framework.md` | Supply chain security controls framework |
| Implementation | `docs/implementation.md` | SBOM generation, artifact signing, dependency management |
| Best Practices | `docs/best-practices.md` | Supply chain security best practices |
| Roadmap | `docs/roadmap.md` | SLSA maturity roadmap and KPIs |

---

### devsecops-maturity-model

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Model overview, how to use for assessment |
| Introduction | `docs/introduction.md` | Maturity model design principles and methodology |
| Architecture | `docs/architecture.md` | Model structure: domains, levels, scoring |
| Framework | `docs/framework.md` | Complete maturity model: all domains and all levels |
| Implementation | `docs/implementation.md` | Assessment process and evidence collection guidance |
| Best Practices | `docs/best-practices.md` | Maturity assessment best practices |
| Roadmap | `docs/roadmap.md` | Using maturity model results for roadmap planning |

---

### compliance-automation-framework

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Framework overview, supported compliance frameworks |
| Introduction | `docs/introduction.md` | Compliance automation context, regulatory landscape |
| Architecture | `docs/architecture.md` | Compliance automation architecture: evidence sources and flows |
| Framework | `docs/framework.md` | Compliance controls framework with framework mapping tables |
| Implementation | `docs/implementation.md` | Evidence collection implementation by compliance framework |
| Best Practices | `docs/best-practices.md` | Compliance automation best practices |
| Roadmap | `docs/roadmap.md` | Compliance maturity roadmap and audit readiness metrics |

---

### secure-pipeline-templates

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Template library overview, supported platforms, quick start |
| Introduction | `docs/introduction.md` | Template design principles, customization guidance |
| Architecture | `docs/architecture.md` | Template architecture and composition patterns |
| Framework | `docs/framework.md` | Complete template catalog by security domain and platform |
| Implementation | `docs/implementation.md` | Template integration and customization guide |
| Best Practices | `docs/best-practices.md` | Pipeline template best practices |
| Roadmap | `docs/roadmap.md` | Template development roadmap |

---

### devsecops-methodology

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Methodology overview, target audience |
| Introduction | `docs/introduction.md` | Organizational change context, transformation philosophy |
| Architecture | `docs/architecture.md` | Transformation program architecture |
| Framework | `docs/framework.md` | Methodology framework: phases, activities, outputs |
| Implementation | `docs/implementation.md` | Transformation implementation guidance |
| Best Practices | `docs/best-practices.md` | Transformation and change management best practices |
| Roadmap | `docs/roadmap.md` | Transformation milestones and success metrics |

---

### cloud-security-devsecops

| Document | Path | Description |
|---|---|---|
| README | `README.md` | Framework overview, covered platforms, key topics |
| Introduction | `docs/introduction.md` | Cloud security fundamentals, shared responsibility model, threat landscape |
| Architecture | `docs/architecture.md` | Cloud security reference architecture for AWS, Azure, GCP |
| Framework | `docs/framework.md` | Cloud security controls framework by domain |
| Implementation | `docs/implementation.md` | Implementation phases, hardening playbooks, incident response |
| Best Practices | `docs/best-practices.md` | 35+ cloud security best practices by domain |
| Roadmap | `docs/roadmap.md` | Cloud security maturity roadmap, KPIs, investment planning |
