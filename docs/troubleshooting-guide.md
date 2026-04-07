# Cross-Framework Troubleshooting Guide

This guide documents the most common implementation problems encountered when deploying Techstream frameworks, along with diagnosis steps and solutions. Problems are organized by the phase of implementation where they typically surface and by the specific framework involved.

Use this guide when:
- A pipeline gate is blocking deployments with findings you believe are false positives
- A tool produces no output when it should produce results
- Two tools produce conflicting severity ratings for the same CVE
- An approval workflow in the release orchestration framework isn't triggering correctly
- Compliance scans report failures for controls that appear to be configured correctly

---

## Table of Contents

1. [Secure Pipeline — Common Pipeline Gate Issues](#1-secure-pipeline--common-pipeline-gate-issues)
2. [Supply Chain Security — SBOM and Signing Issues](#2-supply-chain-security--sbom-and-signing-issues)
3. [Compliance Automation — False Positives and Control Conflicts](#3-compliance-automation--false-positives-and-control-conflicts)
4. [Cloud Security — Misconfiguration Detection Issues](#4-cloud-security--misconfiguration-detection-issues)
5. [Release Orchestration — Approval and Promotion Issues](#5-release-orchestration--approval-and-promotion-issues)
6. [Maturity Assessment — Scoring Disputes](#6-maturity-assessment--scoring-disputes)
7. [Tool Interoperability Issues](#7-tool-interoperability-issues)
8. [Cross-Framework Integration Issues](#8-cross-framework-integration-issues)
9. [AI Pipeline and Model Supply Chain Issues](#9-ai-pipeline-and-model-supply-chain-issues)

---

## 1. Secure Pipeline — Common Pipeline Gate Issues

### 1.1 SAST gate blocks deployment — findings appear to be false positives

**Symptom:** The pipeline fails at the SAST gate with Critical or High findings, but your review indicates the findings are not exploitable in your application context.

**Diagnosis steps:**
1. Read the full finding detail — not just the title. Many tools report the file path, line number, and a code snippet. Confirm the flagged code is actually executed (not dead code, not a test fixture, not a commented-out block).
2. Check if the finding is in a dependency (not your own code). SAST tools sometimes scan imported libraries.
3. Check the finding category — some categories (e.g., "potential SQL injection") have high false positive rates in certain frameworks (e.g., ORMs that prevent SQL injection by design).

**Solutions:**
- **If the finding is a confirmed false positive:** Add a suppression comment with justification. In Semgrep:
  ```python
  user_input = request.GET["q"]  # nosemgrep: python.flask.security.audit.template-injection
  # nosemgrep rationale: input is validated and HTML-escaped in the template layer
  ```
  In CodeQL, use SARIF suppressions or the `@SuppressWarnings` annotation equivalent for your language.
- **If the tool is flagging dependencies:** Configure the SAST tool to exclude `node_modules`, `vendor/`, `.venv/`, or equivalent dependency directories. SAST should scan your code; SCA tools should scan dependencies.
- **If multiple teams encounter the same false positive:** Create a shared Semgrep rule configuration that disables the specific rule across all repositories, with documented rationale.

**Reducing systemic false positives:**

```yaml
# .semgrep.yml — repository-level configuration
rules: []

# Append to reduce false positives in test files
paths:
  exclude:
    - "**/test/**"
    - "**/*_test.py"
    - "**/*spec.js"
    - "**/fixtures/**"
```

---

### 1.2 SCA gate fails — dependency CVE is not relevant to the deployed code

**Symptom:** Grype or Dependabot reports a Critical CVE in a library, but the vulnerable function in that library is never called by the application.

**Diagnosis steps:**
1. Confirm the affected library version. Tools sometimes match on package name without confirming the exact version is affected.
2. Review the CVE description — identify which specific function or feature is vulnerable.
3. Determine whether your application calls that function (grep the codebase, review import paths).
4. Check whether the library is a `devDependency` (npm), test-only dependency, or build-only tool — these may not be in the production artifact.

**Solutions:**
- **Create a VEX assertion** to formally document the non-exploitability (see [software-supply-chain-security-framework: vex-and-sbom-lifecycle.md](../../software-supply-chain-security-framework/docs/vex-and-sbom-lifecycle.md)).
- **Configure Grype allowlisting** for specific CVE/package combinations with documented justification:
  ```yaml
  # .grype.yaml
  ignore:
    - vulnerability: CVE-2021-44228
      package:
        name: log4j-core
        version: 2.14.1
        type: java-archive
      # JNDI not used — see VEX document at ./vex/payment-service.vex.json
  ```
- **For devDependencies:** Ensure your pipeline scans only the production artifact, not the full development environment.

---

### 1.3 Secrets detection produces false positives (high-entropy strings that are not secrets)

**Symptom:** TruffleHog or Gitleaks flags base64-encoded configuration, test tokens, UUIDs, or other high-entropy strings as potential secrets.

**Diagnosis steps:**
1. Identify the flagged string type (base64-encoded config? UUID? Hash? Salt value?).
2. Determine if the string has any operational value to an attacker (can it be used to access any system?).
3. Check if the pattern is produced by a specific framework or build tool.

**Solutions:**
- **For configuration values that look like secrets:** Use a `.gitleaksignore` or TruffleHog configuration to allowlist specific paths:
  ```toml
  # .gitleaks.toml
  [[allowlists]]
  description = "Test fixture tokens — not real credentials"
  paths = ["tests/fixtures/**", "testdata/**"]
  ```
- **For generated configuration:** Add the generator output to `.gitignore` and never commit it.
- **For legitimate high-entropy values** (salts, nonces): add inline suppression with justification:
  ```python
  APP_SALT = "3rKJ8NmQ..."  # gitleaks:allow — non-secret: PBKDF2 salt, not a credential
  ```

---

### 1.4 Container image scan reports vulnerabilities in base OS packages that cannot be patched

**Symptom:** Trivy or Grype reports CVEs in Alpine, Ubuntu, or Debian base OS packages, but `apt-get upgrade` or the equivalent does not resolve them because no patched version is available from the distribution.

**Diagnosis steps:**
1. Confirm the package version and CVE — run `trivy image --format table <image>` and review the "Fixed Version" column.
2. If "Fixed Version" is empty or "N/A", the vulnerability has been reported but no patched package is available from the distribution.
3. Check whether the CVE is actually exploitable in a container context (some kernel CVEs are not exploitable inside a container).

**Solutions:**
- **If Fixed Version is available in a newer distro release:** Update the base image to a newer minor version.
- **If Fixed Version is not available:** Assess exploitability. For containerized workloads, many kernel-level CVEs are not exploitable. Document this with a VEX assertion.
- **Consider distroless or minimal base images:** `gcr.io/distroless/base` or `cgr.dev/chainguard/static` have dramatically fewer OS packages and correspondingly fewer CVEs.
- **Configure Trivy to skip unfixed vulnerabilities** (with explicit acknowledgment that this is a reporting choice, not a security acceptance):
  ```yaml
  # In pipeline:
  trivy image --ignore-unfixed --exit-code 1 --severity CRITICAL <image>
  ```

---

### 1.5 OIDC federation token failures in GitHub Actions

**Symptom:** The pipeline fails with an error like `Error: Credentials could not be loaded` or `Error: The audience is invalid` when attempting to assume an AWS IAM role or GCP service account via OIDC.

**Diagnosis steps:**
1. Confirm `permissions: id-token: write` is set in the job (not just at the workflow level):
   ```yaml
   jobs:
     deploy:
       permissions:
         id-token: write
         contents: read
   ```
2. Check the OIDC subject claim produced by GitHub matches the trust policy in the IAM role:
   ```bash
   # Print the OIDC token claims (base64-decode the middle section)
   echo $ACTIONS_ID_TOKEN_REQUEST_TOKEN | cut -d. -f2 | base64 -d 2>/dev/null | python3 -m json.tool
   ```
3. Verify the IAM trust policy condition matches the actual subject claim format:
   ```
   Expected: repo:<org>/<repo>:ref:refs/heads/main
   Actual:   repo:<org>/<repo>:environment:production
   ```
   The subject changes when an environment is configured — if your IAM role's trust policy conditions `repo:org/repo:ref:refs/heads/main` but the job runs in the `production` environment, the subject becomes `repo:org/repo:environment:production`.
4. Check the AWS region — `aws-actions/configure-aws-credentials` v2+ requires an explicit `aws-region` input.

**Common causes and solutions:**

| Error | Root Cause | Fix |
|-------|-----------|-----|
| `Error: Credentials could not be loaded` | `id-token: write` permission not set on the job | Add `permissions: id-token: write` to the job block |
| `Error: Could not assume role` | Trust policy subject condition does not match actual subject claim | Update IAM trust policy condition; print the token claims to find the actual subject |
| `Error: The audience is invalid` | `audience` parameter mismatch between the action and the role trust policy | Set `audience: sts.amazonaws.com` (AWS) or match the trust policy's `aud` condition |
| `Error: Not authorized to perform sts:AssumeRoleWithWebIdentity` | Trust policy condition is too restrictive (e.g., branch condition blocks a tag-triggered run) | Add condition for `ref:refs/tags/*` if deploying from tags, or use a wildcard with careful scope |
| Token works in staging but not production | Different IAM roles per environment, trust policy for production is configured differently | Compare trust policies between staging and production IAM roles |

**Best practice:** Pin each job to its own dedicated IAM role with a branch or environment condition, never a wildcard subject `*`. See: [OIDC Federation Guide](../../secure-ci-cd-reference-architecture/docs/oidc-federation-guide.md).

---

## 2. Supply Chain Security — SBOM and Signing Issues

### 2.1 Cosign verification fails at the admission controller

**Symptom:** Kyverno or Sigstore Policy Controller rejects an image during deployment with a signature verification error, even though the image was signed during the build.

**Diagnosis steps:**
```bash
# Manually verify the signature to diagnose the error
cosign verify \
  --certificate-identity-regexp "^https://github.com/<org>/<repo>/" \
  --certificate-oidc-issuer "https://token.actions.githubusercontent.com" \
  <REGISTRY>/<IMAGE>@<DIGEST> 2>&1

# Common error messages and their causes:
# "no matching signatures" — image was signed but with different OIDC claims
# "certificate has expired" — Fulcio certificate expired (expected; verify via transparency log)
# "digest does not match" — mutable tag was re-used; policy is resolving tag to different digest
```

**Common causes and solutions:**

| Error | Root Cause | Fix |
|-------|-----------|-----|
| `no matching signatures` | Image in policy uses mutable tag; tag now points to a different image digest | Use digest references in Kubernetes manifests, not tags |
| `certificate has expired` | Cosign certificate from Fulcio is short-lived (10 min) | This is expected; verify using `cosign verify --rekor-url` to validate against transparency log |
| `certificate identity does not match` | OIDC claims in the signing certificate differ from the policy's regexp | Update the Kyverno policy's `subject` regexp to match the actual signing identity |
| `registry authentication error` | Admission controller cannot pull the signature from the registry | Configure registry credentials for the admission controller service account |

---

### 2.2 SBOM generation produces sparse or empty component list

**Symptom:** Syft or Trivy generates an SBOM but it contains very few or zero components, even though the application has many dependencies.

**Diagnosis steps:**
1. Confirm the scan target type (directory vs. container image vs. OCI archive). Different targets require different scan modes.
2. Check whether package lock files are present (`package-lock.json`, `Pipfile.lock`, `go.sum`, `Cargo.lock`). SBOM tools heavily depend on lock files.
3. Check whether the scan is running against the build directory vs. the final artifact.

**Solutions:**
- **Missing lock files:** Ensure lock files are committed to the repository and present in the build context:
  ```bash
  # Verify lock files are present before SBOM generation
  ls package-lock.json yarn.lock Pipfile.lock go.sum Cargo.lock 2>/dev/null
  ```
- **Scanning compiled artifacts without source:** For compiled languages (Go, Java, .NET), scan the compiled binary or package — not the source directory:
  ```bash
  # Scan the compiled Go binary
  syft scan ./bin/myapp -o cyclonedx-json=sbom.cdx.json
  # Scan the JAR/WAR artifact
  syft scan ./target/myapp.jar -o cyclonedx-json=sbom.cdx.json
  ```
- **For container images:** Scan the image digest, not a build directory:
  ```bash
  syft scan docker:myregistry.io/myapp@sha256:... -o cyclonedx-json=sbom.cdx.json
  ```
- **Multi-language projects:** Some tools (cdxgen) handle multi-language monorepos better than Syft:
  ```bash
  cdxgen -t java,python ./src -o sbom.cdx.json
  ```

---

## 3. Compliance Automation — False Positives and Control Conflicts

### 3.1 Checkov or Prowler reports a failure for a control that is implemented

**Symptom:** The IaC scanner reports a control failure (e.g., "S3 bucket logging not enabled") but the control is implemented via a separate resource or a configuration not recognized by the tool.

**Diagnosis steps:**
1. Read the exact check ID and description: `checkov -c <CHECK_ID> --explain`.
2. Verify how the tool detects the control — it may look for a specific resource attribute that your configuration sets differently.
3. Check if the tool supports the configuration method you've used (e.g., centralized logging via a separate module may not be detected by some checks).

**Example: Checkov S3 logging check**
```bash
# Diagnose: what exactly is Checkov checking?
checkov -c CKV_AWS_18 --explain
# Output: "Ensure the S3 bucket has access logging enabled"
# Checkov looks for aws_s3_bucket_logging resource associated with the bucket
# If you configure logging via a separate module, the association may not be detected

# Fix: Add an explicit aws_s3_bucket_logging resource in Terraform
resource "aws_s3_bucket_logging" "example" {
  bucket = aws_s3_bucket.example.id
  target_bucket = aws_s3_bucket.log_bucket.id
  target_prefix = "log/"
}
```

**When the control is genuinely implemented but the tool can't detect it:** Add a documented suppression referencing the actual implementation:
```python
# In Terraform file
# checkov:skip=CKV_AWS_18:IMPLEMENTS-VIA-MODULE
# S3 access logging configured via the centralized-logging module at ../modules/logging
# Evidence: CloudTrail confirms logging is active; see compliance-evidence/s3-logging-audit-2024.pdf
```

---

### 3.2 Two tools report different severity ratings for the same CVE

**Symptom:** Trivy rates a CVE as Critical but Grype rates it as Medium for the same package and version.

**Root cause:** Tools use different CVE severity sources. Trivy primarily uses NVD CVSS scores. Grype uses NVD CVSS as baseline but also considers vendor-specific severity ratings from OS distributions (Debian, Alpine, Red Hat). Distribution maintainers often modify severity ratings based on their package's specific build configuration or application context.

**Diagnosis:**
```bash
# Check which severity source Trivy is using
trivy image --format json <image> | jq '.Results[].Vulnerabilities[] | select(.VulnerabilityID == "CVE-XXXX-XXXXX") | {severity: .Severity, source: .SeveritySource}'

# Check Grype's source
grype <image> --output json | jq '.matches[] | select(.vulnerability.id == "CVE-XXXX-XXXXX") | {severity: .vulnerability.severity, source: .relatedVulnerabilities}'
```

**Resolution approach:**
- For **pipeline gates**, use the higher severity rating as the policy baseline. Do not configure gates based on a specific tool's severity — your gates should use the highest reported severity from any source.
- For **false positive suppression decisions**, prefer the distribution's severity rating when the distribution has explicitly lowered the severity with documented technical rationale (e.g., Alpine patches the vulnerable function out of their build).
- Document the discrepancy and your resolution rationale in the VEX record.

---

### 3.3 OPA policy evaluation produces unexpected results

**Symptom:** An OPA/Rego policy that should deny a resource is allowing it, or a policy that should allow is denying.

**Diagnosis steps:**
```bash
# Test the policy with the exact input
cat resource.json | opa eval \
  --data policy.rego \
  --input /dev/stdin \
  "data.compliance.aws.s3.deny"

# Enable tracing to see the evaluation path
cat resource.json | opa eval \
  --data policy.rego \
  --input /dev/stdin \
  --instrument \
  "data.compliance.aws.s3.deny"
```

**Common causes:**
- **Input document structure mismatch:** The policy expects `input.resource.aws_s3_bucket[name]` but the actual input wraps it differently. Print `input` in the policy to see the actual structure:
  ```rego
  # Debug helper — remove after diagnosis
  debug_input[input]
  ```
- **Rego version compatibility:** OPA 0.59+ introduced `import future.keywords` breaking changes. Verify `import future.keywords.in` and `import future.keywords.if` are present if using those keywords.
- **Undefined vs. empty set:** In Rego, an undefined `deny` rule evaluates to an empty set (allow), not an error. Use `trace` to verify the rule is being evaluated at all.

---

## 4. Cloud Security — Misconfiguration Detection Issues

### 4.1 GuardDuty alert does not correlate with expected threat behavior

**Symptom:** GuardDuty generates a finding (e.g., `Recon:IAMUser/UserPermissions`) for expected activity such as a developer running IAM enumeration commands for legitimate access review.

**Diagnosis:**
1. Review the GuardDuty finding's `affected resource` and `actor` fields — confirm it is the expected identity.
2. Check the frequency — a single occurrence during business hours by a known identity differs significantly from repeated occurrences at 2:00 AM from an unknown IP.
3. Review the actor's IP against your corporate IP ranges and VPN exit nodes.

**Solutions:**
- **Suppress expected false positives** using GuardDuty suppression rules:
  ```bash
  aws guardduty create-filter \
    --detector-id <DETECTOR_ID> \
    --name "suppress-iam-enumeration-from-security-team" \
    --action SUPPRESS \
    --finding-criteria '{
      "Criterion": {
        "type": {"Eq": ["Recon:IAMUser/UserPermissions"]},
        "resource.accessKeyDetails.userType": {"Eq": ["IAMUser"]},
        "service.action.awsApiCallAction.remoteIpDetails.ipAddressV4": {"Eq": ["<SECURITY_TEAM_NAT_IP>"]}
      }
    }'
  ```
- **Document all suppression rules** with justification — undocumented suppressions create audit gaps.

---

### 4.2 Terraform drift detection reports false positives after manual investigation

**Symptom:** `terraform plan` reports changes to infrastructure that appears correctly configured, creating noise in drift detection alerts.

**Common causes and solutions:**

| Cause | Symptom | Fix |
|-------|---------|-----|
| Non-deterministic computed attributes | `terraform plan` always shows a diff for attributes computed by the provider | Use `ignore_changes` lifecycle block for provider-computed attributes |
| Case sensitivity in ARNs | Plan shows change between `arn:aws:iam::123` and `ARN:AWS:IAM::123` | Normalize ARN case in Terraform variables |
| Ordering of set-type attributes | Tags map or security group list shown as changed each plan | Ensure consistent attribute ordering; consider sorting tags |
| State file out of sync | Resources modified outside Terraform | Run `terraform refresh` or `terraform state` commands to resync |

---

## 5. Release Orchestration — Approval and Promotion Issues

### 5.1 Automated promotion gate passes when it should fail

**Symptom:** A deployment is promoted to production despite a CI gate failure or a vulnerability finding that should block promotion.

**Diagnosis steps:**
1. Review the promotion record — was the gate result explicitly overridden by an authorized approver?
2. Check whether the gate evaluates the artifact digest or the tag. Tags are mutable — if a new image was pushed with the same tag after the scan, the gate result may not match the deployed artifact.
3. Review whether the gate checks for a minimum soak period in staging — did the staging deployment satisfy the soak requirement?

**Critical configuration check:**
```yaml
# In your promotion gate configuration, always pin to digest:
# WRONG — tag is mutable:
gates:
  image_scan:
    target: "myregistry.io/payment-service:v3.2.1"

# CORRECT — digest is immutable:
gates:
  image_scan:
    target: "myregistry.io/payment-service@sha256:a3f8c7d9..."
```

---

### 5.2 Approval notification not delivered to approvers

**Symptom:** A deployment awaits approval but approvers do not receive notification. The deployment times out and fails.

**Diagnosis steps:**
1. Check the notification channel configuration — is the Slack webhook or email SMTP configuration still valid?
2. Verify that the approver list is current — team changes may have left stale approver entries.
3. Check whether the notification was sent but filtered (Slack notification preferences, email spam filter).
4. Review the orchestration platform's notification delivery log.

**Solution:** Configure secondary notification channels and test all notification workflows in staging before relying on them in production. Add an escalation path that emails a distribution list if the primary approval mechanism times out.

---

## 6. Maturity Assessment — Scoring Disputes

### 6.1 Assessment score is disputed by the assessed team

**Symptom:** A team disputes their maturity assessment score, arguing their practices are more mature than documented.

**Root cause:** Maturity scoring requires evidence, not assertion. A common disagreement occurs when a team believes they are at Level 3 because they have a tool deployed, but the framework requires behavioral evidence (developers using findings, champions triaging findings) to advance past Level 2.

**Resolution approach:**
1. Return to the evidence requirements in the [Remediation Playbooks](../../devsecops-maturity-model/docs/remediation-playbooks.md) for the disputed domain.
2. Ask the team to produce the specific evidence items listed for the claimed level.
3. If evidence is present but was not considered during scoring, update the score and document the evidence.
4. If evidence is absent, maintain the current score and use the gap as the remediation target.

**Principle:** A SAST tool running in "report" mode (not enforcing) is Level 2. A SAST tool enforcing break-build on Critical/High with developer engagement and finding remediation is Level 3. Tooling deployment without adoption is Level 2.

---

## 7. Tool Interoperability Issues

### 7.1 Cosign and Kyverno version incompatibility

**Symptom:** Kyverno ImageVerify policy fails even though Cosign verification succeeds manually.

**Diagnosis:**
```bash
# Check Kyverno version
kubectl get pod -n kyverno -o jsonpath='{.items[0].spec.containers[0].image}'

# Check Cosign version compatibility with your Kyverno version
# Kyverno 1.10+ requires Cosign v2 format
cosign version
```

**Known compatibility matrix (as of April 2026):**

| Kyverno Version | Cosign Format | Notes |
|----------------|--------------|-------|
| 1.9.x | Cosign v1 | keyless signing with Cosign v1 API |
| 1.10.x+ | Cosign v2 | Breaking change — v1 signatures not verified |
| 1.12.x+ | Cosign v2 + OCI 1.1 referrers | Uses OCI referrers API; may differ from registry to registry |

**Fix:** Align Kyverno and Cosign versions. If your registry does not support OCI 1.1 referrers, set `cosign attest --attachment-tag-prefix` explicitly and configure Kyverno accordingly.

---

### 7.2 Trivy and Grype produce different SBOM component counts

**Symptom:** An SBOM generated by Trivy has 142 components; the same image scanned by Grype produces 187 components.

**Root cause:** Tools use different detection algorithms. Trivy focuses on OS packages and package manager lock files. Grype also detects binaries without a package manager (e.g., statically linked binaries, binaries installed without a package manager) using binary fingerprinting. The difference is not a bug — it reflects different detection scope.

**Resolution:** For compliance SBOM purposes, use both tools and merge the component lists, or select one canonical tool and document the scope limitation. For vulnerability scanning in pipelines, running both tools increases detection coverage — failing the pipeline if either tool reports a Critical finding is the conservative approach.

---

## 8. Cross-Framework Integration Issues

### 8.1 Compliance evidence from CI pipeline does not match auditor's format expectations

**Symptom:** Automated evidence (Checkov SARIF, Prowler JSON) is collected but auditors request evidence in a format their GRC platform cannot import.

**Solution:**
- Generate evidence in multiple formats from the same scan:
  ```bash
  # Generate both SARIF (for code review tools) and JSON (for auditor export) from same Checkov scan
  checkov -d ./infrastructure \
    --output sarif --output-file checkov.sarif \
    --output json --output-file checkov.json
  ```
- Many GRC platforms (Vanta, Drata, SecureFrame) have native connectors for common tools. Verify connector availability before building a custom export pipeline.
- For auditors requesting PDF/Excel: use the evidence management system (AWS S3 + metadata, or a dedicated compliance platform) to generate formatted audit packages.

---

### 8.2 Security maturity assessment score conflicts with compliance audit pass

**Symptom:** The DevSecOps Maturity Assessment scores a domain at Level 2, but the organization just passed a SOC 2 Type II audit.

**This is not a contradiction.** SOC 2 audits verify that specific controls were operating effectively during the audit period. The maturity model measures the depth, consistency, and improvement trajectory of security practices. An organization can pass a SOC 2 audit at Level 2 maturity — it means they have the required point-in-time controls but not the systematic, measurable, continuously improving program that Level 3+ requires.

Use both assessments together:
- SOC 2 tells you which controls were operating.
- Maturity model tells you how robust, repeatable, and measurable those controls are.
- Level 3+ maturity makes future SOC 2 audits faster and less expensive by producing continuous evidence.

---

## 9. AI Pipeline and Model Supply Chain Issues

### 9.1 SCA alert fires on a package introduced by an AI coding assistant (slopsquatting detection)

**Symptom:** The SCA gate (configured for slopsquatting detection) flags a newly introduced package as suspicious — low download count, recent registration date, or no community history. The developer insists the package is legitimate because the AI assistant suggested it and it installs without error.

**Important:** An installable package with low downloads is the expected profile of a slopsquatting attack. The package installing successfully does not confirm it is safe.

**Diagnosis steps:**
1. Check the package registry entry directly:
   - npm: `npm view <package-name>` — check `_npmUser.created`, `dist-tags`, `versions` count, weekly downloads
   - PyPI: `pip index versions <package-name>` + check PyPI page for project history, author, GitHub link
   - Go: check pkg.go.dev for module history and import path legitimacy
2. Verify whether the package name matches a known, established library (check for similar but different names — `requests` vs `requestss`, `boto3` vs `botosd3`).
3. Search for the intended functionality in the registry — did the developer mean to use a different, well-established package that the AI misnamed?
4. Check whether the package is re-exported by or depends on the hallucinated package name. Some attacks re-export a legitimate package while adding malicious code.

**Resolution:**

| Finding | Action |
|---------|--------|
| Package is legitimate but newly published (e.g., team's own internal package now on public registry) | Add package to SCA allowlist with documented justification; verify the package is what the team intended |
| Package is a different name than the intended library | Remove the package; install the correct package; review all imports in the AI-generated code |
| Package is confirmed malicious | Immediately revoke developer machine credentials; audit recent commits for the package in any repository; treat as a supply chain compromise (Playbook 2 in incident-response-playbook.md) |
| Package name matches a legitimate package but author/publisher differs | Treat as potential name-squatting; do not install; file a report with the registry; document the block |

**Systemic fix:** Add a required human review step for all new package introductions in your CODEOWNERS configuration. The `.github/CODEOWNERS` entry should require security team review for changes to `package.json`, `requirements.txt`, `go.mod`, and similar dependency manifests.

---

### 9.2 AI pipeline agent fails to invoke a tool or produces an authorization error

**Symptom:** An AI pipeline agent step fails with a permission denied error, tool not found, or the agent reports it cannot complete its task due to missing access.

**This is expected behavior when authorization boundaries are correctly enforced.** The agent is blocked because its tool access manifest restricts the invoked tool. Do not expand permissions to resolve this — diagnose why the agent is requesting a tool outside its declared scope.

**Diagnosis steps:**
1. Read the agent's tool access manifest (e.g., `agent-permissions.yaml`) and compare the failed tool call to the declared allowed tools.
2. Review the agent's audit log (required by control 11.6 in the hardening checklist) — what tool was invoked, what input was provided, what error was returned?
3. Determine whether the tool invocation was part of the intended workflow or whether the agent deviated from expected behavior:
   - **Expected tool, correct scope:** Update the tool access manifest through a standard change process (not by directly expanding permissions in the failing environment).
   - **Unexpected tool, out-of-scope:** Investigate the agent's prompt and the inputs it processed for prompt injection. Review whether a data source the agent processed contained instructions to invoke the unauthorized tool.

**Common authorization errors and their meanings:**

| Error | Meaning | Action |
|-------|---------|--------|
| `Tool 'deploy_production' not in allowed_tools` | Agent attempted production deployment — not in its tool access manifest | Expected block. Do not add. Review why the agent attempted this. |
| `IAM: AccessDenied on sts:AssumeRole` | Agent's IAM role does not have permission for the attempted action | Verify the action is within the agent's intended scope before updating IAM |
| `Tool requires human_approval: true` | Tool invocation requires human confirmation before proceeding | Ensure the approval workflow is configured; check notification delivery (see Section 5.2) |
| `Environment 'production' not accessible from this agent context` | Agent identity bound to non-production environments only | Correct behavior — production access requires a separate, human-approved agent identity |

**Prompt injection investigation:**
```bash
# Review agent input log for suspicious content patterns
grep -i "ignore previous instructions\|system prompt\|tool call\|invoke\|execute" agent-input-log.txt
# Check for base64 or unicode-encoded instructions in inputs
grep -E '[A-Za-z0-9+/]{50,}={0,2}' agent-input-log.txt | base64 -d 2>/dev/null
```

---

### 9.3 Model hash verification fails before deployment

**Symptom:** The pre-deployment check script (from Stage 12 in the pipeline or the Playbook 5 recovery procedure) reports that the model file's SHA-256 hash does not match the registered value in the model registry or the Cosign attestation.

**This is a critical security signal — do not override this check.** A hash mismatch on a model file means either the model was modified after registration, or the registered hash was computed incorrectly.

**Diagnosis steps:**
1. Identify when the hash was last verified successfully:
   ```bash
   # Retrieve the expected hash from the model registry
   aws s3api head-object --bucket model-registry --key models/classifier/v2.3/model.safetensors \
     --query 'Metadata.sha256' --output text

   # Compute the current hash of the file
   sha256sum model.safetensors
   ```
2. Check the Cosign attestation for the registered model hash:
   ```bash
   cosign verify-attestation \
     --type https://techstream.io/attestations/model-hash/v1 \
     --certificate-identity-regexp "^https://github.com/<org>/" \
     --certificate-oidc-issuer "https://token.actions.githubusercontent.com" \
     <REGISTRY>/<MODEL_IMAGE>@<DIGEST>
   ```
3. Determine whether the mismatch is in the registry record or the file:
   - If the registry record hash differs from the Cosign attestation hash: the registry record was modified outside the normal pipeline.
   - If both registry and attestation agree but differ from the current file: the file was modified after signing.
   - If the mismatch is consistent (same wrong hash every time): the hash may have been computed on a different version of the file (e.g., before/after serialization format conversion).

**Response by root cause:**

| Root Cause | Response |
|-----------|---------|
| File modified after signing | Treat as potential model tampering — initiate Playbook 5 (AI/ML Model Supply Chain Incident) in [incident-response-playbook.md](../../software-supply-chain-security-framework/docs/incident-response-playbook.md) |
| Registry record modified outside pipeline | Audit registry access logs; rotate registry credentials; re-register model through pipeline |
| Hash computed on different serialization | Re-verify hash computation procedure; standardize to hash the final `.safetensors` file after conversion |
| CI pipeline computed hash on intermediate file | Fix the pipeline hash step to reference the final output file; do not deploy until re-signed |

**Do not deploy the model until the hash mismatch is resolved and the model's provenance is confirmed.** A model with an unresolved hash mismatch must be treated as untrusted.

---

When troubleshooting issues not covered in this guide, these commands provide useful diagnostic context:

```bash
# Check what Cosign sees for an image
cosign triangulate <IMAGE>@<DIGEST>

# List all Cosign attestations on an image
cosign verify-attestation --type "" <IMAGE>@<DIGEST> 2>&1 | head -20

# Test OPA policy with verbose output
opa eval --data policy.rego --input input.json --instrument "data.my.policy.deny"

# Verify Kyverno is evaluating your policy
kubectl logs -n kyverno -l app=kyverno --since=5m | grep "<IMAGE_NAME>"

# Check Checkov with specific check ID and print the actual rule logic
checkov -c CKV_AWS_18 --explain

# Trace a Grype vulnerability match decision
grype <IMAGE> --output json 2>&1 | jq '.matches[] | select(.vulnerability.id == "CVE-XXXX-XXXXX")'
```

---

*For issues not covered here, consult the individual framework documentation or raise an issue in the relevant repository. When reporting a new issue, include: the framework version, the tool and version, the exact error message, and the reproduction steps.*

*See also:*
- *[framework-selection-guide.md](framework-selection-guide.md) — Which framework to start with*
- *[integration-scenarios.md](integration-scenarios.md) — End-to-end integration walkthroughs*
- *[secure-ci-cd-reference-architecture: pipeline-forensics-playbook.md](../../secure-ci-cd-reference-architecture/docs/pipeline-forensics-playbook.md) — Investigating pipeline incidents*
