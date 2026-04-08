# Cross-Framework Incident Response Coordination

A significant security incident rarely implicates a single domain. A supply chain compromise affects pipelines, deployed infrastructure, and compliance posture simultaneously. A CI/CD breach propagates through release processes, cloud environments, and regulatory obligations. Effective incident response in a mature DevSecOps organization requires coordination across the Techstream framework capabilities simultaneously — not a siloed response from a single team.

This playbook defines the coordination model, communication protocols, and framework-specific actions required during multi-domain security incidents.

---

## Incident Taxonomy

The following incident types are likely to involve multiple Techstream framework domains:

| Incident Type | Primary Frameworks Involved | Secondary Frameworks |
|--------------|---------------------------|---------------------|
| **Supply chain compromise** (malicious dependency, compromised build tool) | Software Supply Chain Security, Secure CI/CD | Cloud Security, Release Orchestration, Compliance |
| **CI/CD pipeline breach** (compromised runner, leaked pipeline secrets, poisoned pipeline attack) | Secure CI/CD, DevSecOps Framework | Release Orchestration, Supply Chain Security, Cloud Security |
| **Cloud infrastructure breach** (lateral movement from compromised IAM, cloud credential exfiltration) | Cloud Security DevSecOps | Secure CI/CD, Release Orchestration, Compliance |
| **Production deployment of vulnerable artifact** (CVE discovered post-deployment) | Release Orchestration, DevSecOps Framework | Supply Chain Security, Cloud Security |
| **Compliance evidence integrity breach** (tampering with audit evidence, unauthorized access to evidence store) | Compliance Automation | Cloud Security, Secure CI/CD |
| **Secrets exposure** (credentials committed to SCM, exfiltrated from pipeline) | DevSecOps Framework (Secret Lifecycle), Secure CI/CD | All frameworks |
| **AI agent unauthorized action** (agent executes out-of-scope tools, prompt injection, agent escalates permissions) | AI DevSecOps Framework | Forensics and IR Framework, Secure CI/CD |
| **Multi-domain compromise requiring forensic investigation** (any incident spanning pipeline + cloud + artifacts) | Forensics and IR Framework | All affected domain frameworks |

---

## Coordination Roles

### Incident Commander (IC)

The IC owns the overall response. During a multi-framework incident, the IC coordinates domain leads, makes escalation decisions, and manages external communications. The IC is typically the CISO, VP Security, or designated senior security engineer.

**IC responsibilities during multi-framework incidents:**
- Open a war-room channel and ensure all domain leads are present
- Assign domains to technical leads within the first 15 minutes
- Gate major actions (pipeline shutdowns, production rollbacks, external notifications) — no domain should take drastic action without IC acknowledgment during the first hour
- Manage external communications (customer notification, regulatory reporting, legal)

### Domain Leads

| Domain | Lead Role |
|--------|----------|
| Pipeline/CI-CD security | Build/Platform Security Engineer or Platform Engineering Lead |
| Supply chain / SBOM | AppSec Engineer with supply chain specialization |
| Cloud infrastructure | Cloud Security Engineer |
| Release and deployment | Release Engineering Lead |
| Compliance / evidence | Compliance Engineer or GRC Lead |
| Detection and forensics | SOC Analyst (Tier 2+) or Incident Responder |
| AI/agent systems | AI Security Engineer or Platform Engineer with AI pipeline ownership |

### Supporting Functions

| Function | Responsibility |
|----------|---------------|
| Legal / Privacy | Assess regulatory notification obligations |
| Communications | Prepare customer-facing and internal communications |
| Executive Sponsor | Authorize extraordinary actions (full pipeline shutdown, production rollback at scale) |

---

## Response Phases

### Phase 1: Alert and Triage (0–15 minutes)

**Objective:** Confirm the incident is real, establish initial scope, and activate the response team.

1. Alert received from detection system (SIEM, GuardDuty, Dependency-Track alert, manual report)
2. Initial responder performs a 10-minute triage:
   - Is this a real incident or a false positive?
   - What is the likely blast radius? (single service, pipeline, cloud account, or broader?)
   - Which frameworks are likely involved?
3. If confirmed: page the IC and notify domain leads per the contacts runbook
4. Open the incident war-room channel (e.g., `#incident-YYYY-MM-DD-<short-description>`)
5. IC declares severity level (P1/P2/P3) based on triage findings

**Severity classification:**

| Severity | Criteria | Response Time |
|----------|---------|--------------|
| P1 — Critical | Active exploitation; data exfiltration likely; production availability impacted; regulatory breach probable | Immediate; full team |
| P2 — High | Significant blast radius; no confirmed data loss; degraded security posture requiring immediate remediation | Within 30 minutes; relevant domain leads |
| P3 — Medium | Isolated finding; no active exploitation; can be remediated within normal sprint | Within 4 hours; assigned engineer |

### Phase 2: Containment (15 minutes – 2 hours)

**Objective:** Limit further damage while preserving forensic evidence.

Containment actions are domain-specific but must be coordinated:

**CI/CD Pipeline (Secure CI/CD Framework):**

```bash
# Emergency: suspend all pipeline executions across the organization
# GitHub Actions — disable all workflows via API
gh api \
  -X PUT /repos/ORG/REPO/actions/permissions \
  -f enabled=false

# GitLab — pause all project runners
curl -X POST \
  --header "PRIVATE-TOKEN: $GITLAB_TOKEN" \
  "https://gitlab.com/api/v4/runners/RUNNER_ID/pause"

# Immediately rotate all pipeline-accessible credentials
# (see Secret Lifecycle Management framework for rotation procedures)
```

**Cloud Infrastructure (Cloud Security DevSecOps):**

```bash
# Isolate the suspected compromised IAM role (AWS)
# Do NOT delete — preserve for forensics
aws iam put-role-policy \
  --role-name suspected-compromised-role \
  --policy-name EmergencyDenyAll \
  --policy-document '{
    "Version":"2012-10-17",
    "Statement":[{"Effect":"Deny","Action":"*","Resource":"*"}]
  }'

# Preserve CloudTrail logs before any remediation
aws cloudtrail get-trail-status --name main-trail
# Ensure S3 Object Lock is active on the CloudTrail bucket — do not delete any logs
```

**Release and Deployment (Release Orchestration Framework):**

```bash
# Freeze all releases to production (GitOps — disable ArgoCD auto-sync)
argocd app set YOUR-APP --sync-policy none

# For Flux: suspend reconciliation across all namespaces
kubectl -n flux-system patch kustomization flux-system \
  --type merge \
  -p '{"spec":{"suspend":true}}'
```

**Supply Chain (Software Supply Chain Security Framework):**

```bash
# If a compromised dependency is confirmed:
# 1. Identify all projects using the affected package
curl -s \
  -H "X-Api-Key: ${DT_API_KEY}" \
  "${DT_URL}/api/v1/component/identity?purl=pkg:npm/affected-package%401.2.3" \
  | jq '.[] | .project.name'

# 2. Block the package in the private registry mirror
# (Artifactory: add to blocklist immediately)

# 3. Alert all teams using the package
```

**AI/Agent Systems (AI DevSecOps Framework + Forensics and IR Framework):**

```bash
# CRITICAL: Capture agent context window and logs BEFORE any remediation
# This evidence is volatile — once the pod is terminated it is lost
kubectl logs -n ai-pipeline \
  --selector app=ai-agent \
  --previous --timestamps \
  > /evidence/agent-session-$(date +%s).log

# Capture running pod state (environment, mounts, resource limits)
kubectl get pod -n ai-pipeline -o json \
  --selector app=ai-agent \
  > /evidence/agent-pod-state-$(date +%s).json

# Revoke the agent's tool authorization policy immediately
# If using YAML-defined tool scope, apply a deny-all override:
kubectl apply -f - <<EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: agent-tool-policy
  namespace: ai-pipeline
data:
  policy.yaml: |
    tools: []
    allow_filesystem: false
    allow_network: false
    allow_shell: false
    reason: emergency-lockdown-$(date +%Y%m%d)
EOF

# If the agent operates via GitHub Actions — disable the workflow
gh api \
  -X PUT /repos/ORG/REPO/actions/workflows/WORKFLOW_ID/disable

# Rotate ALL credentials the agent had access to
# (Do NOT delete the original credential metadata — preserve for forensic comparison)
# Reference: docs/secret-lifecycle-management.md for rotation procedures

# Terminate active agent sessions AFTER evidence capture
kubectl delete pod -n ai-pipeline \
  --selector app=ai-agent \
  --grace-period=60
# Allow 60 seconds for graceful shutdown; reduces log truncation risk
```

**Preserve the following before any pod deletion:**
- Agent audit trail records from the append-only log store (do NOT rotate the audit S3 bucket)
- System prompt version in use at the time of the incident (model registry or ConfigMap)
- Tool authorization policy version active at the time of the incident
- Input/output log entries from the behavioral monitoring system

**Compliance (Compliance Automation Framework):**

- Alert compliance team that evidence collection may have been affected
- Verify evidence store integrity (check S3 Object Lock status and SHA-256 hashes)
- Note: if evidence store was compromised, regulatory notification timelines may be affected

### Phase 3: Investigation (2 hours – 24 hours)

**Objective:** Determine root cause, full blast radius, and attacker actions.

**Pipeline forensics checklist:**

```bash
# Retrieve pipeline run logs for the affected window
# GitHub Actions
gh run list --limit 50 --json databaseId,status,createdAt,event | \
  jq '.[] | select(.createdAt > "INCIDENT_START_TIME")'

# Review secrets access events
# AWS: which secrets were accessed by pipeline IAM roles in the incident window
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=GetSecretValue \
  --start-time INCIDENT_START_TIME \
  --end-time INCIDENT_END_TIME \
  | jq '.Events[] | {time: .EventTime, user: .Username, secret: .CloudTrailEvent | fromjson | .requestParameters.secretId}'
```

**Supply chain investigation:**

```bash
# Verify image signatures for all production-deployed images
kubectl get pods --all-namespaces -o json | \
  jq -r '.items[].spec.containers[].image' | sort -u | \
  while read img; do
    echo "Verifying: $img"
    cosign verify \
      --certificate-oidc-issuer https://token.actions.githubusercontent.com \
      --certificate-identity-regexp "https://github.com/YOUR-ORG/" \
      "$img" 2>&1
  done

# Check SLSA provenance attestations for recently built artifacts
cosign download attestation --type slsaprovenance \
  registry.internal/your-app:${SUSPICIOUS_TAG} | \
  jq -r '.payload | @base64d | fromjson'
```

**AI/Agent Systems Investigation (Forensics and IR Framework — Five Forensic Questions):**

Apply the Five Forensic Questions framework to bound the agent investigation:

```bash
# Q1 — What did the agent do? (Reconstruct tool call sequence)
# Retrieve audit trail records for the agent session
aws s3 cp \
  s3://immutable-audit-store/agent-audit/$(date +%Y/%m/%d)/ \
  /investigation/agent-audit/ \
  --recursive \
  --sse aws:kms

# Parse tool calls from audit records
jq -s '
  sort_by(.timestamp) |
  .[] | select(.agent_session_id == "SESSION_ID") |
  {ts: .timestamp, tool: .tool_name, params: .tool_parameters, status: .authorization_status}
' /investigation/agent-audit/*.jsonl

# Q2 — What was the agent instructed to do? (Recover system prompt and user instruction)
# Retrieve the system prompt version from the model registry
kubectl get configmap agent-system-prompt \
  -n ai-pipeline \
  -o jsonpath='{.data.system_prompt}' \
  > /investigation/system-prompt-at-incident.txt

# Q3 — What data did the agent access or transmit?
# Cross-reference tool call parameters against data classification inventory
# Look for filesystem reads, network calls, or database queries in audit records
jq -s '
  .[] | select(.agent_session_id == "SESSION_ID") |
  select(.tool_name | test("read_file|http_request|query_database|list_directory")) |
  {ts: .timestamp, tool: .tool_name, target: .tool_parameters}
' /investigation/agent-audit/*.jsonl

# Q4 — What tools did it invoke and with what parameters?
# Full tool call inventory with parameters (not just names)
jq -s '[.[] | select(.agent_session_id == "SESSION_ID")] | group_by(.tool_name) |
  map({tool: .[0].tool_name, invocation_count: length, sample_params: .[0].tool_parameters})
' /investigation/agent-audit/*.jsonl

# Q5 — What was the authorization basis?
# Identify UNAUTHORIZED or UNDETERMINED tool calls
jq -s '.[] | select(.agent_session_id == "SESSION_ID") |
  select(.authorization_status != "AUTHORIZED") |
  {ts: .timestamp, tool: .tool_name, status: .authorization_status, reason: .authorization_reason}
' /investigation/agent-audit/*.jsonl

# Check for prompt injection indicators in the input log
grep -iE "(ignore previous|system:.*override|assistant:.*overrule|<\|im_start\|>|<\|system\|>)" \
  /evidence/agent-session-*.log \
  | tee /investigation/injection-indicators.txt
```

**Evidence gap analysis:** If the audit trail cannot answer all Five Questions, document the gap explicitly. Missing Q2 (system prompt version) or Q5 (authorization basis) constitutes incomplete forensic evidence and must be flagged in the post-incident report. Cross-reference with `forensics-and-incident-response-framework/docs/agent-forensics/five-questions-framework.md` for evidence gap remediation guidance.

**Cloud infrastructure investigation:**

```bash
# Identify all API calls made by the compromised principal
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=Username,AttributeValue=SUSPECTED_PRINCIPAL \
  --start-time INCIDENT_START_TIME \
  | jq '.Events[] | {time: .EventTime, service: .EventSource, action: .EventName, ip: (.CloudTrailEvent | fromjson | .sourceIPAddress)}'

# Check for data exfiltration (S3 GetObject from unexpected principals)
aws cloudwatch logs filter-log-events \
  --log-group-name /aws/s3/access-logs \
  --filter-pattern '{ $.requester = "SUSPECTED_PRINCIPAL" && $.operation = "REST.GET.OBJECT" }'
```

### Phase 4: Eradication and Recovery (Hours to Days)

**Objective:** Remove attacker access, restore integrity, and validate before returning to normal operations.

**Eradication checklist (all domains):**

- [ ] All compromised credentials rotated (pipeline secrets, cloud IAM keys, signing keys)
- [ ] Compromised pipeline configurations reverted to known-good state
- [ ] Compromised build artifacts removed from all registries and deployment systems
- [ ] Clean builds verified from known-good source commits
- [ ] New builds signed with refreshed signing credentials
- [ ] Vulnerability or misconfiguration that enabled the incident remediated
- [ ] Cloud resources created by attacker (IAM roles, EC2 instances, S3 buckets) removed

**Recovery validation — supply chain:**

```bash
# Rebuild all production artifacts from clean base images
# after rotating all credentials and patching root cause

# Verify rebuilt images against fresh signatures
cosign verify \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  --certificate-identity-regexp "https://github.com/YOUR-ORG/" \
  registry.internal/your-app:${POST_INCIDENT_TAG}

# Re-deploy with fresh artifacts through standard promotion pipeline
# (Do not bypass security gates even during incident recovery)
```

**Recovery validation — release gates:**

```bash
# Re-enable ArgoCD sync only after all above checks pass
argocd app set YOUR-APP --sync-policy automated

# Verify deployment of post-incident artifacts with clean provenance
argocd app get YOUR-APP --output json | \
  jq '.status.sync | {status, revision}'
```

**Compliance evidence preservation:**

```bash
# Verify evidence store integrity post-incident
python3 compliance_tools/verify_evidence_integrity.py \
  --bucket compliance-evidence-prod \
  --prefix access-control/ \
  --start-date INCIDENT_START_DATE \
  --end-date INCIDENT_END_DATE

# Generate incident evidence package for regulatory reporting
python3 compliance_tools/audit_packager.py \
  --framework soc2 \
  --period-start INCIDENT_START_DATE \
  --period-end $(date +%Y-%m-%d) \
  --audit-id INCIDENT_ID \
  --include-incident-records true
```

### Phase 5: Post-Incident Review

**Objective:** Learn from the incident to prevent recurrence. Generate documentation required for compliance and organizational learning.

**Post-incident report required sections:**

1. **Timeline** — chronological record from first indicator to containment
2. **Root cause analysis** — the specific vulnerability, misconfiguration, or process failure that enabled the incident
3. **Detection gap** — how long did the attacker operate before detection? Why?
4. **Blast radius** — what data, systems, and credentials were affected?
5. **Containment and recovery** — what actions were taken? What worked? What didn't?
6. **Framework control effectiveness** — which Techstream controls helped, which were absent or ineffective?
7. **Action items** — specific, assigned, time-bound improvements (at least one per gap identified)
8. **Compliance impact** — regulatory notification obligations, audit implications

**Framework-specific lessons learned:**

| Framework | Review Question |
|-----------|---------------|
| Secure CI/CD | Were pipeline security gates effective? Was the incident detectable from pipeline logs? |
| Supply Chain Security | Was SBOM complete enough to identify blast radius quickly? Did signing verification catch tampered artifacts? |
| Release Orchestration | Did release gates prevent propagation of compromised artifacts? How quickly could releases be frozen? |
| Cloud Security | Was the initial compromise detectable in cloud logs within acceptable MTTD? |
| Compliance Automation | Was evidence collection unaffected? Can an audit package be generated that covers the incident period? |
| DevSecOps Maturity | Which maturity gaps enabled this incident? How does this change the improvement roadmap? |
| AI DevSecOps Framework | Were all AI agent tool calls within the defined authorization policy? Was the agent's system prompt version at the time of the incident recorded? Did behavioral monitoring alerts fire before human detection? Was there evidence of prompt injection in the agent's input log? |
| Forensics and IR Framework | Could all Five Forensic Questions be answered from the audit trail? If not, which questions had evidence gaps, and what instrumentation was missing? Was the agent behavioral baseline current at the time of the incident? Was session reconstruction possible from the audit trail, or were records incomplete? |

---

## Communication Templates

### Internal Incident Notification

```
SECURITY INCIDENT DECLARED — [SEVERITY P1/P2/P3]

Incident ID: [INC-YYYYMMDD-001]
Declared: [TIMESTAMP UTC]
IC: [Name]

WHAT WE KNOW:
[1-2 sentences describing the incident type and initial scope]

WHAT WE DON'T KNOW YET:
[Unknowns — blast radius, root cause, data exposure status]

IMMEDIATE ACTIONS TAKEN:
- [Action 1]
- [Action 2]

CURRENT STATUS: [Investigating / Contained / Eradication / Recovering]

WAR ROOM: #incident-[short-name]
NEXT UPDATE: [Timestamp — every 30 min for P1, every 2 hours for P2]

IC: [Name] | [Contact]
```

### Customer-Facing Notification Template (If Data Exposure Confirmed)

Coordinate with Legal before sending. This template requires legal review for specific regulatory context (GDPR 72-hour window, CCPA, SOC 2 contractual obligations):

```
Subject: Security Incident Notification

We are writing to inform you of a security incident that may have affected [describe affected data/systems].

WHAT HAPPENED
On [date], we detected [brief description]. We immediately [immediate actions taken].

WHAT DATA WAS INVOLVED
[Specific data categories affected, or "We have no evidence that customer data was accessed" if applicable]

WHAT WE ARE DOING
[Steps being taken to remediate and prevent recurrence]

WHAT YOU CAN DO
[If applicable: recommended actions for customers]

If you have questions, please contact [security contact].
```

---

## Related Techstream Resources

| Topic | Document |
|-------|---------|
| Pipeline forensics | [Pipeline Forensics Playbook](../../secure-ci-cd-reference-architecture/docs/pipeline-forensics-playbook.md) |
| Supply chain incident response | [Supply Chain IR Playbook](../../software-supply-chain-security-framework/docs/incident-response-playbook.md) |
| Cloud incident response | [Cloud IR Playbooks](../../cloud-security-devsecops/docs/incident-response-playbooks.md) |
| Secret rotation procedures | [Secret Lifecycle Management](../../devsecops-framework/docs/secret-lifecycle-management.md) |
| Compliance evidence under incident | [Evidence Collection Automation](../../compliance-automation-framework/docs/evidence-collection-automation.md) |
| Release freeze procedures | [Release Orchestration Framework](../../release-orchestration-framework/docs/framework.md) |
| AI agent forensics — Five Questions | [Five Questions Framework](../../forensics-and-incident-response-framework/docs/agent-forensics/five-questions-framework.md) |
| AI agent behavioral baseline | [AI Behavioral Baseline](../../forensics-and-incident-response-framework/docs/ai-behavioral-baseline.md) |
| AI agent containment — tool authorization | [Agent Authorization](../../ai-devsecops-framework/docs/agent-authorization.md) |
| AI agent audit trail preservation | [Agent Audit Trail](../../ai-devsecops-framework/docs/agent-audit-trail.md) |
| Framework selection | [Framework Selection Guide](framework-selection-guide.md) |

*Part of the Techstream central documentation. Licensed under Apache 2.0.*
