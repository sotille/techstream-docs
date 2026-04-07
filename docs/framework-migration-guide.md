# Framework Migration and Upgrade Guide

Adopting the Techstream framework ecosystem is not a one-time event. Organizations progress through maturity levels, add frameworks as their program matures, and eventually need to update implementations to newer framework revisions. This guide covers three migration scenarios: advancing between maturity levels within a single framework, adding a new Techstream framework to an existing program, and transitioning from a legacy or third-party framework to the Techstream ecosystem.

---

## Migration Scenario 1: Advancing Maturity Levels

The [DevSecOps Maturity Model](../../devsecops-maturity-model/docs/framework.md) defines five maturity levels. Advancing from one level to the next requires deliberate capability additions — not just time in service.

### Maturity Level Transition Checklist

**Level 1 → Level 2: From Ad Hoc to Managed**

At Level 1, security controls exist in some pipelines but are not consistently applied and are not enforced. The Level 2 transition establishes consistent baselines.

| Capability | Level 1 State | Level 2 Target | Migration Action |
|-----------|--------------|----------------|-----------------|
| Pipeline security scanning | Present in some repositories | All repositories have SAST and SCA on every PR | Adopt the [Secure Pipeline Templates](../../secure-pipeline-templates/README.md); apply to all repos via platform template or organization-level workflow |
| Vulnerability tracking | Ad hoc (scanner output reviewed manually) | Findings tracked in a central tool with SLA enforcement | Deploy Dependency-Track or DefectDojo; configure scanner integrations to push findings |
| Secrets management | Mix of hardcoded secrets and environment variables | All secrets in a secrets manager; no hardcoded credentials | Scan git history with TruffleHog; rotate detected secrets; migrate to Vault or cloud KMS |
| Branch protection | Inconsistent | Protected main branches with required reviews on all repos | Platform-level branch protection policy via GitHub organization rulesets or GitLab group settings |
| Artifact signing | None | Images signed before deployment | Integrate Cosign keyless signing into all pipeline templates |

**Level 2 → Level 3: From Managed to Defined**

At Level 2, controls are consistently applied. Level 3 requires formal process definitions, metrics collection, and organizational role clarity.

| Capability | Level 2 State | Level 3 Target | Migration Action |
|-----------|--------------|----------------|-----------------|
| Security metrics | No formal metrics | Lead and lag indicators tracked; dashboard published | Implement the [DevSecOps Metrics and KPI Guide](../../devsecops-maturity-model/docs/metrics-kpis.md) |
| Security champion program | Informal champions exist | Formal program with training curriculum and responsibilities | Implement the [Security Champion Program](../../devsecops-methodology/docs/security-champion-program.md) |
| SBOM program | SBOM generated in some pipelines | SBOM generated for all releases; stored and queryable | Standardize on CycloneDX; integrate with Dependency-Track; coverage ≥95% |
| Threat modeling | None or ad hoc | Threat modeling in Definition of Done for new features | Train teams on STRIDE/PASTA; create threat model template; track in backlog |
| Compliance automation | Manual evidence collection | Automated evidence collection for primary compliance framework | Adopt the [Compliance Automation Framework](../../compliance-automation-framework/docs/introduction.md) |

**Level 3 → Level 4: From Defined to Quantitatively Managed**

| Capability | Level 3 State | Level 4 Target | Migration Action |
|-----------|--------------|----------------|-----------------|
| Metrics-driven decisions | Metrics collected but not used for decisions | Program decisions (tool selection, resource allocation) backed by metrics data | Define decision criteria; establish review cadence; link metrics to OKRs |
| Predictive risk scoring | Reactive vulnerability response | Risk scoring model that predicts vulnerability likelihood; proactive controls | Implement EPSS scoring alongside CVSS; build risk model with historical data |
| Compliance continuous monitoring | Periodic assessments | Continuous compliance dashboards with real-time control status | Deploy Policy-as-Code (OPA/Kyverno); integrate with GRC platform |
| Supply chain attestations | SBOM generated | SLSA Level 2+ provenance attestations on all builds | Add SLSA provenance generation to CI pipeline; enforce at deployment |

**Level 4 → Level 5: From Quantitatively Managed to Optimizing**

| Capability | Level 4 State | Level 5 Target | Migration Action |
|-----------|--------------|----------------|-----------------|
| Security feedback loops | Metrics tracked | Metrics actively drive automated control tuning | Build feedback loops: false positive rates reduce scanner thresholds; coverage gaps trigger auto-remediation |
| Industry contribution | Internal best practices | Contributing to industry standards (OWASP, CNCF, SLSA WG) | Participate in working groups; open source tools or configurations |
| Zero-trust architecture | Network-level controls | Application-level zero trust; continuous verification | Implement service mesh with mTLS; replace VPN with ZTNA; bind workload identity to every request |

---

## Migration Scenario 2: Adding a New Framework to an Existing Program

As organizations mature, they add Techstream frameworks to address new areas. This scenario covers the recommended sequence and integration steps when adding a framework to a functioning DevSecOps program.

### Recommended Framework Adoption Sequence

If starting from zero, adopt frameworks in this order:

```
Stage 1 (Foundation):
  devsecops-maturity-model          ← Establish baseline before making changes
  devsecops-framework               ← Define principles and lifecycle
  secure-pipeline-templates         ← Implement pipeline controls

Stage 2 (Specialization):
  secure-ci-cd-reference-architecture  ← Deepen pipeline security
  software-supply-chain-security-framework ← Supply chain integrity
  compliance-automation-framework   ← Automate compliance evidence

Stage 3 (Optimization):
  release-orchestration-framework   ← Release governance at scale
  cloud-security-devsecops          ← Cloud security depth
  devsecops-methodology             ← Transformation methodology
  techstream-docs                   ← Central navigation (always current)
```

### Adding the Compliance Automation Framework to an Existing Program

If you have an existing DevSecOps program and are adding compliance automation:

**Pre-migration inventory:**

1. Run the [DevSecOps Maturity Assessment Scorecard](../../devsecops-maturity-model/docs/assessment-scorecard.md) to establish the current baseline
2. Identify your primary compliance framework (SOC 2, ISO 27001, PCI-DSS, FedRAMP)
3. Inventory existing evidence collection: what is already automated vs. manual
4. Map existing controls to the [Regulatory Controls Matrix](../../compliance-automation-framework/docs/regulatory-controls-matrix.md) to identify gaps

**Migration steps:**

1. Deploy the [Compliance Automation Framework architecture](../../compliance-automation-framework/docs/architecture.md) — Policy-as-Code engine, evidence collection pipelines, centralized evidence store
2. Connect existing CI/CD scan outputs (SAST, SCA, container scan results) as evidence sources
3. Implement automated evidence collection for the highest-gap areas first
4. Integrate with your GRC platform (ServiceNow GRC, Vanta, Drata, Tugboat Logic)
5. Run a shadow audit: collect evidence automatically while your team also collects manually; compare for 30 days
6. Sunset manual collection for controls where automated collection passes the shadow audit

### Adding the Software Supply Chain Security Framework to an Existing Program

**Assessment questions before migration:**

- Are SBOMs currently generated? For what percentage of builds?
- Are artifacts signed? What signing mechanism is used?
- Is there a third-party component risk assessment process?
- How quickly can you respond to a supply chain incident (e.g., new critical CVE in a widely-used dependency)?

**Migration path:**

| Phase | Duration | Deliverables |
|-------|---------|-------------|
| 1. Baseline | Weeks 1-2 | Run the [Open Source Component Assessment](../../software-supply-chain-security-framework/docs/open-source-component-assessment.md) for the 10 most critical applications; establish SBOM generation coverage metric |
| 2. Standardize | Weeks 3-6 | Standardize SBOM format (CycloneDX) and generation tooling (Syft/Trivy); integrate SBOM storage into artifact registry; SBOM coverage ≥80% |
| 3. Harden | Weeks 7-12 | Implement artifact signing (Cosign keyless); integrate with Dependency-Track for continuous monitoring; SLSA Level 1 attestations |
| 4. Govern | Weeks 13-20 | Achieve SLSA Level 2; implement VEX workflow; policy enforcement for component approval; incident response drill for supply chain attack |

---

## Migration Scenario 3: Transitioning from a Legacy Framework

Organizations migrating from existing frameworks (OWASP SAMM self-assessment, BSIMM, or internally developed frameworks) should preserve value from prior investments while integrating Techstream.

### Mapping OWASP SAMM to Techstream

If you have an OWASP SAMM assessment, use the following mapping to identify which Techstream frameworks address each SAMM practice:

| OWASP SAMM Practice | Primary Techstream Framework | Supporting Framework |
|--------------------|-----------------------------|--------------------|
| **Governance: Strategy and Metrics** | devsecops-maturity-model | devsecops-methodology |
| **Governance: Policy and Compliance** | compliance-automation-framework | devsecops-framework |
| **Governance: Education and Guidance** | devsecops-methodology | devsecops-framework |
| **Design: Threat Assessment** | devsecops-framework | secure-ci-cd-reference-architecture |
| **Design: Security Requirements** | devsecops-framework | devsecops-methodology |
| **Design: Security Architecture** | devsecops-framework | cloud-security-devsecops |
| **Implementation: Secure Build** | secure-pipeline-templates | secure-ci-cd-reference-architecture |
| **Implementation: Secure Deployment** | release-orchestration-framework | secure-pipeline-templates |
| **Implementation: Defect Management** | devsecops-maturity-model | compliance-automation-framework |
| **Verification: Architecture Assessment** | secure-ci-cd-reference-architecture | cloud-security-devsecops |
| **Verification: Requirements Testing** | secure-pipeline-templates | devsecops-framework |
| **Verification: Security Testing** | secure-pipeline-templates | devsecops-framework |
| **Operations: Incident Management** | cloud-security-devsecops | devsecops-methodology |
| **Operations: Environment Management** | cloud-security-devsecops | release-orchestration-framework |
| **Operations: Operational Management** | release-orchestration-framework | devsecops-maturity-model |

### Transition from Internally Developed Frameworks

Internal frameworks often contain organization-specific customizations that cannot be discarded. When transitioning:

1. **Preserve organization-specific controls.** Techstream provides a baseline; customize rather than replacing internal governance requirements.
2. **Run frameworks in parallel for 60 days.** Use the Techstream maturity assessment alongside your existing assessment to calibrate scores before switching.
3. **Migrate tooling incrementally.** Do not replace all security tools simultaneously. Use the [Tool Selection Decision Matrix](framework-selection-guide.md) to prioritize migration.
4. **Retain audit history.** Prior compliance evidence and assessment results must remain accessible. Techstream's compliance automation framework is additive — it adds new automated evidence without requiring prior manual evidence to be discarded.

---

## Version Compatibility and Breaking Changes

When Techstream framework documents are updated, some changes are additive (new sections, additional guidance) while others may represent material changes to recommended practices. This section describes how to evaluate the impact of framework updates.

### How to Evaluate Framework Updates

1. **Read the roadmap section** in each framework (`docs/roadmap.md`) to understand planned changes.
2. **Review git history** for the specific file — commit messages describe what changed and why.
3. **Use the cross-reference map** in [techstream-docs architecture](architecture.md) to identify downstream documents that reference a changed framework section.

### Impact Assessment Matrix

| Change Type | Impact | Action Required |
|------------|--------|----------------|
| New section or additional guidance | Additive; no existing behavior changed | Review and adopt when relevant; no urgency |
| Revised security control recommendation | May require pipeline or IaC updates | Assess gap; create implementation backlog item |
| Changed tool recommendation (e.g., primary scanner replaced) | Tool migration may be required | Evaluate current tool maturity; plan migration sprint |
| Revised compliance control mapping | May affect evidence sufficiency for auditors | Verify with auditor if current evidence still sufficient |
| Deprecated practice | Current implementation may become non-compliant | Remove deprecated practice from pipelines within 90 days |

---

## Migration Anti-Patterns

Avoid these common mistakes when migrating between maturity levels or adopting new frameworks.

| Anti-Pattern | Why It Fails | Better Approach |
|-------------|-------------|----------------|
| **Big-bang adoption** | Implementing all controls in all repositories simultaneously causes scanner alert fatigue and organizational resistance | Pilot in 3-5 repositories; build confidence; roll out incrementally |
| **Skipping the baseline assessment** | Without a baseline, you cannot measure progress or identify the highest-value improvements | Always run the maturity assessment before and after a migration phase |
| **Treating documentation as the deliverable** | Completing the SSP, policy documents, or maturity assessment without implementing the underlying controls | Evidence of implementation is always required; documentation without automation has no security value |
| **Ignoring developer experience** | Implementing security gates without measuring and managing developer friction | Track "developer friction score" (scan time, false positive rate, blocking events); iterate on tooling to reduce friction |
| **Migrating tools without migrating process** | Replacing Checkmarx with Semgrep but keeping the same quarterly manual review process | Tool migration is an opportunity to automate what was previously manual |

---

## Cross-References

| Topic | Document |
|-------|---------|
| Framework selection for new programs | [Framework Selection Guide](framework-selection-guide.md) |
| Integration scenarios for specific goals | [Integration Scenarios](integration-scenarios.md) |
| Framework customization | [Framework Customization Guide](framework-customization-guide.md) |
| DevSecOps maturity levels (detailed) | [DevSecOps Maturity Model](../../devsecops-maturity-model/docs/framework.md) |
| Maturity assessment scorecard | [Assessment Scorecard](../../devsecops-maturity-model/docs/assessment-scorecard.md) |
| Transformation methodology | [DevSecOps Methodology](../../devsecops-methodology/docs/framework.md) |
