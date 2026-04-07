# DORA Metrics Implementation Guide

DORA (DevOps Research and Assessment) metrics are the most validated, research-backed indicators of software delivery performance. The four key metrics — Deployment Frequency, Lead Time for Changes, Change Failure Rate, and Failed Deployment Recovery Time — measure both throughput and stability, and are directly correlated with organizational performance outcomes (profitability, market share, employee satisfaction).

This guide explains how to instrument each metric across the Techstream framework ecosystem, interpret results, and use metric data to drive improvement.

---

## The Four Key Metrics

### Why These Four

The DORA research program (now part of Google Cloud) analyzed data from thousands of organizations over multiple years. These four metrics emerged as the minimum set that predicts both delivery performance and organizational outcomes. They balance two dimensions that are often treated as trade-offs:

```
        HIGH STABILITY
              │
   ┌──────────┼──────────┐
   │          │          │
   │  Elite   │  Elite   │
   │  (but    │  (full   │
LOW│  slow)   │  square) │HIGH
THROUGHPUT────┼────────────THROUGHPUT
   │          │          │
   │  Low     │  High    │
   │  (lagging)│ (risky) │
   │          │          │
   └──────────┼──────────┘
              │
        LOW STABILITY
```

Elite performers achieve both high throughput AND high stability. The four metrics measure position in this space and identify which dimension to improve.

---

## Metric 1: Deployment Frequency

**Definition:** How often does the organization successfully release to production?

**Why it matters:** High deployment frequency correlates with smaller, lower-risk changes, faster feedback loops, and higher developer productivity. Low frequency indicates release risk accumulation and batch-and-ship patterns that amplify blast radius.

**DORA Benchmarks (2023):**

| Performance Level | Deployment Frequency |
|------------------|---------------------|
| Elite | On-demand (multiple deploys per day) |
| High | Between once per day and once per week |
| Medium | Between once per week and once per month |
| Low | Between once per month and once every six months |

**Instrumentation**

Deployment Frequency is measured from your continuous delivery pipeline. The definition of "deployment" must be agreed upon before instrumentation: it should mean a successful release of a production-deployable artifact to the production environment (or its equivalent — canary, blue-green active slice, or production-class environment).

*GitHub Actions — emit a deployment event:*

```yaml
# In your production deployment workflow
- name: Record deployment metric
  if: success()
  env:
    DEPLOY_TIMESTAMP: ${{ steps.deploy.outputs.timestamp }}
    SERVICE_NAME: ${{ vars.SERVICE_NAME }}
    VERSION: ${{ github.sha }}
    ENVIRONMENT: production
  run: |
    curl -X POST "${{ vars.METRICS_ENDPOINT }}/deployments" \
      -H "Authorization: Bearer ${{ secrets.METRICS_TOKEN }}" \
      -H "Content-Type: application/json" \
      -d '{
        "service": "'"$SERVICE_NAME"'",
        "version": "'"$VERSION"'",
        "environment": "'"$ENVIRONMENT"'",
        "timestamp": "'"$DEPLOY_TIMESTAMP"'",
        "pipeline_run": "${{ github.run_id }}",
        "triggered_by": "${{ github.actor }}"
      }'
```

*GitLab CI — use deployment environments:*

```yaml
deploy-production:
  stage: deploy
  environment:
    name: production
    url: https://app.example.com
  script:
    - ./deploy.sh
  after_script:
    - |
      curl -X POST "${METRICS_ENDPOINT}/deployments" \
        --header "Authorization: Bearer ${METRICS_TOKEN}" \
        --data "{\"service\": \"${CI_PROJECT_NAME}\", \"version\": \"${CI_COMMIT_SHA}\", \"environment\": \"production\", \"timestamp\": \"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"}"
```

*Release Orchestration Framework integration:*

If you are using the `release-orchestration-framework`, deployment events are captured automatically from the release pipeline audit trail. Configure the DORA metrics exporter in your release orchestration configuration:

```yaml
# release-orchestration-framework config
metrics:
  dora:
    enabled: true
    deployment_event_trigger: on_production_promotion
    sink:
      type: prometheus  # or datadog, grafana-loki, custom-webhook
      endpoint: "${PROMETHEUS_PUSHGATEWAY_URL}"
```

**Measurement query (Prometheus):**

```promql
# Deployment frequency over past 7 days per service
increase(deployments_total{environment="production"}[7d])

# Deployments per day (rolling 30-day average)
rate(deployments_total{environment="production"}[30d]) * 86400
```

---

## Metric 2: Lead Time for Changes

**Definition:** The time from a code commit being merged to the main branch to that commit being deployed to production.

**Why it matters:** Short lead times enable rapid response to bugs, security vulnerabilities, and user feedback. Long lead times indicate batch accumulation, approval bottlenecks, manual handoffs, or slow pipelines.

**DORA Benchmarks (2023):**

| Performance Level | Lead Time for Changes |
|------------------|----------------------|
| Elite | Less than one hour |
| High | Between one day and one week |
| Medium | Between one week and one month |
| Low | Between one month and six months |

**Instrumentation**

Lead time requires two timestamps: (1) the commit timestamp when the change was merged to the main branch, and (2) the deployment timestamp when that commit was deployed to production.

```python
# Lead time calculation (Python example for a metrics service)
import datetime

def calculate_lead_time(commit_sha: str, deploy_timestamp: datetime.datetime) -> float:
    """
    Returns lead time in minutes for a given commit.
    commit_sha: the git SHA of the deployed commit
    deploy_timestamp: when the deployment completed
    """
    commit_info = git_client.get_commit(commit_sha)
    commit_timestamp = commit_info.author.date  # When merged to main

    lead_time_minutes = (deploy_timestamp - commit_timestamp).total_seconds() / 60
    return lead_time_minutes

def calculate_median_lead_time(deployments: list[dict], window_days: int = 30) -> float:
    """Median lead time for deployments in the past N days."""
    lead_times = [d["lead_time_minutes"] for d in deployments
                  if d["lead_time_minutes"] > 0]
    if not lead_times:
        return 0
    lead_times.sort()
    mid = len(lead_times) // 2
    return lead_times[mid]
```

*GitHub Actions — capture commit-to-deploy lead time:*

```yaml
- name: Calculate and record lead time
  if: success()
  run: |
    # Get the timestamp of the commit being deployed
    COMMIT_TIME=$(git log -1 --format=%cI ${{ github.sha }})
    DEPLOY_TIME=$(date -u +%Y-%m-%dT%H:%M:%SZ)

    # Calculate lead time in minutes
    COMMIT_EPOCH=$(date -d "$COMMIT_TIME" +%s)
    DEPLOY_EPOCH=$(date -d "$DEPLOY_TIME" +%s)
    LEAD_TIME_MINUTES=$(( (DEPLOY_EPOCH - COMMIT_EPOCH) / 60 ))

    curl -X POST "${{ vars.METRICS_ENDPOINT }}/lead-time" \
      -H "Authorization: Bearer ${{ secrets.METRICS_TOKEN }}" \
      -H "Content-Type: application/json" \
      -d "{
        \"service\": \"${{ vars.SERVICE_NAME }}\",
        \"commit_sha\": \"${{ github.sha }}\",
        \"commit_time\": \"$COMMIT_TIME\",
        \"deploy_time\": \"$DEPLOY_TIME\",
        \"lead_time_minutes\": $LEAD_TIME_MINUTES
      }"
```

**Interpretation and common bottlenecks:**

| Lead Time Segment | Measurement Point | Common Causes of Delay |
|-------------------|------------------|------------------------|
| Code review time | PR created → PR merged | Too few reviewers, large PRs, slow CODEOWNERS |
| Pipeline duration | PR merged → artifact built and scanned | Slow tests, large container images, sequential scan stages |
| Approval wait time | Artifact ready → deployment approved | Manual CAB processes, notification delays, timezone gaps |
| Deployment duration | Approval granted → traffic shifted to new version | Slow rolling updates, large cluster bootstrapping |

---

## Metric 3: Change Failure Rate

**Definition:** The percentage of deployments to production that result in a degraded service or require remediation (rollback, hotfix, patch).

**Why it matters:** CFR measures the quality of what reaches production. High CFR indicates insufficient testing gates, poor change isolation, or inadequate pre-production environments. Low CFR with high deployment frequency is the hallmark of elite teams.

**DORA Benchmarks (2023):**

| Performance Level | Change Failure Rate |
|------------------|---------------------|
| Elite | 0–5% |
| High | 5–10% |
| Medium | 11–15% (DORA notes some medium performers see higher CFR with high DF) |
| Low | 16–30% |

**Defining a "failed deployment"**

The definition must be unambiguous before measurement begins. Include:
- Deployments that triggered a P1 or P2 incident within 24 hours of release
- Deployments followed by a rollback within 24 hours
- Deployments followed by a hotfix/patch deployment within 24 hours of release
- Deployments that caused automatic scaling failures or SLO breaches

Exclude:
- Infrastructure changes unrelated to application deployment
- Feature flags toggled post-deployment (unless they caused an incident)
- Deployments to non-production environments

```python
def calculate_change_failure_rate(
    deployments: list[dict],
    incidents: list[dict],
    window_days: int = 30
) -> float:
    """
    deployments: list of {service, deploy_id, timestamp, version}
    incidents: list of {service, triggered_at, severity, linked_deploy_id}
    """
    cutoff = datetime.datetime.utcnow() - datetime.timedelta(days=window_days)

    recent_deployments = [d for d in deployments if d["timestamp"] > cutoff]
    total = len(recent_deployments)
    if total == 0:
        return 0.0

    p1_p2_incidents = [i for i in incidents
                       if i["severity"] in ("P1", "P2")
                       and i["triggered_at"] > cutoff
                       and i.get("linked_deploy_id")]

    failed_deploy_ids = {i["linked_deploy_id"] for i in p1_p2_incidents}
    failures = len([d for d in recent_deployments if d["deploy_id"] in failed_deploy_ids])

    return (failures / total) * 100
```

**Integration with incident management:**

```yaml
# PagerDuty webhook → DORA metrics service
# When an incident is created, associate it with the most recent deployment

POST /incidents
{
  "title": "Payment service 5xx spike",
  "severity": "P1",
  "service": "payment-processor",
  "triggered_at": "2024-03-15T14:32:00Z",
  "linked_deploy_id": "deploy-a1b2c3d4",  # Auto-populated by incident tooling
  "version": "sha-abc1234"                 # From deployment record
}
```

Most incident management platforms (PagerDuty, OpsGenie, ServiceNow) can be configured to query the deployment API at incident creation time to automatically link the most recent deployment.

---

## Metric 4: Failed Deployment Recovery Time

**Definition:** The time to restore service to users after a failed deployment — from when the incident is detected to when service is restored.

This metric was originally called "Mean Time to Restore" (MTTR). The DORA 2023 report renamed it to "Failed Deployment Recovery Time" to make the scope precise: it specifically measures recovery from deployment-induced failures, not all incidents.

**DORA Benchmarks (2023):**

| Performance Level | Failed Deployment Recovery Time |
|------------------|--------------------------------|
| Elite | Less than one hour |
| High | Less than one day |
| Medium | Between one day and one week |
| Low | More than one week |

**Instrumentation**

Recovery time requires: (1) the incident start time (when degradation was detected or reported), and (2) the incident resolution time (when service was restored to normal operation).

```python
def calculate_failed_deployment_recovery_time(incidents: list[dict]) -> dict:
    """
    incidents: list of {
        incident_id, service, severity,
        detected_at, resolved_at,
        linked_deploy_id  # Only incidents linked to a deployment
    }
    Returns mean and median recovery time in minutes.
    """
    deployment_incidents = [i for i in incidents if i.get("linked_deploy_id")]
    recovery_times = []

    for incident in deployment_incidents:
        if incident.get("resolved_at") and incident.get("detected_at"):
            recovery_minutes = (
                incident["resolved_at"] - incident["detected_at"]
            ).total_seconds() / 60
            recovery_times.append(recovery_minutes)

    if not recovery_times:
        return {"mean": 0, "median": 0, "count": 0}

    return {
        "mean": sum(recovery_times) / len(recovery_times),
        "median": sorted(recovery_times)[len(recovery_times) // 2],
        "count": len(recovery_times)
    }
```

**Reducing recovery time — key levers:**

| Lever | Target | Implementation |
|-------|--------|---------------|
| Detection latency | < 5 minutes | SLO-based alerting; synthetic monitoring; deployment-triggered smoke tests |
| Rollback automation | < 10 minutes | Automated rollback trigger on error rate spike post-deployment |
| Runbook availability | Immediate | Deployment playbook linked in deployment notification |
| On-call awareness | < 5 minutes | Deployment notification sent to on-call channel with rollback runbook |

*Release Orchestration Framework: automated rollback on error rate spike:*

```yaml
# Canary deployment with automated rollback
progressive_delivery:
  strategy: canary
  stages:
    - weight: 10
      duration: 10m
      success_criteria:
        error_rate_threshold: 1%   # Max 1% error rate at canary
        latency_p99_threshold: 500ms
      on_failure: rollback_immediately
    - weight: 50
      duration: 15m
      success_criteria:
        error_rate_threshold: 0.5%
      on_failure: rollback_immediately
    - weight: 100
      success_criteria:
        error_rate_threshold: 0.5%
      on_failure: rollback_immediately
```

---

## Dashboard and Reporting

### Grafana Dashboard Configuration

```json
{
  "title": "DORA Metrics",
  "panels": [
    {
      "title": "Deployment Frequency (30-day rolling)",
      "type": "stat",
      "targets": [
        {
          "expr": "round(increase(deployments_total{environment='production'}[30d]) / 30, 0.01)",
          "legendFormat": "Deployments/day"
        }
      ],
      "thresholds": {
        "steps": [
          {"color": "red", "value": 0},
          {"color": "yellow", "value": 1},
          {"color": "green", "value": 5}
        ]
      }
    },
    {
      "title": "Lead Time for Changes (median, hours)",
      "type": "stat",
      "targets": [
        {
          "expr": "histogram_quantile(0.50, rate(lead_time_minutes_bucket{environment='production'}[30d])) / 60",
          "legendFormat": "Median lead time (hours)"
        }
      ],
      "thresholds": {
        "steps": [
          {"color": "red", "value": 0},
          {"color": "yellow", "value": 24},
          {"color": "green", "value": 0},
          {"color": "green", "value": null, "op": "lte", "val": 1}
        ]
      }
    },
    {
      "title": "Change Failure Rate (%)",
      "type": "stat",
      "targets": [
        {
          "expr": "100 * (increase(deployment_failures_total{environment='production'}[30d]) / increase(deployments_total{environment='production'}[30d]))",
          "legendFormat": "CFR %"
        }
      ],
      "thresholds": {
        "steps": [
          {"color": "green", "value": 0},
          {"color": "yellow", "value": 5},
          {"color": "red", "value": 10}
        ]
      }
    },
    {
      "title": "Failed Deployment Recovery Time (median, hours)",
      "type": "stat",
      "targets": [
        {
          "expr": "histogram_quantile(0.50, rate(recovery_time_minutes_bucket[30d])) / 60",
          "legendFormat": "Median recovery time (hours)"
        }
      ]
    }
  ]
}
```

### Executive Reporting Template

The following table format is suitable for quarterly business reviews and compliance evidence:

| Metric | Current (30-day) | Target | DORA Level | Trend |
|--------|-----------------|--------|------------|-------|
| Deployment Frequency | `X.X` deploys/day | `Y.Y` deploys/day | Elite / High / Medium / Low | ↑ / ↓ / → |
| Lead Time for Changes | `X.X` hours (median) | `Y.Y` hours | Elite / High / Medium / Low | ↑ / ↓ / → |
| Change Failure Rate | `X.X`% | `< Y%` | Elite / High / Medium / Low | ↑ / ↓ / → |
| Failed Deployment Recovery Time | `X.X` hours (median) | `< Y hours` | Elite / High / Medium / Low | ↑ / ↓ / → |

---

## Integration Across Techstream Frameworks

DORA metrics are a cross-cutting measurement layer. Each framework contributes to specific metric improvement:

| Framework | Primary DORA Contribution |
|-----------|--------------------------|
| `secure-pipeline-templates` | **Lead Time** — Parallel scan stages, caching, and fast feedback reduce pipeline duration. **CFR** — Security gates prevent vulnerable code from reaching production. |
| `release-orchestration-framework` | **Deployment Frequency** — Standardized promotion gates reduce manual coordination friction. **Recovery Time** — Automated rollback and runbook integration reduce MTTR. |
| `secure-ci-cd-reference-architecture` | **Lead Time** — Well-designed pipeline architecture minimizes queuing and bottlenecks. **CFR** — Secure pipeline controls reduce post-deployment security incidents. |
| `devsecops-maturity-model` | **All metrics** — Maturity assessment identifies which practices are limiting improvement in each metric. |
| `devsecops-methodology` | **Deployment Frequency** — Transformation program addresses cultural and process barriers to frequent deployment. |
| `cloud-security-devsecops` | **CFR and Recovery Time** — Solid cloud security posture reduces infrastructure-related incidents that appear in CFR. |

### Recommended Integration Architecture

```
Source of Truth                 Metrics Collection          Visualization
─────────────────               ──────────────────          ─────────────
GitHub Actions /                                            Grafana
GitLab CI /         ──events──▶ DORA Metrics       ──────▶ Datadog
Jenkins                         Service              query  Backstage
                                (custom or           ──────▶ Google DORA
Incident Mgmt       ──events──▶ Faros/Cortex/              Quick Check
(PagerDuty,                     LinearB/etc.)
OpsGenie)

Release             ──events──▶
Orchestration
Framework
```

**Recommended open-source and commercial tooling:**

| Tool | Type | DORA Metrics Supported |
|------|------|----------------------|
| Faros AI | Open source | All four |
| Backstage + plugins | Open source | All four (with pipeline and incident integrations) |
| LinearB | Commercial | All four + Engineering Intelligence |
| Cortex | Commercial | All four + service catalog integration |
| Google DORA Quick Check | Free assessment | Self-reported assessment (not instrumented) |
| Sleuth | Commercial | All four with automated attribution |

---

## Improvement Playbook

### Improving Deployment Frequency

**If deployment frequency is Low or Medium:**

1. Identify the batch size: are teams accumulating changes before releasing? Why?
2. Check release scheduling: are there change windows or CAB reviews blocking frequent deploys?
3. Assess test confidence: do teams fear deploying frequently because tests don't catch regressions?
4. Check pipeline reliability: do pipelines fail frequently, discouraging use?

**Actions:**
- Implement feature flags to decouple deploy from release (deploy dark; release via flag)
- Reduce batch size through trunk-based development practice
- Replace scheduled CAB reviews with automated change risk assessment
- Invest in test coverage to build deploy confidence

### Improving Lead Time for Changes

**If lead time is High or Medium:**

1. Measure each segment: code review time, pipeline duration, approval wait, deployment duration
2. Identify the longest segment — that is where to focus

**Actions by bottleneck:**
- **Code review too slow:** Reduce PR size; CODEOWNERS configuration; async review culture
- **Pipeline too slow:** Parallelize stages; cache dependencies; incremental scanning
- **Manual approvals blocking:** Define clear criteria for automated approval; use risk-based approval thresholds
- **Deployment too slow:** Faster rollout strategy (canary with smaller initial weight is not necessarily faster — optimize for rollout duration)

### Improving Change Failure Rate

**If CFR is High or Medium:**

1. Analyze failure patterns: which services fail most? What type of changes fail?
2. Check testing coverage for the failure categories
3. Assess pre-production environment fidelity

**Actions:**
- Add or improve tests for the change categories that fail most (integration tests, contract tests)
- Improve staging environment to more closely mirror production
- Implement automated rollback so failures are recovered quickly (also improves Recovery Time)
- Add synthetic monitoring and smoke tests post-deployment

### Improving Failed Deployment Recovery Time

**If Recovery Time is High or Medium:**

1. Measure detection latency: how long from failure to alert?
2. Measure diagnosis time: how long to identify root cause?
3. Measure action time: how long to execute rollback or hotfix?

**Actions:**
- Deploy synthetic monitoring and SLO alerting (detection)
- Link deployment notifications with runbooks (diagnosis)
- Automate rollback trigger on error rate thresholds (action)
- Conduct blameless post-mortems to identify recurring failure patterns

---

## Security Considerations for DORA Metrics Infrastructure

The DORA metrics collection system itself is sensitive infrastructure. Deployment event data reveals release schedules, version cadence, and team velocity — information that should be protected.

| Control | Implementation |
|---------|---------------|
| Metrics API authentication | Service-to-service authentication via short-lived tokens (OIDC or client certificates) |
| Audit log of metric ingestion | All deployment events logged with pipeline job ID for traceability |
| Metrics data retention | Align to organizational data retention policy; deployment events: 2 years |
| Access control to DORA dashboards | Role-based access; executive dashboards do not expose individual commit or developer-level data |
| Metrics pipeline integrity | Pipeline events should be signed or authenticated to prevent spoofing of deployment records |

---

## Related Documents

- [Framework Selection Guide](framework-selection-guide.md) — Choosing where to start based on organizational goals
- [Integration Scenarios](integration-scenarios.md) — End-to-end implementation walkthroughs
- [Release Orchestration Framework](../../release-orchestration-framework/docs/framework.md) — Deployment governance and rollback automation
- [DevSecOps Maturity Model: Metrics and KPIs](../../devsecops-maturity-model/docs/metrics-kpis.md) — Broader security and delivery metric set
- [DevSecOps Framework: KPIs](../../devsecops-framework/docs/framework.md) — Security-specific KPI definitions
