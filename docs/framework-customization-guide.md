# Framework Customization Guide

The Techstream framework collection is designed to be adopted as-is for organizations without domain-specific constraints, and extended for those with industry-specific regulatory, architectural, or operational requirements. This guide explains when and how to customize Techstream frameworks, how to document customizations so they remain maintainable, and how to handle the most common industry-specific extension scenarios.

---

## Table of Contents

- [Customization Philosophy](#customization-philosophy)
- [What to Customize vs. What to Accept as-is](#what-to-customize-vs-what-to-accept-as-is)
- [Customization Patterns](#customization-patterns)
- [Industry-Specific Extensions](#industry-specific-extensions)
- [Documenting Customizations](#documenting-customizations)
- [Governance for Customized Frameworks](#governance-for-customized-frameworks)
- [Keeping Customizations Aligned with Upstream](#keeping-customizations-aligned-with-upstream)

---

## Customization Philosophy

The Techstream frameworks are deliberately written to be platform-neutral, sector-agnostic, and scalable across organization sizes. This breadth is a strength for initial adoption and a limitation for specialized contexts. A financial institution operating under PCI-DSS and DORA (EU Digital Operational Resilience Act) has compliance requirements that a general DevSecOps framework cannot fully capture. A healthcare organization handling PHI under HIPAA has data handling requirements that require additional controls.

**Customization is expected and appropriate.** The Techstream frameworks provide:

- Foundational control structures and governance models
- General-purpose implementation patterns
- Industry-neutral terminology and decision frameworks
- Cross-cutting best practices applicable to most contexts

**Customization should provide:**

- Industry-specific regulatory control mappings
- Organization-specific technology stack guidance
- Internal naming conventions, tooling choices, and approval processes
- Thresholds and targets appropriate to the organization's risk tolerance

### The Customization Stack

```
┌────────────────────────────────────────────────┐
│      Organization-specific layer               │
│  Internal tooling, naming, thresholds, RACI    │
├────────────────────────────────────────────────┤
│      Industry-specific layer                   │
│  Regulatory controls, sector standards         │
├────────────────────────────────────────────────┤
│      Techstream framework layer (base)         │
│  General-purpose controls and guidance         │
└────────────────────────────────────────────────┘
```

Keep these layers separate in your documentation. When Techstream frameworks are updated, changes to the base layer should be reviewed for impact on the layers above. Mixing organization-specific guidance directly into Techstream framework files makes this review impossible.

---

## What to Customize vs. What to Accept as-is

### Accept as-is (no customization needed)

- Core security principles and their rationale
- STRIDE threat modeling methodology
- Maturity level progression criteria (Initial → Managed → Defined → Quantitatively Managed → Optimizing)
- DORA metric definitions
- SLSA level requirements
- SBOM format specifications (CycloneDX, SPDX)
- General pipeline security architecture
- Incident response process structure
- Change management governance model

These elements are grounded in industry research and standards. Customizing them typically signals a misunderstanding of the underlying security rationale rather than a genuine organizational need.

### Common customization points

| Element | Customization Examples |
|---|---|
| **Tooling choices** | "We use Wiz for CSPM instead of Prowler" |
| **Approval authorities** | "Our CAB is chaired by the CISO, not the CTO" |
| **Thresholds** | "Our SLO for critical vulnerability remediation is 3 days, not 7" |
| **Regulatory mappings** | "Add FedRAMP High control mappings" |
| **Data classification tiers** | "We use four tiers: Public, Internal, Confidential, Restricted" |
| **Environment names** | "We use dev/qa/staging/prod, not dev/test/staging/production" |
| **Team structure references** | "Our equivalent of 'Release Manager' is 'Deployment Lead'" |
| **Pipeline templates** | "Add steps for our internal artifact registry" |
| **Scanning thresholds** | "Block on MEDIUM and above (we operate in regulated environment)" |

---

## Customization Patterns

### Pattern 1: Extension Documents

Create supplementary documents that extend the base framework without modifying it. Reference the base framework document and add organization-specific guidance.

**Example: Financial services extension to the Compliance Automation Framework**

```markdown
# Compliance Automation Framework — Financial Services Extension

This document extends the [Techstream Compliance Automation Framework](../compliance-automation-framework/docs/framework.md)
with controls specific to financial services regulation.

## Additional Regulatory Scope (Financial Services)

The following frameworks apply to this organization in addition to the base
Techstream framework scope (SOC 2, ISO 27001):

- PCI-DSS 4.0 (payment processing systems)
- EU DORA (Digital Operational Resilience Act) — effective January 2025
- NYDFS Part 500 (New York entities)
- FFIEC CAT (Federal Financial Institutions Examination Council)

## Extended Control Catalog

[Additional controls specific to these frameworks...]

## ICT Risk Management (EU DORA Article 6)

[Specific guidance for DORA ICT risk management requirements...]
```

**Advantages of extension documents:**
- Base framework remains unmodified and upgradeable
- Clear attribution of organization-specific requirements
- Extensions can be updated independently
- New team members understand what is standard vs. customized

### Pattern 2: Configuration Overrides

For pipeline templates and assessment scorecards, use configuration files to override defaults without modifying the template source.

**Checkov override configuration (`.checkov.yaml`):**

```yaml
# .checkov.yaml — Organization-specific Checkov configuration
# Extends the Techstream Secure Pipeline Templates default configuration.

# Environment: Financial services (PCI-DSS scope)
# Stricter than Techstream defaults — block on MEDIUM and above
soft_fail_on:
  - LOW

# Checks suppressed with documented justification
skip-check:
  - CKV_AWS_18    # S3 access logging: covered by centralized S3 Access Log aggregation
                  # Justification: JIRA SEC-2341, approved by security team 2024-11-15
  - CKV_K8S_28   # readOnlyRootFilesystem: not compatible with our legacy service mesh
                  # Justification: tracked in JIRA SEC-2891 for remediation in Q2 2025

# Additional compliance framework checks (PCI-DSS)
framework:
  - terraform
  - cloudformation
  - kubernetes
  - pci_dss       # Enable PCI-DSS-specific checks
```

**Assessment scorecard override (industry targets):**

```yaml
# assessment-config.yaml — Industry-specific maturity targets
# Overrides Techstream DevSecOps Maturity Model default targets.

# Healthcare organization (HIPAA scope)
organization_profile: healthcare

# More aggressive targets for high-sensitivity domains
domain_targets:
  culture_and_organization: 3          # Techstream default: 3
  requirements_and_design: 4           # Techstream default: 3 — elevated for HIPAA
  development_practices: 3
  ci_cd_security: 4                    # Techstream default: 3 — PHI pipeline requirements
  security_testing: 4                  # Techstream default: 3 — HIPAA §164.306(a)(1)
  infrastructure_security: 4           # Techstream default: 3
  operations_and_monitoring: 4         # Techstream default: 3
  governance_and_compliance: 5         # Techstream default: 4 — HIPAA audit requirements

# Industry-specific vulnerability remediation SLAs
vulnerability_slas:
  critical:
    days: 3                            # Techstream default: 7 — tighter for healthcare
  high:
    days: 14                           # Techstream default: 30
  medium:
    days: 45                           # Techstream default: 90
```

### Pattern 3: Role Mapping Documents

When organizational titles don't map cleanly to Techstream's generic role names, maintain an explicit mapping document rather than renaming roles throughout the framework documents.

```markdown
# Role Mapping — Acme Corp to Techstream Framework

This document maps Acme Corp's internal role titles to the Techstream
framework role definitions.

| Techstream Role | Acme Corp Title | Notes |
|---|---|---|
| Release Manager | Deployment Lead | Multiple Deployment Leads; one per product group |
| Release Train Engineer (RTE) | Program Increment Lead | SAFe-aligned title |
| Service Owner / Tech Lead | Engineering Manager (EM) | EM holds both responsibilities at Acme |
| Platform / DevOps Engineer | Site Reliability Engineer (SRE) | Deployment execution is an SRE responsibility |
| Change Advisory Board (CAB) | Engineering Review Board (ERB) | Meets bi-weekly; not per-change |
| Emergency Change Authority (ECA) | On-call VP Engineering | On-call VP has emergency change authority |
| Security Champion | Security Guild Member | Champions are called "guild members" internally |
```

---

## Industry-Specific Extensions

### Financial Services (FSI)

**Applicable frameworks:** PCI-DSS 4.0, EU DORA, FFIEC CAT, NYDFS Part 500, SOX (for public companies), MAS TRM (Singapore)

**Key customization areas:**

**1. Third-party / ICT risk management (EU DORA)**

DORA requires formal ICT third-party risk management that exceeds the Techstream Software Supply Chain Security Framework's scope. Add:
- ICT service provider register with criticality classification
- Contractual security requirements for ICT providers
- Exit strategy requirements for critical ICT dependencies
- Concentration risk monitoring (multiple critical services on same cloud provider)

**2. Operational resilience testing (DORA Article 25)**

DORA mandates Threat-Led Penetration Testing (TLPT) for significant financial entities. Extend the [Secure CI/CD Reference Architecture](../secure-ci-cd-reference-architecture/docs/introduction.md) with:
- Annual TLPT scope and methodology documentation
- TIBER-EU/CBEST alignment (where applicable)
- Red team exercise integration with the release freeze calendar

**3. PCI-DSS cardholder data environment (CDE) segmentation**

Pipeline controls for CDE-scope services require additional isolation:
- Dedicated CI/CD runners for CDE scope (no shared compute with out-of-scope workloads)
- Network segmentation between CDE and non-CDE pipeline infrastructure
- Quarterly ASV scans (external) and monthly internal scans (automated)
- File integrity monitoring on CDE build systems

**4. Change management alignment with ITIL and FFIEC**

Financial regulators expect formal change management processes. The [Release Orchestration Framework](../release-orchestration-framework/docs/framework.md) maps to ITIL 4, but FSI-specific additions include:
- Pre-approval requirements for changes affecting trading systems
- Market-hours change windows (no production changes during trading hours)
- T+2 settlement cycle awareness in deployment scheduling

### Healthcare (HIPAA/HITECH)

**Applicable frameworks:** HIPAA Security Rule (45 CFR Part 164), HITECH, HITRUST CSF, SOC 2

**Key customization areas:**

**1. PHI data classification and pipeline controls**

All pipelines handling Protected Health Information (PHI) require:
- PHI classifier labels in IaC and container image tags
- Separate scanning thresholds: block on MEDIUM and above for PHI-scope services
- No PHI in CI/CD logs (log sanitization step in pipeline)
- Dedicated compute for PHI-scope build jobs

**2. Business Associate Agreement (BAA) for tooling**

Any security tooling that processes PHI (directly or through logs) requires a BAA:
- SAST tools that scan PHI-processing code: verify BAA or ensure no PHI in code/test data
- Container scanning tools: verify BAA if images may contain PHI in layer metadata
- SBOM tools: PHI must not appear in SBOM component descriptions

**3. Audit log requirements (HIPAA §164.312(b))**

HIPAA requires audit logs for all PHI access. Pipeline evidence collection must include:
- Access logs for all PHI-scope CI/CD systems
- 6-year retention for all HIPAA-related audit logs (longer than Techstream's default 3-year recommendation for most evidence types)
- Audit log integrity controls (tamper-evidence)

**4. Minimum necessary principle in scanning**

SAST and dynamic analysis tools must not receive PHI in test data:
- Test data generation policies prohibiting real PHI in test environments
- Synthetic data generation tooling requirements
- Data masking pipeline for any PHI that exists in non-production environments

### Government and Public Sector

**Applicable frameworks:** FedRAMP (US), Cyber Essentials (UK), ASD Essential Eight (Australia), IRAP (Australia), BSI IT-Grundschutz (Germany)

**Key customization areas:**

**1. FedRAMP-specific control baselines**

FedRAMP maps to NIST 800-53 Rev 5. The Techstream Compliance Automation Framework covers NIST 800-53, but FedRAMP adds:
- Authorization Boundary documentation requirements
- Continuous monitoring (ConMon) reporting frequency requirements (monthly POA&M updates)
- Penetration testing requirements aligned to FedRAMP Penetration Test Guidance
- Third-party assessment organization (3PAO) engagement requirements

**2. Cryptographic algorithm restrictions**

US government systems must use NIST-approved cryptographic algorithms (FIPS 140-2/3). Extend pipeline controls:
```yaml
# FIPS-compliant build environment check
- name: Verify FIPS-compliant cryptography
  run: |
    # Check that FIPS mode is enabled on build runners
    if [ "$(cat /proc/sys/crypto/fips_enabled 2>/dev/null)" != "1" ]; then
      echo "FAIL: FIPS mode not enabled on build runner"
      exit 1
    fi

    # Scan for non-approved algorithms in code
    # MD5 and SHA-1 are prohibited for security purposes in FedRAMP
    if grep -r "MD5\|SHA1\|DES\|3DES\|RC4" src/ --include="*.go" --include="*.py" --include="*.java"; then
      echo "WARNING: Potentially non-FIPS-approved algorithm references found"
    fi
```

**3. Supply chain and software provenance (EO 14028)**

US federal contractors must comply with NIST SSDF and provide SBOMs. The Techstream Software Supply Chain Security Framework covers SBOM generation, but federal-specific additions include:
- SBOM sharing with federal agencies on request
- Provenance attestation aligned to NIST SP 800-218A
- VEX document generation and maintenance obligations

### Software as a Service (SaaS) Providers

**Key customization areas:**

**1. Multi-tenant isolation controls**

SaaS providers with multi-tenant architectures require tenant isolation controls that the base Techstream frameworks don't cover:
- Tenant-boundary threat modeling (customer data must never be accessible cross-tenant)
- Tenant isolation testing in DAST scope (use dedicated tenant pairs for cross-tenant testing)
- Tenant-scoped access tokens and API key validation in SAST rules

**2. Shared responsibility documentation**

SaaS providers must clearly document the shared responsibility model for their customers:
- Which security controls the provider manages
- Which controls the customer configures
- How customers can verify provider-side controls (SOC 2 report, trust portal)

**3. Customer-facing security SLAs**

Service-level agreements with security components require measurement and reporting:
- Vulnerability disclosure timelines (e.g., "critical vulnerabilities patched within 72 hours")
- Penetration testing frequency and scope disclosure
- Incident notification timelines (e.g., "customers notified within 72 hours of confirmed breach")

---

## Documenting Customizations

### Customization Decision Record Format

Every customization from the Techstream base should be documented with a Customization Decision Record (CDR):

```markdown
# CDR-001: Vulnerability Remediation SLA Reduction

**Date:** 2025-03-15
**Status:** Approved
**Approver:** CISO

## Summary

This CDR documents our decision to use more aggressive vulnerability remediation
SLAs than the Techstream DevSecOps Maturity Model defaults.

## Context

The Techstream framework defaults (Critical: 7 days; High: 30 days) are designed
for general-purpose DevSecOps programs. As a financial services organization
processing payment card data, we operate under PCI-DSS 6.3.3, which requires
"all applicable security patches and updates" to be applied within one month for
critical patches — but our interpretation, confirmed with our QSA, is that
internally discovered critical vulnerabilities should be treated more urgently.

## Decision

We adopt the following SLAs:
- Critical: 3 days
- High: 14 days
- Medium: 45 days
- Low: 90 days

## Rationale

1. Reduced window between vulnerability identification and exploitation (threat landscape)
2. QSA expectation alignment for PCI-DSS annual assessment
3. Our automated remediation pipeline (Renovate + automated PR merge) makes
   sub-7-day remediation achievable without excessive operational burden

## Impact

- Dependency-Track SLA alerts reconfigured to these thresholds
- Weekly vulnerability SLA report targets updated
- Team OKRs updated to reflect new targets
- Existing open findings will be grandfathered at original SLAs for 30 days

## Review Schedule

Annual review at the start of each fiscal year, or immediately upon change to
PCI-DSS requirements or significant threat landscape shift.
```

### Customization Registry

Maintain a central registry of all active customizations in your documentation system:

| CDR | Summary | Framework Affected | Approved | Review Date |
|---|---|---|---|---|
| CDR-001 | Vulnerability SLA reduction | DevSecOps Maturity Model | 2025-03-15 | 2026-03-01 |
| CDR-002 | FedRAMP control mappings | Compliance Automation | 2025-01-10 | 2026-01-01 |
| CDR-003 | PHI pipeline isolation | Secure Pipeline Templates | 2024-11-20 | 2025-11-01 |

---

## Governance for Customized Frameworks

### Approval Process for Customizations

Not all customizations require the same level of oversight:

| Customization Type | Approval Required | Review Frequency |
|---|---|---|
| Threshold tightening (more restrictive than base) | Team Lead | Annually |
| Threshold relaxation (less restrictive than base) | CISO + documented justification | Every 6 months |
| Additional regulatory control mappings | Security Architect | Annually or on regulatory change |
| Tool substitution (same function, different tool) | Security team | On substitution |
| Exception to a baseline control | CISO + risk acceptance | Per-exception; 90-day expiry |
| New industry-specific control | Security Architect + Compliance | On introduction |

### Preventing Customization Drift

Customizations that are never reviewed become outdated and may no longer reflect the actual state of controls. Prevent drift through:

1. **Annual review calendar** — schedule CDR reviews at the start of each year
2. **Trigger-based review** — regulatory changes, significant security incidents, or major architecture changes trigger CDR review
3. **Automated staleness detection** — alert when a CDR's review date has passed without a documented review
4. **Retirement process** — when a customization is no longer needed, retire the CDR and restore the base framework default

---

## Keeping Customizations Aligned with Upstream

### Monitoring Techstream Framework Updates

Subscribe to the Techstream repositories to receive notifications of significant updates:

- Watch the GitHub repositories for tagged releases
- Review release notes for changes to areas you have customized
- Assess whether upstream changes should be incorporated into your customizations or whether your CDRs still provide valid justification for deviation

### Update Impact Assessment

When Techstream frameworks are updated, assess impact before incorporating:

| Change Type | Impact Assessment Required | Incorporation Timeline |
|---|---|---|
| New optional section added | Low — review for applicability | Within 60 days |
| Existing guidance updated | Medium — compare against your CDRs | Within 30 days |
| New control added to baseline | High — assess against your environment | Within 14 days |
| Control requirement tightened | High — assess gap; may require CDR update | Within 14 days |
| Significant architecture change | High — full review by security architect | Within 14 days |

---

## Related Techstream Resources

- [Techstream Docs — Framework Selection Guide](framework-selection-guide.md)
- [Techstream Docs — Integration Scenarios](integration-scenarios.md)
- [Compliance Automation Framework — Regulatory Controls Matrix](../compliance-automation-framework/docs/regulatory-controls-matrix.md)
- [DevSecOps Maturity Model — Assessment Scorecard](../devsecops-maturity-model/docs/assessment-scorecard.md)
- [Secure Pipeline Templates — Hardening Checklist](../secure-pipeline-templates/docs/hardening-checklist.md)
