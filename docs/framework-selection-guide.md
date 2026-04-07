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
├─ We want to improve release governance and reduce deployment risk
│   └─ Start with: release-orchestration-framework
│       Then add: secure-pipeline-templates → devsecops-framework
│
└─ We are adopting AI coding assistants or deploying AI/LLM-powered applications
    └─ Start with: secure-ci-cd-reference-architecture (ai-assisted-development.md)
        Then add: software-supply-chain-security-framework (ML-1/ML-2/ML-3 controls)
                  → devsecops-framework (ai-security.md)
                  → secure-pipeline-templates (AI agent controls in hardening checklist)
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

### AI-Native Organization (significant AI/LLM product surface or AI-assisted development)

**Goal:** Extend the DevSecOps baseline to cover AI-specific supply chain risks, agentic pipeline threats, and AI/ML model security — without sacrificing deployment velocity.

**Recommended sequence:**

| Step | Framework | Focus |
|------|-----------|-------|
| 1 | `secure-ci-cd-reference-architecture` | Deploy AI-assisted development controls: slopsquatting detection, LLM input/output schema validation, AI pipeline agent authorization boundaries |
| 2 | `software-supply-chain-security-framework` | Enable Model Bill of Materials (ML-SBOM), model provenance verification, and private model registry |
| 3 | `devsecops-framework` | Apply AI security guidance for LLM applications: prompt injection, OWASP LLM Top 10, MCP server controls |
| 4 | `secure-pipeline-templates` | Add AI agent pipeline controls to the hardening checklist; enforce new-dependency approval for AI-suggested packages |
| 5 | `devsecops-maturity-model` | Assess AI security maturity; add AI/ML-specific KPIs to existing domain scoring |
| 6 | `devsecops-methodology` | Extend anti-patterns training to cover AI anti-patterns (hallucinated dependencies, AI code review as sole gate, agents without authorization boundaries) |

**Key integration touchpoints for AI-native development:**

| AI Risk | Primary Framework | Specific Document |
|---------|------------------|-------------------|
| Hallucinated/slopsquatted packages | `secure-ci-cd-reference-architecture` | [ai-assisted-development.md](../../secure-ci-cd-reference-architecture/docs/ai-assisted-development.md) |
| AI model supply chain compromise | `software-supply-chain-security-framework` | ML-1/ML-2/ML-3 controls in [framework.md](../../software-supply-chain-security-framework/docs/framework.md) |
| AI model incident response | `software-supply-chain-security-framework` | [incident-response-playbook.md](../../software-supply-chain-security-framework/docs/incident-response-playbook.md) — Playbook 5 |
| Agentic pipeline threats | `secure-ci-cd-reference-architecture` | [threat-model.md](../../secure-ci-cd-reference-architecture/docs/threat-model.md) — AI-Augmented Pipeline Threats |
| LLM application security | `devsecops-framework` | [ai-security.md](../../devsecops-framework/docs/ai-security.md) |
| AI anti-patterns | `devsecops-methodology` | [anti-patterns.md](../../devsecops-methodology/docs/anti-patterns.md) — AI/Agentic section |

**Milestone:** At 90 days, all AI-assisted development pipelines should have slopsquatting detection, new-dependency approval policies, and model hash verification for any production ML models. AI agent authorization must be enforced at the IAM layer, not solely at the prompt layer.

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

## Common Implementation Blockers and Workarounds

Organizations frequently encounter organizational or technical constraints that prevent the clean adoption path described above. This section addresses the most common blockers encountered during real-world Techstream framework adoptions.

### Blocker 1: No IaC-managed infrastructure yet

**Symptom:** The team manually provisions cloud resources through the console. No Terraform, Pulumi, or CDK exists for production infrastructure.

**Impact:** Blocks `compliance-automation-framework` (which assumes IaC as the source-of-truth for configuration evidence) and `cloud-security-devsecops` (which uses IaC policy gates to enforce controls).

**Workaround — staged path:**

1. **Do not delay compliance automation entirely.** Start with evidence collection from existing sources: cloud provider APIs (AWS Config, Azure Policy, GCP Security Command Center) can be queried directly by Prowler, Steampipe, or cloud-native compliance dashboards. These produce evidence without requiring IaC.
2. **Begin IaC adoption in parallel for new resources only.** Do not attempt a "big bang" IaC migration. Every new resource provisioned from this point forward uses IaC. Over 6–12 months, coverage grows organically.
3. **Use `compliance-automation-framework` evidence collection scripts in API-query mode** rather than IaC-scan mode during the transition. The `regulatory-controls-matrix.md` identifies which controls can be evidenced by API query vs. which require IaC policy assertion.

**Milestone to unlock full adoption:** 50% of production resources managed by IaC and IaC repositories connected to CI/CD with policy scanning active.

---

### Blocker 2: Legacy CI/CD platform (Jenkins, Bamboo, TeamCity) with no pipeline-as-code

**Symptom:** CI/CD pipelines are configured through UI wizards. No YAML or Groovy pipeline definitions exist in version control.

**Impact:** Blocks `secure-pipeline-templates` adoption (templates are pipeline-as-code only). Partially blocks `secure-ci-cd-reference-architecture` (some controls assume scriptable pipelines).

**Workaround:**

1. **Use `secure-ci-cd-reference-architecture/docs/legacy-cicd-migration.md`** for a staged migration path from Jenkins, Bamboo, and TeamCity to pipeline-as-code.
2. **Start with security scanning as standalone stages, not integrated templates.** Run Trivy, Semgrep, and Gitleaks as standalone jobs triggered on a schedule or via webhook, independent of the existing pipeline UI. This delivers security value immediately without requiring pipeline migration.
3. **Target new projects for pipeline-as-code from day one.** Freeze legacy pipeline configuration for existing projects; use the migration guide to convert high-priority projects in each quarter.
4. **Use the Jenkins Declarative Pipeline template** (`secure-pipeline-templates/templates/jenkins-secure-pipeline.groovy`) as the migration target format — it is designed for Jenkins environments.

**Milestone to unlock full adoption:** At least one production pipeline migrated to pipeline-as-code and validated against the security template.

---

### Blocker 3: No container runtime; workloads run directly on VMs or bare metal

**Symptom:** Applications run as systemd services, EC2 instances with AMIs, or on-premises servers — not in containers. No Kubernetes.

**Impact:** Blocks most of `cloud-security-devsecops` (primarily Kubernetes-focused), container-centric steps in `secure-pipeline-templates`, and some SBOM generation approaches.

**Workaround:**

1. **SBOM generation is not container-specific.** Use `syft` in filesystem mode to generate SBOMs for VM-based workloads: `syft scan dir:/opt/myapp -o cyclonedx-json`. SBOM generation, VEX, and supply chain controls all apply to non-container artifacts.
2. **For `cloud-security-devsecops`:** Focus on the non-Kubernetes sections — IAM hardening, VPC/network controls, CSPM (Prowler/Security Hub), CloudTrail, and infrastructure hardening. Skip or defer the Kubernetes-specific sections (Cilium, Tetragon, OPA Gatekeeper, Kyverno) until container adoption begins.
3. **For `secure-pipeline-templates`:** The SAST, SCA, secrets scanning, and artifact signing stages are all applicable to VM-based workloads. Skip or adapt the container build/push/deploy stages. The `iac-security-pipeline.yml` template applies fully for infrastructure-focused teams.
4. **Treat VM AMIs as the artifact unit.** Apply Cosign signing to AMI manifests or deployment manifests. The supply chain security framework's provenance and signing controls apply to any artifact type.

**Milestone to unlock full adoption:** Container runtime in use for at least one production workload; Kubernetes cluster operational.

---

### Blocker 4: No centralized Git repository for infrastructure or configuration

**Symptom:** Infrastructure is managed ad hoc. Some scripts exist on individual engineers' laptops. No authoritative source-of-truth for configuration.

**Impact:** Blocks GitOps adoption (requires a Git config repository), `release-orchestration-framework` (assumes PR-based promotion), and `compliance-automation-framework` (IaC configuration evidence).

**Workaround:**

1. **This is a foundational gap that limits all other framework adoption.** Resolve it before attempting any other framework. The effort is low: create a Git repository and begin committing configuration files.
2. **Start with the most critical configuration first:** secrets rotation scripts, IAM policies, firewall rules. Import them into Git in their current state — imperfect configuration under version control is better than perfect configuration that exists only in someone's head.
3. **Use `devsecops-methodology/docs/brownfield-adoption-guide.md`** (available in `devsecops-framework`) for the organizational change management approach to getting teams to commit to centralized configuration management.
4. **Set a freeze policy:** from a defined date, no infrastructure change is accepted without a corresponding Git commit. Enforce this at the team level before enforcing it technically.

**Milestone to unlock full adoption:** All production infrastructure configuration committed to a centralized Git repository with at least one other engineer having reviewer access.

---

### Blocker 5: Insufficient CI/CD platform permissions (no secrets access, no service account)

**Symptom:** The CI/CD platform runs with limited permissions. Engineers cannot configure new environment variables, service accounts, or OIDC trust. Access requests take weeks.

**Impact:** Blocks artifact signing (requires OIDC or service account), secrets scanning in pipeline (requires configuration), SAST tool configuration.

**Workaround:**

1. **Run scans outside the pipeline initially.** Semgrep, Trivy, and Gitleaks can all be run locally by developers as pre-commit hooks via `pre-commit` framework. This shifts left beyond the pipeline until pipeline configuration access is available.
2. **Use GitHub Actions or GitLab CI as an alternative for new projects.** If the primary platform is locked down, adopt a cloud-hosted CI/CD platform for new projects while the access request process proceeds for existing systems.
3. **Use OpenID Connect (OIDC) for keyless artifact signing** to minimize the secrets-access footprint required in CI. OIDC-based Sigstore/Cosign signing requires only that the pipeline can request an OIDC token from the CI provider — no stored secrets needed. Present this to the platform team as a lower-risk alternative to stored credentials.
4. **Escalate using the security budget framework.** Use `devsecops-methodology/docs/security-tool-budget-framework.md` ROI calculations to quantify the cost of delayed pipeline security controls — this is often the most effective way to accelerate access request approvals.

**Milestone to unlock full adoption:** CI/CD platform service account configured with OIDC trust; secrets access approved for security scanning environment variables.

---

### Blocker 6: Resistance from development teams to pipeline security gates

**Symptom:** Engineering teams report that security scanning gates slow down deployments and block releases on findings they believe are non-applicable.

**Impact:** Blocks sustained adoption of `secure-pipeline-templates` and `secure-ci-cd-reference-architecture` controls.

**Workaround:**

1. **Audit-only mode first.** Configure all new security gates in non-blocking mode (`--exit-code 0` or equivalent) for the first 30 days. Gather data on finding volume, false positive rates, and scan times. Use this data to tune thresholds before enabling blocking.
2. **Gate only on Critical severity initially.** A gate that blocks on every Medium finding will be bypassed or removed. Start with Critical-only gates; expand to High once the team has tuned the false positive backlog.
3. **Implement VEX from day one.** The fastest way to reduce developer friction from SCA gates is to implement VEX suppression (see `software-supply-chain-security-framework/docs/vex-and-sbom-lifecycle.md`). VEX removes non-applicable findings before they reach the gate.
4. **Use `devsecops-methodology/docs/resistance-management.md`** for structured approaches to the seven most common forms of engineering team resistance to security controls.
5. **Instrument scan times.** If a scan adds more than 5 minutes to a PR feedback loop, it will be resented. Use caching, incremental scanning, and parallel execution to keep total security scan overhead under 3 minutes for most PRs.

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

## Role-Based First-Day and First-Week Checklists

The sequencing guides above address organizational strategy. These checklists address individual practitioners: what to do on day one when you have been assigned ownership of a specific framework adoption.

---

### For a CISO or VP of Engineering — First Week

**Day 1–2: Establish baseline**
- [ ] Run the `devsecops-maturity-model` 45-question assessment with your leadership team. Target completion in 90 minutes. This produces a radar chart showing current state across eight domains.
- [ ] Identify the two lowest-scoring domains — these become your first 90-day focus areas.
- [ ] Read `devsecops-methodology/docs/anti-patterns.md` — identify which of the 14 anti-patterns your organization currently exhibits.

**Day 3–4: Scope the investment**
- [ ] Use `devsecops-methodology/docs/security-tool-budget-framework.md` to inventory current tool spending. Identify the overlap analysis matrix results for your portfolio.
- [ ] Identify the compliance frameworks you are required to meet (SOC 2, ISO 27001, PCI-DSS, FedRAMP). Read `compliance-automation-framework/docs/regulatory-controls-matrix.md` to understand your current gap.
- [ ] Draft a 3-paragraph executive brief: current state maturity (maturity model output), the two highest-risk gaps, and the investment required to close them using the ROI model from the budget framework.

**Day 5: Align the team**
- [ ] Select a pilot team for the first framework rollout (recommend 2–5 engineers; pick a team that is already motivated, not the most resistant one).
- [ ] Schedule a kick-off with the pilot team using `devsecops-methodology/docs/implementation.md` Phase 0 checklist as the agenda.
- [ ] Set your 90-day success metric: what single measurable improvement will demonstrate the program is working? (Suggested: percentage of pipelines with SAST+SCA active and blocking on Critical.)

---

### For a Platform Engineering Team Lead — First Week

**Day 1: Choose your pipeline starting point**
- [ ] Read `secure-pipeline-templates/docs/introduction.md` (15 minutes).
- [ ] Identify which CI/CD platform your organization uses (GitHub Actions, GitLab CI, Jenkins, Azure Pipelines) and locate the corresponding template in `secure-pipeline-templates/templates/`.
- [ ] Fork or copy the template to a test repository. Do not modify it yet — run it as-is against a low-risk application.

**Day 2: First scan run**
- [ ] Run the template against a real repository. Collect the first scan output.
- [ ] Categorize all findings as: Critical-exploitable, Critical-not-applicable (VEX candidate), High, Medium/Low.
- [ ] Note how long the full security stage takes to run. If > 5 minutes, flag as a tuning requirement.

**Day 3–4: Configure for your environment**
- [ ] Set `REGISTRY` and `IMAGE_NAME` variables for your artifact registry.
- [ ] Configure OIDC trust for keyless artifact signing (see `secure-pipeline-templates/docs/implementation.md`).
- [ ] Configure the Dependency-Track upload step for your Dependency-Track instance (or deploy Dependency-Track if not yet available).
- [ ] Apply the `secure-ci-cd-reference-architecture/docs/hardening-checklist.md` and tick off at least the Critical and High items for your pipeline configuration.

**Day 5: Roll out to first team**
- [ ] Present the template and scan results to the first pilot team.
- [ ] Create a Jira epic for Critical findings; assign initial VEX assessment tasks.
- [ ] Set a 30-day review date to assess false positive rates and determine which gates to enable in blocking mode.

---

### For an Application Security Engineer — First Week

**Day 1: Map your control inventory**
- [ ] Read `devsecops-framework/docs/framework.md` — specifically the controls catalog. Identify which controls your organization currently has implemented (partial credit counts).
- [ ] Read `secure-ci-cd-reference-architecture/docs/threat-model.md` — focus on the threat scenarios most relevant to your architecture (cloud-native, on-premises, AI-augmented).

**Day 2: Assess the vulnerability management posture**
- [ ] Query your current vulnerability scanner output (Trivy, Snyk, Dependabot). Count open Critical and High findings across all production repositories.
- [ ] Identify whether VEX is in use. If not, read `software-supply-chain-security-framework/docs/vex-and-sbom-lifecycle.md` and estimate how many current Critical findings could be VEX-suppressed as not-applicable. This is your "noise reduction opportunity."

**Day 3–4: Pick one control gap to close this week**
- [ ] From your Day 1 control inventory, select the highest-priority un-implemented control.
- [ ] If secret scanning is not active: configure Gitleaks or GitHub Advanced Security secret scanning for all repositories by end of week. This is the fastest control to deploy with immediate impact.
- [ ] If SBOMs are not being generated: configure Syft in CI for at least one production service. Attach the SBOM to the image digest via Cosign.

**Day 5: Establish your review cadence**
- [ ] Schedule a weekly 30-minute vulnerability triage meeting with at least one developer from each product team.
- [ ] Set up Dependency-Track Slack/Teams notifications for new Critical findings so they surface within 24 hours of disclosure.
- [ ] Read `devsecops-maturity-model/docs/assessment-scorecard.md` — prepare to run a formal assessment with the team within 30 days.

---

### For a Development Lead — First Week

**Day 1: Deploy pre-commit hooks locally**
- [ ] Install the `pre-commit` framework in your local environment.
- [ ] Configure the following hooks for your primary repository:
  ```yaml
  # .pre-commit-config.yaml
  repos:
    - repo: https://github.com/gitleaks/gitleaks
      rev: v8.18.0
      hooks:
        - id: gitleaks
    - repo: https://github.com/pre-commit/pre-commit-hooks
      rev: v4.5.0
      hooks:
        - id: detect-private-key
        - id: check-merge-conflict
  ```
- [ ] Share this configuration with your team via the team's engineering wiki.

**Day 2: Review your dependency posture**
- [ ] Run `trivy fs . --severity CRITICAL,HIGH` against your primary service repository.
- [ ] Identify the top 5 Critical/High findings. For each: is it exploitable in your context? If not, it is a VEX candidate.
- [ ] Read `software-supply-chain-security-framework/docs/vex-and-sbom-lifecycle.md` Part 1 (VEX Workflow) — approximately 20 minutes.

**Day 3: Adopt the secure pipeline template**
- [ ] Locate the appropriate template for your CI/CD platform in `secure-pipeline-templates/templates/`.
- [ ] Identify which stages you can enable immediately (secrets scanning, SCA) vs. which require coordination with the platform team (artifact signing, DAST).
- [ ] Enable secrets scanning and SCA stages in audit mode (non-blocking) for your team's repositories.

**Day 4–5: First sprint of security hygiene**
- [ ] Resolve at least 2 Critical dependency vulnerabilities this week. These are the highest-ROI security investment a development team can make.
- [ ] Pin all direct dependencies to explicit version ranges (no `*` or `latest` in manifests).
- [ ] Ensure no secrets are committed in your repository history. If `gitleaks detect --source .` reports findings, triage and rotate the exposed credentials before end of week.

---

## Related Documents

- [Ecosystem Architecture](architecture.md) — Framework dependency map and platform reference architecture
- [Integration Scenarios](integration-scenarios.md) — End-to-end walkthroughs for six organizational contexts
- [techstream-docs README](../README.md) — Quick start and framework index
