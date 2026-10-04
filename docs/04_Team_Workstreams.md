# USAR Intelligent Rescue System
## Team Workstreams and Ownership Plan

**Document:** `04_Team_Workstreams.md`  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Delivery Model:** Hybrid Agile / Scrum-style Sprints  
**Primary Goal:** Balanced technical ownership, parallel development, Hardware-in-the-Loop as a core capability, and controlled integration  

---

# 1. Purpose

This document defines how the USAR Intelligent Rescue System is divided across five team members.

The allocation is designed to:

- distribute technical workload as evenly as practical;
- allow all five members to work in parallel from Sprint 0;
- keep the microcontroller and Hardware-in-the-Loop path as core project scope;
- preserve clear technical ownership;
- reduce blocking dependencies;
- require peer review across workstreams;
- maintain continuous integration;
- preserve research integrity and the ground-truth firewall;
- and ensure that the final result is one integrated rescue system rather than five isolated student projects.

The architecture is interface-driven. Every workstream shall be independently developable and testable using mocks, fixtures, recorded data, or emulated hardware packets before every upstream component is complete.

---

# 2. Final Team Structure

The project is divided into five primary technical workstreams:

1. **Simulation and Mission Systems**
2. **Sensors, Microcontroller, Acquisition, and Signal Processing**
3. **HAIF Fusion and Localization**
4. **Victim Tracking, AI Intelligence, Vitality, and Rescue Decision**
5. **Backend, Operational Dashboard, and System Integration**

Each member owns one primary workstream.

Every workstream includes:

- design;
- implementation;
- testing;
- documentation;
- integration support;
- code review;
- and evidence of completion.

No member is assigned only documentation or project-management responsibilities.

The **Technical Lead / Delivery Coordinator** role is additional to one member's technical workstream and does not replace technical ownership.

---

# 3. Workload Balancing Principle

Workload balance shall be evaluated using:

- algorithmic complexity;
- implementation effort;
- integration difficulty;
- testing burden;
- debugging risk;
- experimental responsibility;
- hardware dependency;
- cross-language integration;
- and expected maintenance effort.

The project is not considered balanced merely because every member receives the same number of Jira issues.

During Sprint Planning, the team shall compare actual workload and may redistribute **secondary tasks** while keeping ownership of core algorithms stable.

---

# 4. Workstream 1 — Simulation and Mission Systems

## Owner

**Team Member 1**

## Role Title

**Simulation and Mission Systems Engineer**

## Mission

Build and maintain the rescue simulation environment, mission model, probe behavior, search strategy, reproducible scenario generation, and simulation-only ground truth used by the rest of the system.

## Core Responsibilities

### 4.1 Scenario Generation

Implement and maintain the approved scenarios:

- Ideal;
- Dense Debris;
- High Noise;
- Deep Burial;
- Multiple Victims;
- Weak Vital Signs.

Scenario parameters include:

- debris density;
- environmental noise;
- burial depth;
- victim count;
- victim positions;
- vital strength;
- sensor-degradation configuration;
- structural obstacles;
- random seed.

### 4.2 Ground Truth Firewall

Maintain simulation-only ground truth including:

- true victim count;
- true victim coordinates;
- victim identity;
- scenario configuration;
- simulated vitality parameters;
- environment metadata.

Ground truth shall be available only to evaluation and testing. It shall not enter fusion, localization, tracking, AI inference, vitality, or rescue-decision logic.

### 4.3 Probe Model

Implement and maintain:

- probe initialization;
- probe state;
- current position;
- trajectory;
- visited cells;
- coverage state;
- waypoint information.

### 4.4 Search Path

Implement systematic mission coverage such as:

```text
Boustrophedon Search
```

and maintain:

- path history;
- visited cells;
- coverage percentage;
- next-waypoint logic;
- obstacle-aware movement where required.

### 4.5 Mission Controller

Maintain mission states including:

```text
INITIALIZING
MOVING
SENSING
PROCESSING
DECISION
UPDATE
COMPLETED
ERROR
```

### 4.6 Mission Events

Emit mission events required by:

- processing;
- backend integration;
- dashboard timeline;
- testing;
- evaluation.

## Expected Core Modules

Examples:

```text
createScenario
createProbe
missionController
runProbeSimulation
searchAlgorithms
updateCoverage
```

## Inputs

- scenario configuration;
- random seed;
- mission settings.

## Outputs

- `ScenarioContext`;
- environment state;
- probe state;
- mission state;
- simulation events;
- evaluation-only ground truth.

## Main Deliverables

- configurable scenario engine;
- deterministic scenario generation;
- mission controller;
- probe model;
- search-path implementation;
- coverage tracking;
- mission-state logging;
- scenario fixtures;
- simulation tests.

## Testing Responsibilities

- deterministic seed tests;
- scenario-generation tests;
- victim-placement tests;
- mission-state transition tests;
- path-coverage tests;
- ground-truth isolation tests.

## Parallel Development Strategy

This workstream can start immediately.

Workstream 2 consumes `ScenarioContext` fixtures and does not need to wait for the final simulation engine.

---

# 5. Workstream 2 — Sensors, Microcontroller, Acquisition, and Signal Processing

## Owner

**Team Member 2**

## Role Title

**Sensor, Embedded, and Acquisition Engineer**

## Mission

Own the complete system input path for both Simulation Mode and Hardware-in-the-Loop Mode, from sensor-source data or microcontroller packets through validated and normalized `SensorObservation` output.

The microcontroller is a **core project component**, not a future extension.

## Core Responsibilities

### 5.1 UWB Radar Processing

Implement or maintain processing related to:

- respiration evidence;
- heartbeat evidence;
- periodicity;
- signal strength;
- attenuation;
- burial-depth effects;
- distance falloff;
- weak vital-sign behavior.

### 5.2 Thermal Processing

Implement or maintain:

- thermal evidence;
- thermal contrast;
- environmental effects;
- occlusion behavior;
- normalization.

### 5.3 Acoustic Processing

Implement or maintain:

- acoustic filtering;
- envelope extraction;
- human-related sound evidence;
- periodicity;
- environmental noise handling.

### 5.4 Simulation Sensor Path

Using `ScenarioContext` and probe state, produce controlled simulated sensing observations that represent:

- normal operation;
- noise;
- attenuation;
- occlusion;
- weak vital signs;
- distance falloff;
- sensor degradation;
- sensor dropout.

### 5.5 Signal Preprocessing

Own:

- filtering;
- normalization;
- baseline removal where required;
- feature extraction;
- validity checks;
- missing-value handling at acquisition level;
- initial sensor-availability handling.

### 5.6 Microcontroller Firmware / Acquisition

Own the microcontroller integration layer including:

- sensor or emulated-sensor acquisition;
- sampling;
- timestamp handling;
- packet construction;
- sequence numbering;
- device-state reporting;
- communication status.

### 5.7 Communication Protocol

Implement and maintain:

- Serial / USB Serial communication;
- documented packet format;
- packet parsing;
- packet validation;
- connection monitoring;
- packet-loss detection;
- delayed/duplicate packet handling;
- malformed-packet handling;
- reconnect behavior.

### 5.8 Common Observation Adapter

Convert both acquisition paths into the same shared contract:

```text
Simulation Input ─┐
                  ├─→ SensorObservation
HardwarePacket ───┘
```

No downstream module shall require a separate implementation for simulation and hardware data.

### 5.9 Hardware Status Telemetry

Expose:

- connection state;
- device ID;
- port where applicable;
- packet rate;
- packet-loss rate;
- sensor availability;
- device diagnostics where available.

## Expected Core Modules

Examples:

```text
signalProcessingManager
sensorObservationAdapter
embedded/firmware
embedded/protocol
embedded/acquisition
hardwareStatusManager
```

## Inputs

### Simulation Mode

```text
ScenarioContext + Probe State
```

### Hardware-in-the-Loop Mode

```text
Physical / Emulated Sensors
        ↓
Microcontroller
        ↓
HardwarePacket
```

## Outputs

- `HardwarePacket`;
- `SensorObservation`;
- `HardwareStatus`;
- sensor diagnostics.

## Main Deliverables

- UWB processing path;
- thermal processing path;
- acoustic processing path;
- microcontroller firmware/acquisition path;
- communication protocol;
- packet schema and validator;
- HIL adapter;
- common `SensorObservation` adapter;
- hardware-status telemetry;
- sensor/HIL fixtures.

## Testing Responsibilities

- radar-processing tests;
- thermal-processing tests;
- acoustic-processing tests;
- sensor-degradation tests;
- normalization tests;
- serial packet tests;
- malformed-packet tests;
- packet-loss tests;
- delayed/duplicate packet tests;
- disconnect/reconnect tests;
- HIL smoke tests.

## Parallel Development Strategy

This workstream starts using:

- `ScenarioContext` fixtures;
- simulated sensor inputs;
- protocol fixtures;
- emulated serial packets.

Physical sensors do not need to be available on day one for implementation to begin, but the microcontroller/HIL path remains a required final deliverable.

---

# 6. Workstream 3 — HAIF Fusion and Localization

## Owner

**Team Member 3**

## Role Title

**Multi-Sensor Fusion and Localization Engineer**

## Mission

Own the reliability-aware fusion pipeline and the conversion of fused evidence into spatial victim-location estimates.

This workstream contains two tightly connected scientific responsibilities:

1. **HAIF / adaptive multi-sensor fusion**
2. **Evidence-based localization**

## Core Responsibilities

### 6.1 Sensor Health

Estimate sensor operational condition independently from current measurement quality.

### 6.2 Measurement Quality

Estimate observation-specific quality.

The system shall preserve the distinction:

```text
Healthy Sensor + Poor Measurement
```

### 6.3 Robust Statistical Processing

Maintain approved methods such as:

- median;
- MAD;
- robust scaling;
- Cauchy weighting where applicable.

### 6.4 Sensor Agreement

Estimate agreement among:

- radar;
- thermal;
- acoustic evidence.

### 6.5 Adaptive Weighting

Calculate dynamic modality weights.

### 6.6 HAIF

Own the HAIF implementation and maintenance including concepts such as:

- availability;
- health;
- quality;
- quality memory;
- conflict;
- innovation;
- effective sensor support;
- one-sided anomaly handling;
- quality-gated conflict;
- adaptive modality influence.

### 6.7 Fusion Score and Confidence

Generate:

- fusion score;
- confidence;
- modality weights;
- reliability diagnostics;
- sensor agreement;
- effective support.

### 6.8 Fusion Explainability

Expose:

- weights;
- quality;
- health;
- agreement;
- conflict;
- innovation;
- effective support;
- approved diagnostics.

### 6.9 Evidence Map

Maintain spatial evidence accumulation using approved evidence-map logic.

### 6.10 Spatial Update and Decay

Implement:

- Gaussian spatial update;
- evidence decay where required;
- reliability masking;
- evidence-peak search.

### 6.11 Localization

Estimate victim coordinates using the approved localization method, including weighted-centroid logic where applicable.

### 6.12 Localization Confidence and Acceptance

Produce:

- localization confidence;
- evidence peak;
- accepted/rejected status;
- explicit rejection reason when evidence is insufficient or unstable.

## Expected Core Modules

Examples:

```text
calculateAdaptiveWeights
adaptiveFusion
confidenceEstimation
HAIF
updateEvidenceMap
localizationManager
readHAIFLocalizationQualitySupport
```

## Inputs

```text
SensorObservation
Probe Position / Observation Context
```

## Outputs

- `FusionOutput`;
- `LocalizationOutput`;
- fusion diagnostics;
- localization diagnostics.

## Main Deliverables

- baseline fusion;
- robust fusion baseline;
- HAIF;
- confidence estimation;
- reliability diagnostics;
- Evidence Map;
- localization pipeline;
- localization acceptance policy;
- robustness experiments;
- fusion/localization telemetry.

## Testing Responsibilities

### Fusion / HAIF

- clean-condition tests;
- attenuation tests;
- sensor-dropout tests;
- degraded-quality tests;
- one-sided anomaly tests;
- weight-response tests;
- regression tests;
- baseline-comparison tests.

### Localization

- synthetic-location tests;
- spatial-evidence tests;
- localization-stability tests;
- accepted/rejected localization tests;
- multiple-peak tests;
- regression tests.

## Research Responsibility

This owner is the primary technical maintainer of the HAIF and quality-aware localization research contribution.

Experimental review shall involve at least one additional team member. Protected calibration, development, fresh-validation, and locked evaluation rules must be respected.

## Parallel Development Strategy

This workstream begins using:

```text
interfaces/fixtures/sensor_observation.json
```

without waiting for Workstream 2 to finish.

---

# 7. Workstream 4 — Victim Tracking, AI Intelligence, Vitality, and Rescue Decision

## Owner

**Team Member 4**

## Role Title

**Victim Intelligence and AI Decision Engineer**

## Mission

Own the persistent victim-intelligence layer after localization: candidate/track management, AI candidate validation, uncertainty-aware decisions, vitality estimation, and rescue prioritization.

The AI layer is a decision-support layer **above** the deterministic sensing/fusion/localization pipeline. It does not replace HAIF or localization.

## Core Responsibilities

### 7.1 Victim Candidate Creation

Create operational victim candidates from accepted localization evidence.

### 7.2 Candidate Association and Merge

Implement:

- association to existing tracks;
- duplicate handling;
- track merge logic;
- new-track creation.

### 7.3 Victim Track Management

Maintain:

- victim/track ID;
- estimated location;
- localization confidence;
- detection count;
- independent views;
- temporal stability;
- location history;
- existence probability;
- status.

### 7.4 Candidate Feature Record

Assemble the approved AI candidate feature schema from upstream scientific features while enforcing the no-oracle policy.

### 7.5 Candidate Classification

Train and integrate classification for:

```text
TRUE_TRACK
HARD_NEGATIVE
```

while excluding:

```text
AMBIGUOUS_IGNORE
```

from the primary supervised training target.

### 7.6 AI Data Integrity

Verify:

- split integrity;
- missingness;
- leaked columns;
- oracle leakage;
- duplicate-mission contamination;
- feature-version consistency.

### 7.7 Model Training

Implement reproducible training and model-version management.

### 7.8 Probability Calibration

Calibrate classifier probabilities using the approved development protocol.

### 7.9 Uncertainty Estimation

Estimate model uncertainty.

### 7.10 Abstention

Implement:

```text
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
```

### 7.11 Explainability and Auditability

Record and expose:

- model version;
- feature version;
- probability;
- uncertainty;
- decision;
- abstention;
- threshold/version metadata;
- supporting factors;
- risk factors;
- timestamp.

### 7.12 Vitality Index

Calculate the approved Vitality Index using operational evidence.

### 7.13 Rescue Priority

Generate rescue priority for confirmed/evaluated victims.

### 7.14 Multi-Victim Ranking

Maintain an ordered rescue recommendation and associated metadata.

## Expected Core Modules

Examples:

```text
updateVictimDatabase
trackAssociation
candidateFeatureBuilder
ai/training
ai/calibration
ai/uncertainty
ai/inference
vitalityManager
decisionEngine
priorityRanking
```

## Inputs

- `LocalizationOutput`;
- approved fusion/reliability features;
- observation history;
- candidate history.

## Outputs

- `VictimTrack`;
- `CandidateFeatureRecord`;
- `AIOutput`;
- `RescueDecision`;
- vitality and ranking metadata.

## Main Deliverables

- candidate association;
- persistent tracking;
- candidate dataset pipeline;
- AI training pipeline;
- trained model artifact;
- calibration;
- uncertainty;
- abstention;
- explainability;
- AI evaluation;
- vitality engine;
- rescue-priority logic;
- multi-victim ranking.

## Testing Responsibilities

### Tracking

- candidate-association tests;
- duplicate-merge tests;
- temporal-stability tests;
- multi-victim tests;
- lost/reacquired track tests where applicable.

### AI

- feature-schema tests;
- leakage tests;
- split tests;
- reproducibility tests;
- calibration tests;
- inference tests;
- uncertainty tests;
- abstention tests;
- sealed-test governance checks.

### Decision

- vitality tests;
- rescue-ranking tests;
- priority-consistency tests;
- uncertain/abstained candidate handling.

## Parallel Development Strategy

This workstream starts using:

```text
interfaces/fixtures/localization_output.json
interfaces/fixtures/victim_track.json
interfaces/fixtures/candidate_features.json
```

without waiting for Workstream 3 to finish.

---

# 8. Workstream 5 — Backend, Operational Dashboard, and System Integration

## Owner

**Team Member 5**

## Role Title

**Full-Stack Platform and Integration Engineer**

## Mission

Own the product-facing software layer that integrates MATLAB, Python AI outputs, hardware status, mission telemetry, REST/API services, and the operator dashboard.

This workstream does **not** reimplement scientific algorithms. It exposes validated outputs through stable APIs and user interfaces.

## Part A — Backend and Integration Responsibilities

### 8.1 Backend Service

Own the backend application and service boundaries, including where applicable:

- FastAPI application;
- configuration;
- API routing;
- CORS;
- structured error handling;
- telemetry ingestion/consumption;
- mission-state assembly;
- result/evaluation release rules;
- logging.

### 8.2 MATLAB Integration

Integrate the MATLAB mission pipeline with the backend through the approved telemetry/result contracts.

### 8.3 Python AI Integration

Expose Workstream 4 AI outputs through the operational state without duplicating the AI model internally in the frontend.

### 8.4 Hardware Status Integration

Consume `HardwareStatus` from Workstream 2 and expose it to the dashboard.

### 8.5 MissionState Assembly

Build the unified dashboard-facing `MissionState` from approved operational contracts.

### 8.6 API Contract

Maintain versioned API responses and controlled failure behavior.

## Part B — Dashboard Responsibilities

### 8.7 Frontend Stack

Own implementation using:

```text
React
TypeScript
Vite
```

### 8.8 Mission Status

Display:

- mission ID;
- operating mode;
- mission status;
- elapsed time;
- coverage;
- system reliability;
- AI state;
- victim count.

### 8.9 Operational Map

Display:

- structure/environment;
- probe;
- path;
- coverage;
- evidence visualization where approved;
- victim locations;
- selected victim;
- rescue route/recommendation metadata.

### 8.10 Victim Intelligence

Display:

- victim/track identity;
- estimated location;
- localization confidence;
- AI probability;
- uncertainty;
- AI decision/abstention;
- vitality;
- rescue priority.

### 8.11 Fusion Intelligence

Visualize approved Workstream 3 data:

- fusion score;
- confidence;
- modality weights;
- health/quality;
- effective support;
- agreement/conflict diagnostics where approved.

### 8.12 Sensor and Hardware Status

Display:

- radar/thermal/acoustic status;
- sensor health/quality where available;
- microcontroller connection state;
- packet rate;
- packet-loss rate;
- hardware warnings.

### 8.13 AI Assessment

Display:

- classification;
- probability;
- uncertainty;
- abstention;
- supporting factors;
- risk factors;
- model version where appropriate.

### 8.14 Timeline and Evaluation View

Display mission events and post-mission evaluation without leaking evaluation-only ground truth during an active mission.

## Expected Core Modules

Examples:

```text
backend/app/main.py
backend/app/api/*
backend/app/telemetry/*
backend/app/integration/*
dashboard/src/*
MissionStatusStrip
OperationalMap
VictimList
DecisionWorkspace
DecisionSummary
RecommendationCard
AIExplanation
Timeline
VictimIntelligence
FusionIntelligence
SensorReadings
HardwareStatus
EvaluationView
```

## Inputs

- `HardwareStatus`;
- `FusionOutput`;
- `LocalizationOutput`;
- `VictimTrack`;
- `AIOutput`;
- `RescueDecision`;
- `MissionEvent`;
- mission telemetry/results.

## Outputs

- `MissionState`;
- versioned REST/API responses;
- operator-facing visualization and interactions;
- integration logs.

## Main Deliverables

- backend service;
- telemetry bridge;
- integration adapters;
- unified mission state;
- operational API;
- complete dashboard;
- dashboard integration;
- error/loading states;
- hardware/HIL visualization;
- backend tests;
- frontend tests;
- integration tests.

## Testing Responsibilities

### Backend / Integration

- API tests;
- telemetry parsing tests;
- schema validation;
- ground-truth safety-boundary tests;
- mission-state assembly tests;
- MATLAB/backend integration tests;
- AI/backend integration tests;
- HIL/backend integration tests;
- controlled-error tests.

### Dashboard

- component tests;
- payload parsing;
- victim selection;
- map synchronization;
- error-state rendering;
- HIL status rendering;
- AI state rendering;
- mission lifecycle rendering.

## Parallel Development Strategy

This workstream starts using:

```text
interfaces/fixtures/mission_state.json
interfaces/fixtures/hardware_status.json
interfaces/fixtures/ai_output.json
interfaces/fixtures/rescue_decision.json
```

without waiting for live MATLAB, AI, or hardware integration.

---

# 9. Cross-Workstream AI Feature Ownership

AI feature generation is a shared scientific responsibility, but the AI schema and model pipeline are owned by Workstream 4.

| Feature Family | Primary Owner |
|---|---|
| Sensor signal features | Workstream 2 |
| Sensor availability | Workstream 2 |
| Sensor health / measurement quality | Workstream 3 |
| Fusion weights / confidence | Workstream 3 |
| Agreement / conflict / effective support | Workstream 3 |
| Localization confidence / spatial stability | Workstream 3 |
| Candidate temporal features | Workstream 4 |
| Candidate dataset assembly | Workstream 4 |
| Model preprocessing | Workstream 4 |
| Calibration / uncertainty / abstention | Workstream 4 |
| Operational API exposure | Workstream 5 |

This prevents duplicate scientific logic and improves traceability.

---

# 10. Cross-Workstream Integration Responsibilities

Integration is a team responsibility.

No workstream may state:

> "My module works, so my work is complete."

A module is complete only when it:

1. works locally;
2. respects its approved contract;
3. passes required tests;
4. integrates with at least the approved fixture/consumer path;
5. does not break protected regression behavior.

Workstream 5 owns the platform integration implementation, while every workstream remains responsible for making its module integrable.

---

# 11. Reviewer Matrix

Recommended primary reviewer rotation:

| Owner | Primary Reviewer |
|---|---|
| Workstream 1 | Workstream 2 |
| Workstream 2 | Workstream 3 |
| Workstream 3 | Workstream 4 |
| Workstream 4 | Workstream 5 |
| Workstream 5 | Workstream 1 |

A second reviewer may be requested for high-risk research, hardware, AI, or architectural changes.

---

# 12. Technical Lead / Delivery Coordinator

One team member may additionally act as:

```text
Technical Lead / Delivery Coordinator
```

Responsibilities:

- architecture consistency;
- sprint-goal alignment;
- dependency resolution;
- integration planning;
- interface-change control;
- risk escalation;
- ensuring required tests are run;
- coordinating integrated builds;
- coordinating Sprint Review evidence.

The Technical Lead shall not be the sole decision-maker for major technical changes.

---

# 13. Parallel Development Model

All five members begin in parallel.

### Workstream 1
Uses the real or developing simulation engine.

### Workstream 2
Uses `ScenarioContext` plus simulated conditions and protocol fixtures.

### Workstream 3
Uses mock `SensorObservation`.

### Workstream 4
Uses mock `LocalizationOutput`, `VictimTrack`, and candidate fixtures.

### Workstream 5
Uses mock `MissionState`, `AIOutput`, `HardwareStatus`, and rescue-decision fixtures.

Therefore:

```text
No team member waits for another member to finish the full module.
```

---

# 14. Shared Fixtures

The repository shall contain:

```text
interfaces/fixtures/
```

with representative test data.

Minimum initial fixtures:

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

Every fixture shall conform to the same schema used by production modules.

---

# 15. Shared Interface Contracts

Major shared contracts:

```text
ScenarioContext
HardwarePacket
SensorObservation
FusionOutput
LocalizationOutput
VictimTrack
CandidateFeatureRecord
AIOutput
RescueDecision
HardwareStatus
MissionState
MissionEvent
```

Detailed field definitions are maintained in:

```text
05_Interface_Contracts.md
```

---

# 16. Definition of Done — All Workstreams

A work item is Done only when applicable criteria are satisfied:

1. implementation completed;
2. local tests pass;
3. interface contract respected;
4. code committed;
5. pull request created;
6. peer review completed;
7. integration tests pass where applicable;
8. protected regression remains green;
9. documentation updated;
10. Jira issue updated;
11. acceptance criteria demonstrated.

---

# 17. Git Workflow

Normal feature work shall not be performed directly on `main`.

Recommended flow:

```text
main
  ↑
develop
  ↑
feature/*
```

Examples:

```text
feature/scenario-engine
feature/microcontroller-protocol
feature/sensor-observation-adapter
feature/haif-fusion
feature/localization
feature/victim-tracking
feature/ai-calibration
feature/backend-telemetry
feature/dashboard-map
```

Typical workflow:

```text
Jira Issue
    ↓
Feature Branch
    ↓
Implementation
    ↓
Tests
    ↓
Pull Request
    ↓
Peer Review
    ↓
Integration
```

---

# 18. Commit Convention

Examples:

```text
feat: add radar attenuation model
feat: implement microcontroller packet validator
feat: implement candidate abstention
fix: correct localization centroid calculation
test: add sensor dropout regression
docs: update sensor observation contract
refactor: isolate acquisition adapter
```

Commits should describe meaningful changes.

---

# 19. Jira Ownership

Each implementation issue shall contain at least:

- Summary;
- Description;
- Requirement references;
- Assignee;
- Reviewer;
- Priority;
- Acceptance Criteria;
- Dependencies;
- Sprint;
- Definition of Done.

Example:

```text
Story: Implement Hardware Packet Validation
Owner: Workstream 2
Reviewer: Workstream 3
Requirements: FR-019, FR-020
Acceptance:
- malformed packets rejected
- missing sequence detected
- valid packets converted to SensorObservation
- tests pass
```

---

# 20. Recommended Jira Epics

```text
EPIC 1  — Project Foundation
EPIC 2  — Simulation and Mission Engine
EPIC 3  — Sensors, Microcontroller, and Acquisition
EPIC 4  — HAIF and Adaptive Fusion
EPIC 5  — Localization
EPIC 6  — Victim Tracking and AI Intelligence
EPIC 7  — Vitality and Rescue Decision
EPIC 8  — Backend and Operational Dashboard
EPIC 9  — System Integration
EPIC 10 — Testing and Validation
EPIC 11 — Final Demonstration and Documentation
```

---

# 21. Sprint Structure

Recommended sprint duration:

```text
1–2 weeks
```

The project shall use a short **Sprint 0** before feature implementation.

Sprint 0 focuses on:

- repository readiness;
- contract freeze;
- fixtures;
- toolchain verification;
- basic workstream skeletons;
- first interface smoke tests.

Every later sprint shall have one shared system-level goal.

Preferred Sprint Goal example:

> The integrated system can propagate one valid observation through acquisition, fusion, and localization using the approved contracts.

---

# 22. Integration Cadence

At least one integration checkpoint shall occur during each sprint.

Recommended cadence:

```text
Development
    ↓
Mid-Sprint Integration Check
    ↓
Development / Fixes
    ↓
End-of-Sprint Integrated Build
```

Integration issues are project work, not one member's private problem.

---

# 23. Suggested Sprint 0 Allocation

## Workstream 1

- scenario configuration skeleton;
- mission skeleton;
- probe fixture;
- `ScenarioContext` fixture.

## Workstream 2

- sensor input schemas;
- `HardwarePacket` v1.0 draft;
- microcontroller protocol draft;
- radar/thermal/acoustic processing entry points;
- acquisition mock;
- `SensorObservation` fixture.

## Workstream 3

- `SensorObservation` consumer;
- fusion entry point;
- HAIF regression entry point;
- localization entry point;
- `FusionOutput` and `LocalizationOutput` fixtures.

## Workstream 4

- victim-track structure;
- candidate-feature loader/schema;
- AI project skeleton;
- vitality/decision entry points;
- `VictimTrack`, `CandidateFeatureRecord`, `AIOutput`, and `RescueDecision` fixtures.

## Workstream 5

- backend project skeleton;
- dashboard project skeleton;
- `MissionState` mock renderer;
- integration directory structure;
- basic API/telemetry smoke path;
- CI/repository integration support.

## Sprint 0 Shared Goal

```text
All five workstreams can execute independently against agreed v1.0
contracts and fixtures, and the team can demonstrate at least one
producer-consumer interface without unfinished upstream code.
```

---

# 24. Required End-to-End Product Paths

The project is not complete unless both paths are represented.

## 24.1 Simulation Path

```text
Scenario / Probe
    ↓
Simulated Sensors
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
Vitality / Rescue Decision
    ↓
Backend
    ↓
Dashboard
```

## 24.2 Hardware-in-the-Loop Path

```text
Physical / Emulated Sensors
    ↓
Microcontroller
    ↓
HardwarePacket
    ↓
Acquisition / Validation
    ↓
SensorObservation
    ↓
Same Downstream Pipeline
    ↓
Backend
    ↓
Dashboard
```

The system shall converge at `SensorObservation` wherever practical.

---

# 25. Workstream Dependency Matrix

| Workstream | Main Upstream Dependency | Can Start Without Final Upstream? |
|---|---|---|
| WS1 Simulation / Mission | None | Yes |
| WS2 Sensors / Microcontroller | ScenarioContext | Yes, with fixture |
| WS3 HAIF / Localization | SensorObservation | Yes, with fixture |
| WS4 Tracking / AI / Decision | LocalizationOutput | Yes, with fixture |
| WS5 Backend / Dashboard / Integration | Mission-facing outputs | Yes, with fixtures |

Therefore all five workstreams can start in parallel.

---

# 26. Workload Rebalancing Rules

During Sprint Planning, review:

- open issue count;
- story points / estimated effort;
- technical risk;
- blocked tasks;
- testing burden;
- bug count;
- integration burden;
- hardware availability;
- research/evaluation workload.

If one workstream becomes overloaded:

- secondary tests may be shared;
- dashboard visual components may be delegated temporarily;
- experiment automation may be shared;
- documentation support may be redistributed;
- integration debugging may be paired.

Core scientific ownership should remain stable unless there is a clear reason to change it.

---

# 27. Team Communication

## Short Daily Check-In

Each member answers:

1. What did I complete?
2. What am I doing next?
3. What is blocking me?
4. Did I change or request a change to any shared interface?

## Sprint Planning

Define:

- Sprint Goal;
- Stories;
- Owners;
- Reviewers;
- story points / effort;
- dependencies;
- risks.

## Mid-Sprint Integration Review

Review:

- interface mismatches;
- failed tests;
- integration blockers;
- schema changes;
- hardware blockers.

## Sprint Review

Demonstrate the integrated increment.

## Retrospective

Discuss:

- what worked;
- what did not;
- what should change next sprint.

---

# 28. Interface Change Policy

Shared interfaces shall not be changed silently.

Any breaking change to:

```text
HardwarePacket
SensorObservation
FusionOutput
LocalizationOutput
VictimTrack
CandidateFeatureRecord
AIOutput
RescueDecision
HardwareStatus
MissionState
```

must include:

1. Jira issue;
2. proposed change;
3. rationale;
4. affected workstreams;
5. reviewer approval;
6. schema-version update;
7. updated fixtures;
8. updated tests;
9. integration check.

---

# 29. Ownership Does Not Mean Isolation

An owner is accountable for ensuring a module succeeds.

Ownership does not mean only that person may understand or edit the module.

At least one reviewer shall understand each critical module to reduce single-person dependency and improve maintainability.

---

# 30. Final Ownership Summary

## Team Member 1 — Simulation and Mission Systems Engineer

Owns:

- Simulation;
- Scenario Engine;
- Ground Truth Firewall;
- Probe;
- Search Path;
- Coverage;
- Mission Controller.

## Team Member 2 — Sensor, Embedded, and Acquisition Engineer

Owns:

- UWB;
- Thermal;
- Acoustic;
- simulated sensor path;
- Signal Processing;
- Microcontroller;
- Serial/USB Communication;
- Acquisition;
- HardwarePacket;
- SensorObservation adapter;
- HardwareStatus.

## Team Member 3 — Multi-Sensor Fusion and Localization Engineer

Owns:

- Sensor Health;
- Measurement Quality;
- HAIF;
- Adaptive Weights;
- Fusion;
- Confidence;
- Reliability Diagnostics;
- Evidence Map;
- Localization;
- Localization Acceptance.

## Team Member 4 — Victim Intelligence and AI Decision Engineer

Owns:

- Candidate Association;
- Victim Tracking;
- Candidate Dataset Schema;
- Candidate Classifier;
- Calibration;
- Uncertainty;
- Abstention;
- Explainability;
- AI Evaluation;
- Vitality;
- Rescue Priority;
- Multi-Victim Ranking.

## Team Member 5 — Full-Stack Platform and Integration Engineer

Owns:

- FastAPI / backend service;
- Telemetry Integration;
- API Contracts;
- MissionState;
- MATLAB/Python/HIL platform integration;
- React / TypeScript / Vite dashboard;
- Operational Map;
- Mission/Victim/AI/Fusion/Hardware views;
- system integration tests.

---

# 31. Approval Criteria for Team Division

This workstream division is approved when:

1. each member has one clear primary technical ownership area;
2. all five members can begin in parallel using fixtures;
3. the microcontroller/HIL path is treated as core scope;
4. simulation and HIL converge on a common observation interface;
5. HAIF and localization remain a coherent scientific workstream;
6. AI is separate from the dashboard and remains above the deterministic pipeline;
7. backend/dashboard/integration have a dedicated owner;
8. workload is reviewed every sprint;
9. peer review exists across workstreams;
10. end-to-end integration remains a shared team responsibility.
