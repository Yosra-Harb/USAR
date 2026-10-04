# USAR Intelligent Rescue System
## Test Strategy

**Document:** `06_Test_Strategy.md`  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Purpose:** Define the minimum testing approach required before implementation begins  

---

# 1. Purpose

This document defines how the USAR Intelligent Rescue System will be verified throughout development.

The goal is not to create unnecessary testing bureaucracy before coding starts. The goal is to agree on:

- what must be tested;
- who owns each test area;
- which tests are required before integration;
- how regression failures are handled;
- how the microcontroller/HIL path is verified;
- how protected AI and research evaluation data are handled;
- and what conditions must be satisfied before a feature is considered complete.

Testing is part of development, not a final activity performed only at the end.

---

# 2. Testing Principles

The project shall follow these principles:

1. Every major module must be independently testable.
2. Shared interfaces must be tested before integration.
3. Ground truth must never enter operational decision logic.
4. Previously validated scientific behavior must remain protected by regression tests.
5. AI evaluation must use separated development and sealed-test data.
6. Hardware communication failures must be tested explicitly.
7. The microcontroller/HIL path is a required product path.
8. Simulation and HIL should share the same downstream processing after `SensorObservation`.
9. End-to-end integration must begin before all modules are complete.
10. A feature is not complete until its required tests pass.

---

# 3. Test Levels

The project will use the following levels:

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

# 4. Unit Testing by Workstream

Each workstream owns unit tests for its internal logic.

## Workstream 1 — Simulation and Mission Systems

Test:

- scenario creation;
- seed reproducibility;
- victim placement;
- mission-state transitions;
- probe movement;
- coverage calculation;
- search-path behavior;
- ground-truth isolation;
- mission-event generation.

## Workstream 2 — Sensors, Microcontroller, Acquisition, and Signal Processing

Test:

- radar processing;
- thermal processing;
- acoustic processing;
- simulated sensor degradation;
- normalization;
- acquisition-level availability handling;
- microcontroller packet creation/parsing;
- malformed hardware packets;
- packet sequencing;
- duplicate packets;
- delayed packets;
- disconnect/reconnect behavior;
- packet-loss detection;
- conversion to `SensorObservation`;
- hardware-status generation.

## Workstream 3 — HAIF Fusion and Localization

### HAIF / Fusion

Test:

- measurement-quality calculation;
- sensor-health handling;
- sensor agreement;
- adaptive weighting;
- sensor dropout;
- degraded-sensor behavior;
- HAIF calculations;
- confidence estimation;
- one-sided anomaly behavior;
- effective-support behavior;
- protected regression behavior.

### Localization

Test:

- evidence-map update;
- evidence decay;
- reliability masking;
- evidence-peak detection;
- weighted position estimation;
- localization confidence;
- localization acceptance/rejection;
- insufficient evidence;
- competing evidence peaks;
- spatial stability.

## Workstream 4 — Victim Tracking, AI, Vitality, and Rescue Decision

### Tracking

Test:

- candidate creation;
- association;
- duplicate merging;
- track update count;
- independent views;
- temporal stability;
- existence probability;
- multi-victim behavior.

### AI

Test:

- feature-schema validation;
- preprocessing;
- no-oracle leakage;
- split integrity;
- reproducible training;
- model inference;
- probability calibration;
- uncertainty output;
- abstention;
- model-version metadata;
- missing-data handling.

### Vitality / Rescue Decision

Test:

- vitality calculation;
- confirmed/rejected/abstained candidate handling;
- rescue-priority ordering;
- multi-victim ranking;
- recommendation consistency.

## Workstream 5 — Backend, Dashboard, and System Integration

### Backend

Test:

- API route behavior;
- telemetry parsing;
- mission-state assembly;
- schema validation;
- structured errors;
- ground-truth safety boundary;
- result/evaluation release behavior where applicable;
- MATLAB/backend integration adapters;
- AI/backend integration adapters;
- HardwareStatus ingestion.

### Dashboard

Test:

- mission payload parsing;
- mission status rendering;
- victim selection;
- map synchronization;
- AI state rendering;
- sensor-health rendering;
- localization rendering;
- rescue-priority rendering;
- hardware-state rendering;
- error/loading-state rendering;
- mission lifecycle rendering.

---

# 5. Interface / Contract Testing

Every contract defined in:

```text
05_Interface_Contracts.md
```

must have at least one producer-consumer test.

Required boundaries:

```text
ScenarioContext → Sensor Layer
HardwarePacket → Acquisition Adapter
Acquisition Adapter → SensorObservation
SensorObservation → HAIF / Fusion
FusionOutput → Localization
LocalizationOutput → Victim Tracking
VictimTrack → CandidateFeatureRecord
CandidateFeatureRecord → AI
AIOutput → Rescue Decision
RescueDecision → MissionState
HardwareStatus → MissionState
MissionState → Dashboard
```

Each contract test shall verify:

- schema version;
- required fields;
- data types;
- numeric ranges;
- enum values;
- missing-value behavior;
- successful parsing;
- controlled rejection of invalid input.

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
- AI feature schema;
- integration contracts;
- backend ground-truth safety behavior.

A regression suite shall be run before merging high-risk changes.

Research versions that are explicitly frozen shall not be modified merely to make a later experiment pass.

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
- median localization error;
- localization-confidence behavior;
- accepted localization rate;
- rejected localization rate;
- spatial stability.

Ground truth shall be used only by the evaluation layer.

---

# 10. Victim Tracking and Rescue Decision Testing

Tracking/decision testing shall include:

- repeated observations of one victim;
- two nearby candidates;
- duplicate candidate hypotheses;
- track merge behavior;
- lost and reacquired evidence where applicable;
- multiple victims;
- low-confidence localization;
- AI rejection;
- AI abstention;
- vitality ordering;
- rescue-priority consistency.

The system shall not silently convert an AI-abstained candidate into a confirmed rescue target without the approved decision policy.

---

# 11. AI Testing Strategy

The AI layer requires separate development and evaluation controls.

## 11.1 Data Integrity Tests

Verify:

- no split overlap;
- no oracle leakage;
- no forbidden ground-truth feature;
- no duplicate mission contamination across protected splits;
- schema consistency;
- missingness handling;
- feature-version consistency.

## 11.2 Training Tests

Verify:

- training pipeline executes reproducibly;
- expected features are loaded;
- model artifact is produced;
- model metadata is stored.

## 11.3 Classification Evaluation

Measure, as applicable:

- precision;
- recall;
- F1-score;
- confusion matrix;
- false positives;
- false negatives.

## 11.4 Calibration Evaluation

Evaluate whether predicted probabilities correspond reasonably to observed outcomes using approved calibration metrics and reliability analysis.

## 11.5 Uncertainty Tests

Verify:

- uncertainty output is generated;
- uncertainty stays within the defined representation;
- high-uncertainty cases can trigger abstention.

## 11.6 Abstention Tests

Verify the three operational outcomes:

```text
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
```

The system must not force a binary answer when the approved abstention policy is triggered.

## 11.7 Sealed Test

The sealed test set shall not be used for:

- model tuning;
- threshold tuning;
- feature selection;
- calibration selection.

It shall be used only after development choices are frozen.

---

# 12. Hardware-in-the-Loop Testing

HIL testing is mandatory for the final product path.

The complete acquisition path is:

```text
Physical / Emulated Sensors
    ↓
Microcontroller
    ↓
Serial / USB Communication
    ↓
HardwarePacket
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
- unsupported schema version;
- packet-rate reporting;
- packet-loss reporting.

The final HIL acceptance path may use emulated sensor values if a specific physical sensing component is unavailable, but the microcontroller communication, packet protocol, host acquisition, and downstream integration must still be demonstrated.

---

# 13. Backend and Dashboard Testing

## 13.1 Backend

The backend shall be tested using controlled telemetry and contract fixtures before live integration.

Required cases include:

- valid mission state;
- invalid schema;
- no mission available;
- partial/invalid telemetry where applicable;
- ground-truth leakage attempt;
- hardware disconnected;
- AI abstention;
- completed mission;
- structured backend error.

## 13.2 Dashboard

The dashboard shall be tested using controlled `MissionState` fixtures.

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

# 14. End-to-End Testing

The final system shall support both paths.

## 14.1 Simulation End-to-End

```text
Mission Creation
    ↓
Scenario / Simulated Sensor Input
    ↓
SensorObservation
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
Backend / MissionState
    ↓
Dashboard
```

## 14.2 HIL End-to-End

```text
Physical / Emulated Sensors
    ↓
Microcontroller
    ↓
HardwarePacket
    ↓
Acquisition / SensorObservation
    ↓
Same Downstream Processing
    ↓
Backend / MissionState
    ↓
Dashboard
```

The downstream scientific pipeline shall remain consistent wherever practical.

---

# 15. Evaluation Metrics

## Detection

- Precision;
- Recall;
- F1-score;
- False Positives;
- False Negatives;
- Critical Success Index.

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

## Hardware / Acquisition

- packet rate;
- packet-loss rate;
- malformed-packet rejection;
- reconnect behavior;
- end-to-end acquisition latency where measurable.

## System

- response time;
- mission completion;
- failure recovery;
- end-to-end stability.

---

# 16. Test Ownership

| Test Area | Primary Owner | Reviewer |
|---|---|---|
| Simulation / Mission | Workstream 1 | Workstream 2 |
| Sensors / Microcontroller / Signal Processing / HIL | Workstream 2 | Workstream 3 |
| HAIF / Fusion / Localization | Workstream 3 | Workstream 4 |
| Tracking / AI / Vitality / Rescue Decision | Workstream 4 | Workstream 5 |
| Backend / Dashboard / Platform Integration | Workstream 5 | Workstream 1 |
| End-to-End Integration | Entire Team | Technical Lead |
| Research Validation Gate | Relevant Owner + Independent Reviewer | Technical Lead / Team |

---

# 17. Quality Gates

A change shall not be merged when:

- required unit tests fail;
- interface tests fail;
- critical regression tests fail;
- a shared schema is changed without documentation/versioning;
- oracle leakage is detected;
- protected AI split rules are violated;
- integration is broken;
- a core HIL contract is broken;
- required reviewer approval is missing.

---

# 18. Definition of Done

A feature is considered Done only when:

1. implementation is complete;
2. required tests exist;
3. required tests pass;
4. interface contracts are respected;
5. protected regression remains green;
6. reviewer approval is complete;
7. documentation is updated;
8. Jira status is updated;
9. integration behavior is demonstrated where applicable.

---

# 19. Minimum Test Assets Required Before Sprint 1

Sprint 0 shall create or verify:

```text
interfaces/fixtures/
```

with at least:

```text
scenario_context.json
hardware_packet.json
sensor_observation.json
fusion_output.json
localization_output.json
victim_track.json
candidate_features.json
ai_output.json
rescue_decision.json
hardware_status.json
mission_state.json
```

These fixtures allow all five workstreams to begin Sprint 1 independently.

---

# 20. Testing During Sprints

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

# 21. Sprint 0 Test Gate

Sprint 0 is complete when:

- the v1.0 interfaces have representative fixtures;
- each workstream can run at least one local smoke test;
- the HIL packet path has a valid and invalid fixture;
- Workstream 3 can consume `SensorObservation`;
- Workstream 4 can consume `LocalizationOutput`/candidate fixtures;
- Workstream 5 can render/serve a `MissionState` fixture;
- at least one producer-consumer contract test passes;
- the repository's test commands are documented.

---

# 22. Pre-Development Approval

This Test Strategy is sufficient for project start when:

- test levels are agreed;
- owners are known;
- shared interfaces are defined;
- initial fixtures are planned;
- regression policy is agreed;
- AI sealed-test policy is agreed;
- HIL failure cases are defined;
- Definition of Done is accepted.

Detailed test cases may be created incrementally during implementation.
