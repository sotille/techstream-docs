# Changelog

All notable changes to the Techstream Documentation Portal are documented here.
Format: `[version] — [date] — [summary of changes]`

---

## [Unreleased]

- [2026-04-08] Added Scenario 8 (Securing an AI-Assisted DevSecOps Pipeline End-to-End) to docs/integration-scenarios.md — three-phase integration scenario (MVASP establishment → systematic controls → forensic readiness/governance) for organizations deploying AI at the developer, code review, and autonomous remediation layers; includes integration flow diagram, week-by-week Phase 1 implementation sequence, Phase 2 and Phase 3 objectives and control references, control-to-framework mapping table for 12 AI security controls, and expected outcome narrative; updated Table of Contents and Framework Touchpoint Reference table to include AI pipeline security, agent forensics, and AI security maturity rows

- [2026-04-08] Updated docs/cross-framework-incident-response.md — major expansion covering AI agent incidents: (1) Phase 2 Containment: added AI/Agent Systems containment procedures including volatile evidence capture before pod termination (agent context window, pod state JSON), tool authorization policy emergency lockdown via ConfigMap override, GitHub Actions workflow disable, credential rotation sequence, and list of evidence items to preserve before pod deletion; (2) Phase 3 Investigation: added Five Forensic Questions investigation procedures with bash commands for reconstructing tool call sequences (Q1), recovering system prompt and instructions (Q2), identifying data access and transmission (Q3), full tool inventory with parameters (Q4), and authorization basis verification with UNAUTHORIZED/UNDETERMINED flagging (Q5) — includes prompt injection pattern detection and evidence gap documentation guidance; (3) Phase 5 Lessons Learned: added AI DevSecOps Framework and Forensics and IR Framework review questions covering authorization policy compliance, behavioral monitoring effectiveness, prompt injection indicators, Five Questions answerability, and session reconstruction capability; (4) Related Resources: added 4 new links to agent forensics, behavioral baseline, agent authorization, and agent audit trail documents

- Added CHANGELOG.md (this file) for version tracking
- [2026-04-07] docs/glossary.md: Added 7 AI agent security terms — POLA (agent-specific), Context Window Poisoning, Cascade Compromise, Agent Forensics, Five Forensic Questions, Forensics Readiness Score (FRS 1–5), and MVASP (Minimum Viable AI Security Program) — with cross-references to forensics-and-incident-response-framework
- Added "Learning Resources" section to README.md linking to all four book volumes, techstream-learn labs, and techstream.app
- [2026-04-07] Added Ecosystem Architecture section to README.md — describes all 4 ecosystem layers with audience, purpose, and layer boundary guidance for new users
- [2026-04-07] Added CNAPP, CIEM, and SPIFFE/SPIRE definitions to docs/glossary.md — cloud security and workload identity terms referenced in framework docs but previously undefined
- [2026-04-07] Added forensics-and-incident-response-framework and ai-devsecops-framework to Framework Repository Index and Mermaid ecosystem map — introduced cross-cutting layer (purple) with FIRF and AIDF nodes and 7 new dependency edges
- [2026-04-07] Updated framework-selection-guide.md — added forensics investigation and AI/agentic security paths to decision tree, updated AI-Native Organization sequence, added both frameworks to scope boundaries and prerequisites tables
- [2026-04-07] Updated cross-framework-incident-response.md — added AI agent unauthorized action and multi-domain forensic investigation incident types; added AI/agent systems domain lead role
- [2026-04-07] Added Book 5 (AI and Agentic Systems Security) to README.md Learning Resources section; updated "all four volumes" references to "all five volumes"
- [2026-04-07] Fixed README.md Layer 3 table — corrected "Four-volume book series" to "Five-volume book series" to match actual VOLUMES.md content
- [2026-04-07] Updated techstream-books/editorial/glossary-master.md — added Agent forensics (A), Jailbreak (J, new section), MCP and Model poisoning (M, new section), Prompt injection (P), Slopsquatting (S)

## [1.0.0] — 2024-01-15

- Initial public release of the Techstream Documentation Portal
- Core documentation: introduction, architecture, framework, implementation, best-practices, roadmap
- Framework selection guide for choosing the right Techstream framework for specific use cases
- Framework customization guide for enterprise tailoring
- Framework migration guide for organizations transitioning from legacy security frameworks
- Integration scenarios covering common multi-framework deployment patterns
- DORA metrics guide for measuring DevSecOps delivery performance
- Cross-framework incident response playbook
- Investment framework for calculating DevSecOps ROI
- Unified glossary aligned with the Techstream Book Series master glossary
- Troubleshooting guide for common framework adoption challenges
- Apache 2.0 license and contribution guidelines
