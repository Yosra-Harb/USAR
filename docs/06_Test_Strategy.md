# USAR Intelligent Rescue System
## Test Strategy

**Document:** 06_Test_Strategy.md  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Purpose:** Define the minimum testing approach required before implementation begins  

---

# 1. Purpose

This document defines how the USAR Intelligent Rescue System will be verified throughout development.

The goal is not to create a large testing bureaucracy before coding starts. The goal is to agree, before implementation, on:

- what must be tested;
- who owns each test area;
- which tests are required before integration;
- how regression failures are handled;
- and what conditions must be satisfied before a feature is considered complete.

Testing is part of development, not a final activity performed only at the end.

---

# 2. Testing Principles

The project shall follow these principles:

1. Every major module must be independently testable.
2. Shared interfaces must be tested before integration.
3. Ground truth must never enter operational decision logic.
4. Previously validated behavior must remain protected by regression tests.
5. AI evaluation must use separated development and sealed-test data.
6. Hardware communication failures must be tested explicitly.
7. End-to-end integration must begin before all modules are complete.
8. A feature is not complete until its required tests pass.

---

# 3. Test Levels

The project will use the following test levels:

```text
Unit Tests
    ↓
Interface / Contract Tests
    ↓
Module Integration Tests
    ↓
System Integration Tests
    ↓
Regression Tests
    ↓
AI Validation Tests
    ↓
Hardware-in-the-Loop Tests
    ↓
End-to-End Tests
    ↓
Acceptance Tests
```

---

# 4. Unit Testing

Each workstream owns unit tests for its internal logic.

## Workstream 1 — Simulation and Mission

Test:

- scenario creation;
- seed reproducibility;
- victim placement;
- mission-state transitions;
- probe movement;
- coverage calculation;
- search-path behavior;
- ground-truth isolation.

## Workstream 2 — Sensors and Embedded

Test:

- radar processing;
- thermal processing;
- acoustic processing;
- normalization;
- availability handling;
- malformed hardware packets;
- packet sequencing;
- disconnect/reconnect behavior;
- packet-loss detection.

## Workstream 3 — Fusion and Reliability

Test:

- measurement-quality calculation;
- sensor-health handling;
- sensor agreement;
- adaptive weighting;
- sensor dropout;
- degraded-sensor behavior;
- HAIF calculations;
- confidence estimation;
- one-sided anomaly behavior.

## Workstream 4 — Localization and Rescue Decision

Test:

- evidence-map update;
- decay;
- reliable masking;
- peak detection;
- weighted position estimation;
- localization acceptance/rejection;
- victim association;
- duplicate merging;
- vitality calculation;
- rescue-priority ordering.

## Workstream 5 — AI and Dashboard

AI tests:

- feature-schema validation;
- preprocessing;
- model inference;
- probability calibration;
- uncertainty output;
- abstention;
- model-version metadata;
- missing-data handling.

Dashboard tests:

- mission payload parsing;
- victim selection;
- map synchronization;
- AI state rendering;
- sensor-health rendering;
- hardware-state rendering;
- error-state rendering.

---

# 5. Interface / Contract Testing

Every contract defined in:

`05_Interface_Contracts.md`

must have at least one producer-consumer test.

Required boundaries:

```text
ScenarioContext → Sensor Layer
HardwarePacket → Acquisition Adapter
SensorObservation → Fusion
FusionOutput → Localization
LocalizationOutput → Victim Tracking
VictimTrack → CandidateFeatureRecord
CandidateFeatureRecord → AI
AIOutput → Rescue Decision
RescueDecision → MissionState
MissionState → Dashboard
```

Each contract test shall verify:

- schema version;
- required fields;
- data types;
- numeric ranges;
- enum values;
- missing-value behavior;
- successful parsing.

---

# 6. Regression Testing

Regression tests protect previously validated behavior.

A code change must not silently break a previously passing core test.

Regression testing is especially important for:

- HAIF;
- localization;
- candidate tracking;
- vitality;
- rescue decision;
- AI data generation;
- integration contracts.

A regression suite shall be run before merging high-risk changes.

---

# 7. Simulation Scenario Testing

The system shall be evaluated across controlled scenarios including:

- Ideal;
- Dense Debris;
- High Noise;
- Deep Burial;
- Multiple Victims;
- Weak Vital Signs.

Each scenario should be executed across multiple seeds.

Recorded outputs should include:

- detection performance;
- localization performance;
- fusion behavior;
- system reliability;
- AI behavior;
- rescue-decision outputs;
- response time.

---

# 8. Sensor Degradation Testing

The project shall explicitly test:

```text
Normal Sensor
Degraded Sensor
Attenuated Sensor
Noisy Sensor
Sensor Dropout
Conflicting Sensor
```

Expected behavior:

- degraded measurements should receive reduced influence when justified;
- unavailable sensors should not contribute operational weight;
- healthy sensors should not be penalized solely because another sensor becomes anomalous;
- the system should degrade gracefully where sufficient evidence remains.

---

# 9. Localization Testing

Localization testing shall include:

- clean single-victim cases;
- noisy evidence;
- sparse evidence;
- multiple victims;
- weak signals;
- competing evidence peaks;
- insufficient evidence;
- repeated observations.

Metrics shall include:

- localization error;
- localization-confidence behavior;
- accepted localization rate;
- rejected localization rate;
- spatial stability.

Ground truth shall be used only by the evaluation layer.

---

# 10. AI Testing Strategy

The AI layer requires separate development and evaluation controls.

## 10.1 Data Integrity Tests

Verify:

- no split overlap;
- no oracle leakage;
- no forbidden ground-truth feature;
- no duplicate mission contamination across protected splits;
- schema consistency;
- missingness handling.

## 10.2 Training Tests

Verify:

- training pipeline executes reproducibly;
- expected features are loaded;
- model artifact is produced;
- model metadata is stored.

## 10.3 Classification Evaluation

Measure, as applicable:

- precision;
- recall;
- F1-score;
- confusion matrix;
- false positives;
- false negatives.

## 10.4 Calibration Evaluation

Evaluate whether predicted probabilities correspond reasonably to observed outcomes using approved calibration metrics and reliability analysis.

## 10.5 Uncertainty Tests

Verify:

- uncertainty output is generated;
- uncertainty stays within the defined representation;
- high-uncertainty cases can trigger abstention.

## 10.6 Abstention Tests

Verify the three operational outcomes:

```text
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
```

The system must not force a binary answer when the approved abstention policy is triggered.

## 10.7 Sealed Test

The sealed test set shall not be used for:

- model tuning;
- threshold tuning;
- feature selection;
- calibration selection.

It shall be used only after development choices are frozen.

---

# 11. Hardware-in-the-Loop Testing

HIL testing shall verify the complete acquisition path:

```text
Microcontroller
    ↓
Communication
    ↓
Packet Validation
    ↓
SensorObservation
    ↓
Core Pipeline
```

Required cases:

- normal connection;
- connection loss;
- reconnect;
- malformed packet;
- missing packet;
- duplicated packet;
- delayed packet;
- sensor unavailable;
- degraded sensor status;
- unsupported schema version.

---

# 12. Dashboard Testing

The dashboard shall be tested using controlled mission-state fixtures before live integration.

Required states include:

- no victim detected;
- candidate detected;
- confirmed victim;
- multiple victims;
- AI abstention;
- low localization confidence;
- sensor degradation;
- hardware disconnected;
- mission completed;
- system error.

The dashboard must clearly distinguish:

- operational data;
- warning states;
- critical rescue information;
- evaluation-only ground truth.

---

# 13. End-to-End Testing

The final system shall support an end-to-end test:

```text
Mission Creation
    ↓
Sensor Input
    ↓
Signal Processing
    ↓
HAIF Fusion
    ↓
Localization
    ↓
Victim Tracking
    ↓
AI Validation
    ↓
Vitality
    ↓
Rescue Priority
    ↓
Dashboard
```

A second end-to-end path shall replace simulated acquisition with:

```text
Microcontroller / HIL
```

while keeping the downstream pipeline consistent.

---

# 14. Evaluation Metrics

The final evaluation framework should support, where applicable:

## Detection

- Precision
- Recall
- F1-score
- False Positives
- False Negatives
- Critical Success Index

## Localization

- localization error;
- median localization error;
- accepted localization rate.

## Fusion

- modality weights;
- effective support;
- sensor agreement;
- robustness under degradation.

## AI

- classification metrics;
- calibration metrics;
- uncertainty behavior;
- abstention rate;
- selective performance.

## System

- response time;
- mission completion;
- hardware packet loss;
- failure recovery;
- end-to-end stability.

---

# 15. Test Ownership

| Test Area | Primary Owner | Reviewer |
|---|---|---|
| Simulation / Mission | Workstream 1 | Workstream 2 |
| Sensors / Embedded | Workstream 2 | Workstream 3 |
| HAIF / Fusion | Workstream 3 | Workstream 4 |
| Localization / Tracking / Decision | Workstream 4 | Workstream 5 |
| AI | Workstream 5 | Workstream 3 or 4 |
| Dashboard | Workstream 5 | Workstream 1 |
| End-to-End Integration | Entire Team | Technical Lead |

---

# 16. Quality Gates

A change shall not be merged when:

- required unit tests fail;
- interface tests fail;
- critical regression tests fail;
- a shared schema is changed without documentation;
- oracle leakage is detected;
- integration is broken;
- required reviewer approval is missing.

---

# 17. Definition of Done

A feature is considered Done only when:

1. implementation is complete;
2. required tests exist;
3. required tests pass;
4. interface contracts are respected;
5. regression remains green;
6. reviewer approval is complete;
7. documentation is updated;
8. integration behavior is demonstrated where applicable.

---

# 18. Minimum Test Assets Required Before Sprint 1

Before full parallel implementation begins, the repository should contain:

```text
interfaces/fixtures/
```

with at least:

```text
sensor_observation.json
fusion_output.json
localization_output.json
victim_track.json
candidate_features.json
ai_output.json
mission_state.json
hardware_packet.json
```

These fixtures allow all five workstreams to begin independently.

---

# 19. Testing During Sprints

Testing shall occur inside each sprint.

Recommended flow:

```text
Implement
    ↓
Unit Test
    ↓
Interface Test
    ↓
Pull Request
    ↓
Peer Review
    ↓
Integration Test
    ↓
Regression Check
```

Testing shall not be deferred to the final project phase.

---

# 20. Pre-Development Approval

This Test Strategy is considered sufficient for project start when:

- test levels are agreed;
- owners are known;
- shared interfaces are defined;
- initial fixtures are planned;
- regression policy is agreed;
- AI sealed-test policy is agreed;
- HIL failure cases are defined;
- Definition of Done is accepted.

Detailed test cases can be created incrementally during implementation and do not need to be fully written before development begins.
