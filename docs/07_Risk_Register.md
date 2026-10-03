# USAR Intelligent Rescue System
## Risk Register

**Document:** 07_Risk_Register.md  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Purpose:** Identify, prioritize, and manage risks before implementation begins  

---

# 1. Purpose

This document records the major technical, integration, research, hardware, delivery, and team risks that may affect the USAR Intelligent Rescue System.

The goal is not to predict every possible problem.

The goal is to identify the risks that could:

- block development;
- invalidate results;
- delay integration;
- reduce system quality;
- cause rework;
- or prevent the final demonstration from working.

Each major risk shall have:

- an owner;
- probability;
- impact;
- mitigation;
- trigger;
- contingency action.

---

# 2. Risk Rating Method

Probability and impact shall use:

```text
Low
Medium
High
```

Overall priority is based on both probability and impact.

Recommended interpretation:

| Probability | Impact | Priority |
|---|---|---|
| High | High | Critical |
| High | Medium | High |
| Medium | High | High |
| Medium | Medium | Medium |
| Low | High | Medium |
| Low | Low | Low |

---

# 3. Risk Status Values

Use:

```text
OPEN
MONITORING
MITIGATED
OCCURRED
CLOSED
```

---

# 4. Core Risk Register

| ID | Risk | Probability | Impact | Priority | Owner | Mitigation |
|---|---|---:|---:|---:|---|---|
| R-001 | Project scope becomes too large | High | High | Critical | Technical Lead | Freeze MVP scope and classify backlog as MUST/SHOULD/COULD |
| R-002 | Integration is delayed until the end | High | High | Critical | Entire Team | Use interface contracts, mocks, and integration checkpoints every sprint |
| R-003 | One member becomes a bottleneck | Medium | High | High | Technical Lead | Clear interfaces, peer reviewers, shared test fixtures, documentation |
| R-004 | Shared interface changes break other modules | Medium | High | High | Interface Owner | Version schemas and require review for breaking changes |
| R-005 | Localization becomes unstable under difficult scenarios | Medium | High | High | Workstream 4 | Maintain regression tests, baselines, acceptance criteria, fallback rejection |
| R-006 | HAIF improvements reduce robustness in another condition | Medium | High | High | Workstream 3 | Use fixed regression suite, clean/degraded comparisons, development vs validation separation |
| R-007 | AI dataset has insufficient HARD_NEGATIVE samples | Medium | High | High | Workstream 5 | Controlled distractor generation, class-balance monitoring, no premature model freezing |
| R-008 | AI model overfits development data | Medium | High | High | Workstream 5 | Train/validation/sealed-test separation and reproducible evaluation |
| R-009 | Oracle leakage enters AI features | Low | High | High | Workstream 5 | Automated leakage checks and feature allowlist |
| R-010 | AI probabilities are overconfident | Medium | High | High | Workstream 5 | Probability calibration and uncertainty-aware abstention |
| R-011 | Microcontroller or required hardware is unavailable | Medium | High | High | Workstream 2 | Emulated packets, HIL abstraction, hardware-independent core pipeline |
| R-012 | Serial/HIL communication is unreliable | Medium | Medium | Medium | Workstream 2 | Packet sequence, validation, reconnect logic, packet-loss telemetry |
| R-013 | Dashboard development blocks on unavailable backend data | High | Medium | High | Workstream 5 | Build dashboard against stable MissionState fixtures |
| R-014 | Simulation behavior is not reproducible | Low | High | Medium | Workstream 1 | Seed control, configuration logging, versioned scenarios |
| R-015 | Ground truth leaks into operational decision logic | Low | High | High | Workstream 1 + 5 | Ground-truth firewall and explicit evaluation-only interfaces |
| R-016 | Regression tests become too slow to run frequently | Medium | Medium | Medium | Technical Lead | Separate smoke, integration, and full regression suites |
| R-017 | Team members work on isolated code that cannot integrate | Medium | High | High | Entire Team | Contract-first development, PR review, shared integration goals |
| R-018 | Dashboard becomes visually complex and hard to use | Medium | Medium | Medium | Workstream 5 | Prioritize operational decisions over raw telemetry |
| R-019 | Vitality/rescue-priority logic is interpreted as medical diagnosis | Low | High | Medium | Workstream 4 | Clearly label as decision-support prototype, not clinical diagnosis |
| R-020 | Final demo depends on live hardware and fails | Medium | High | High | Entire Team | Maintain deterministic Simulation Mode and recorded fallback demo |
| R-021 | Git conflicts or accidental main-branch changes | Medium | Medium | Medium | Entire Team | Feature branches, PR review, protected main where possible |
| R-022 | Testing is postponed until late stages | Medium | High | High | Technical Lead | Definition of Done requires tests before merge |
| R-023 | Requirements expand during development without review | High | Medium | High | Technical Lead | Change-control rule and backlog prioritization |
| R-024 | AI runtime integration differs from training feature pipeline | Medium | High | High | Workstream 5 | Shared feature schema, versioning, inference fixtures |
| R-025 | Data schemas differ across MATLAB, Python, and Dashboard | Medium | High | High | Integration Owner | Central interface contracts and JSON fixtures |
| R-026 | Limited compute resources slow experiments | Medium | Medium | Medium | Workstream 3 + 5 | Use staged experiment sizes, smoke runs, cached artifacts |
| R-027 | Results cannot be reproduced before submission | Low | High | Medium | Entire Team | Store seeds, configs, model versions, and experiment metadata |
| R-028 | One subsystem produces plausible but invalid values | Medium | High | High | Module Owner | Range validation, explicit error states, interface tests |
| R-029 | Team workload becomes unbalanced | Medium | Medium | Medium | Technical Lead | Review workload each sprint and reassign secondary tasks |
| R-030 | Final integration exposes hidden dependencies | Medium | High | High | Entire Team | Early incremental integration and end-to-end smoke tests |

---

# 5. Detailed Critical Risks

## R-001 — Scope Creep

### Description

The project combines:

- simulation;
- three sensor modalities;
- signal processing;
- HAIF;
- localization;
- tracking;
- AI;
- uncertainty;
- abstention;
- vitality;
- rescue priority;
- microcontroller integration;
- HIL;
- dashboard;
- and evaluation.

The project can become too large if new features continue to be added.

### Trigger

New features are introduced without replacing or deprioritizing existing work.

### Mitigation

Use backlog priority:

```text
MUST
SHOULD
COULD
WON'T FOR CURRENT RELEASE
```

Only MUST items are required for the integrated graduation-project baseline.

### Contingency

Freeze new features and focus only on:

```text
End-to-End Working System
Testing
Integration
Demo
Documentation
```

---

## R-002 — Late Integration

### Description

Each module may work independently while the full system fails when combined.

### Trigger

A sprint ends without any cross-module integration test.

### Mitigation

At least one integration checkpoint per sprint.

Use fixtures from:

```text
interfaces/fixtures/
```

### Contingency

Create a dedicated integration sprint and freeze non-critical feature development.

---

## R-005 — Localization Instability

### Description

Localization is one of the most technically difficult components and may degrade under sparse, noisy, or conflicting evidence.

### Trigger

Any of the following:

- unstable victim position;
- large error increase;
- low accepted-localization rate;
- false localization peaks;
- multi-victim merging problems.

### Mitigation

Maintain:

- localization regression tests;
- clean and degraded scenarios;
- explicit acceptance criteria;
- localization rejection when evidence is insufficient.

### Contingency

Prefer:

```text
No Accepted Localization
```

over a confident but unsupported location.

---

## R-006 — HAIF Regression

### Description

A fusion improvement under one degraded condition may damage clean behavior or another sensor-degradation condition.

### Trigger

Previously passing structural or clean-condition regression tests fail.

### Mitigation

Maintain separate:

- clean regression;
- attenuation regression;
- dropout regression;
- anomaly regression;
- baseline comparison.

### Contingency

Rollback the change or isolate it behind a configuration flag until revalidated.

---

## R-007 — Insufficient AI Class Balance

### Description

The candidate dataset may contain many TRUE_TRACK samples but too few meaningful HARD_NEGATIVE samples.

### Trigger

Negative candidate count is too small for stable training/evaluation.

### Mitigation

Use controlled non-victim distractor scenarios and maintain explicit dataset-quality gates.

### Contingency

Delay model freezing rather than training a misleading classifier.

---

## R-009 — Oracle Leakage

### Description

Ground-truth information can accidentally enter AI features, creating unrealistically strong results.

### Trigger

Any feature includes:

- true victim label;
- true victim coordinates;
- distance to true victim;
- future mission outcome;
- equivalent derived oracle information.

### Mitigation

Maintain:

- feature allowlist;
- leakage audit;
- automated tests;
- ground-truth firewall.

### Contingency

Invalidate affected experiments and regenerate the dataset.

---

## R-011 — Hardware Unavailability

### Description

Physical sensors or the intended microcontroller configuration may not be available when required.

### Trigger

Hardware cannot be obtained, repaired, or connected on schedule.

### Mitigation

The architecture already separates:

```text
Simulation Mode
HIL Mode
```

and uses a common `SensorObservation` contract.

### Contingency

Use:

- microcontroller with emulated sensor values;
- recorded hardware-like packets;
- HIL demonstration with available components.

The core system must remain demonstrable in Simulation Mode.

---

## R-020 — Final Demo Hardware Failure

### Description

A live hardware demo may fail because of connection, power, or device issues.

### Trigger

Hardware status becomes unstable during final testing.

### Mitigation

Prepare:

1. Live HIL demo.
2. Deterministic Simulation Mode demo.
3. Recorded mission playback as final fallback.

### Contingency

Switch immediately to a reproducible simulation or playback demonstration.

The academic demonstration must not depend on one fragile live hardware path.

---

# 6. Team and Delivery Risks

## 6.1 Blocking Dependency Risk

No member should wait for another member's unfinished implementation.

Mitigation:

```text
Mocks
Fixtures
Stable Contracts
Parallel Development
```

## 6.2 Knowledge Concentration Risk

At least one second member shall review every critical module.

## 6.3 Workload Imbalance Risk

Actual work shall be reviewed every sprint.

The team may reassign:

- tests;
- visual components;
- experiment automation;
- documentation;
- integration work.

Core technical ownership remains stable unless formally changed.

---

# 7. Research Risks

Research-related risks include:

- overfitting;
- weak baselines;
- selective reporting;
- contaminated test data;
- poor reproducibility;
- tuning against validation repeatedly;
- using test results to redesign the model.

Mitigation shall include:

- frozen baselines;
- explicit development vs validation vs sealed-test separation;
- recorded configurations;
- documented failures as well as successes;
- no hidden oracle variables.

---

# 8. Hardware Risks

Hardware risks include:

- device unavailable;
- serial port conflict;
- damaged cable;
- unstable power;
- packet corruption;
- packet loss;
- sensor dropout;
- incompatible sampling rate.

The system must expose these failures rather than silently treating them as valid measurements.

---

# 9. Dashboard Risks

Potential dashboard risks:

- too much information on one screen;
- critical alerts lost among charts;
- inconsistent values between panels;
- frontend tightly coupled to MATLAB internals.

Mitigation:

- consume only `MissionState`;
- prioritize map + decision information;
- separate detailed technical tabs;
- use schema validation.

---

# 10. Risk Review Cadence

Risks shall be reviewed:

```text
At Sprint Planning
At Sprint Review
Whenever a critical technical decision changes
Whenever a new blocker appears
```

The full register does not need to be rewritten every sprint.

Only changed risks need updates.

---

# 11. Jira Risk Handling

Critical or active risks that require work should create Jira issues.

Example:

```text
Risk:
R-012 Serial communication instability

Jira Task:
Implement reconnect and packet-loss handling

Owner:
Workstream 2
```

The Risk Register records the risk.

Jira tracks the work needed to reduce it.

---

# 12. Risk Ownership

The risk owner is responsible for:

- monitoring the risk;
- recognizing triggers;
- ensuring mitigation work is planned;
- escalating when necessary.

Risk ownership does not mean the owner must solve the problem alone.

---

# 13. Pre-Development Risk Gate

The project is ready to begin implementation when:

- Critical risks have mitigation plans;
- Simulation Mode can serve as fallback;
- HIL does not block core algorithm development;
- interface-change control is agreed;
- AI data-integrity rules are agreed;
- ground-truth isolation is agreed;
- integration will occur during every sprint;
- scope priorities are defined.

---

# 14. Initial Critical Risks to Monitor from Sprint 1

The team should actively monitor these first:

```text
R-001 Scope Creep
R-002 Late Integration
R-004 Interface Breakage
R-005 Localization Instability
R-006 HAIF Regression
R-007 AI Dataset Balance
R-009 Oracle Leakage
R-011 Hardware Availability
R-017 Isolated Development
R-020 Demo Hardware Failure
R-025 Cross-Technology Schema Mismatch
```

---

# 15. Risk Register Update Template

Use this format for new risks:

```text
Risk ID:
Title:

Description:

Probability:
Low / Medium / High

Impact:
Low / Medium / High

Priority:
Low / Medium / High / Critical

Owner:

Trigger:

Mitigation:

Contingency:

Status:
OPEN / MONITORING / MITIGATED / OCCURRED / CLOSED
```

---
