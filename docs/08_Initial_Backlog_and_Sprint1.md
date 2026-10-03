# USAR Intelligent Rescue System
## Initial Backlog and Sprint 1 Plan

**Document:** 08_Initial_Backlog_and_Sprint1.md  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Delivery Model:** Hybrid Agile  
**Purpose:** Convert approved project documents into executable Jira work and define Sprint 1  

---

# 1. Purpose

This document defines the initial implementation backlog and the first development sprint for the USAR Intelligent Rescue System.

It is the final required planning document before implementation begins.

This document translates:

- Project Charter;
- Requirements;
- System Architecture;
- Team Workstreams;
- Interface Contracts;
- Test Strategy;
- Risk Register

into:

- Jira Epics;
- Stories;
- Tasks;
- Sprint 1 goals;
- ownership;
- dependencies;
- acceptance criteria;
- and integration checkpoints.

---

# 2. Delivery Approach

The project will use a Hybrid Agile model.

The team will combine:

- stable project scope;
- explicit architecture;
- controlled interface contracts;
- short implementation sprints;
- continuous integration;
- peer review;
- regression testing;
- and milestone-based delivery.

Recommended sprint duration:

```text
1–2 weeks
```

For the initial team setup, a 1-week Sprint 1 is recommended because its purpose is to validate parallel development and interfaces rather than deliver the complete system.

---

# 3. Initial Jira Epics

Create the following Epics in Jira.

## EPIC-01 — Project Foundation

Purpose:

Maintain approved project-definition documents and governance.

Includes:

- Charter;
- Requirements;
- Architecture;
- Workstreams;
- Interfaces;
- Testing;
- Risks.

Status at Sprint 1 start:

```text
Mostly Complete
```

---

## EPIC-02 — Simulation and Mission Engine

Owner:

```text
Workstream 1
```

Scope:

- scenario generation;
- probe initialization;
- mission state machine;
- search path;
- coverage;
- scenario fixtures;
- ground-truth isolation.

---

## EPIC-03 — Sensor and Embedded Systems

Owner:

```text
Workstream 2
```

Scope:

- radar;
- thermal;
- acoustic;
- signal preprocessing;
- microcontroller;
- communication protocol;
- acquisition adapter;
- packet validation;
- HIL.

---

## EPIC-04 — HAIF and Adaptive Fusion

Owner:

```text
Workstream 3
```

Scope:

- sensor health;
- measurement quality;
- robust statistics;
- HAIF;
- adaptive weights;
- confidence;
- reliability diagnostics.

---

## EPIC-05 — Localization and Victim Tracking

Owner:

```text
Workstream 4
```

Scope:

- evidence map;
- localization;
- localization confidence;
- victim candidate creation;
- association;
- track management.

---

## EPIC-06 — AI Decision Intelligence

Owner:

```text
Workstream 5
```

Scope:

- candidate classification;
- training pipeline;
- calibration;
- uncertainty;
- abstention;
- explainability;
- AI auditability.

---

## EPIC-07 — Vitality and Rescue Decision

Primary Owner:

```text
Workstream 4
```

Scope:

- Vitality Index;
- rescue priority;
- multi-victim ranking;
- operator-facing recommendation metadata.

---

## EPIC-08 — Operational Dashboard

Primary Owner:

```text
Workstream 5
```

Scope:

- operational map;
- mission status;
- victims;
- AI outputs;
- sensor health;
- fusion intelligence;
- localization;
- vitality;
- hardware status;
- timeline;
- evaluation view.

---

## EPIC-09 — System Integration

Owner:

```text
Entire Team
```

Coordination:

```text
Technical Lead
```

Scope:

- module adapters;
- contract validation;
- MATLAB/Python integration;
- HIL integration;
- unified MissionState;
- end-to-end pipeline.

---

## EPIC-10 — Testing and Validation

Owner:

```text
Entire Team
```

Scope:

- unit tests;
- interface tests;
- regression;
- AI validation;
- HIL tests;
- end-to-end tests;
- acceptance tests.

---

## EPIC-11 — Final Demonstration and Documentation

Owner:

```text
Entire Team
```

Scope:

- final demo;
- reproducible run configuration;
- fallback demo;
- project report;
- presentation assets.

---

# 4. Backlog Priority Policy

Every backlog item shall receive one of:

```text
MUST
SHOULD
COULD
WON'T FOR CURRENT RELEASE
```

Sprint 1 shall include MUST items only.

---

# 5. Sprint 1 Goal

## Shared Sprint Goal

```text
All five workstreams can execute independently against agreed v1.0
interfaces, and the team can demonstrate one controlled data flow
across at least two adjacent modules without relying on unfinished code.
```

Sprint 1 is not intended to complete major algorithms.

Its purpose is to prove:

- repository structure;
- team workflow;
- interfaces;
- mocks;
- basic module skeletons;
- test execution;
- pull-request flow;
- and first integration.

---

# 6. Sprint 1 Entry Criteria

Sprint 1 shall begin only when:

- `01_Project_Charter.md` is approved;
- `02_Requirements.md` is approved;
- `03_System_Architecture.md` is approved;
- `04_Team_Workstreams.md` is approved;
- `05_Interface_Contracts.md` is approved;
- `06_Test_Strategy.md` is approved;
- `07_Risk_Register.md` is approved;
- the five team members are identified;
- repository access is confirmed;
- Jira access is confirmed;
- core interfaces are frozen as v1.0 for Sprint 1.

---

# 7. Sprint 1 Workstream 1 Backlog

## Story S1-WS1-01 — Create Scenario Configuration Model

**Epic:** Simulation and Mission Engine  
**Owner:** Workstream 1  
**Reviewer:** Workstream 2  
**Priority:** MUST  

### Requirements

Related:

```text
FR-001
FR-002
FR-003
FR-004
```

### Tasks

- define scenario configuration structure;
- include scenario type;
- include random seed;
- include debris;
- include noise;
- include burial depth;
- include victim count;
- include vital strength;
- generate one valid `ScenarioContext`;
- create one fixture.

### Acceptance Criteria

- scenario can be created from configuration;
- same seed reproduces same configuration;
- `ScenarioContext` matches interface v1.0;
- ground truth is stored separately;
- unit tests pass.

---

## Story S1-WS1-02 — Create Mission Controller Skeleton

**Epic:** Simulation and Mission Engine  
**Owner:** Workstream 1  
**Priority:** MUST  

### Tasks

Create initial states:

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

Implement valid transitions.

### Acceptance Criteria

- controller initializes;
- valid transition sequence runs;
- invalid state transition is handled;
- state transitions are logged.

---

## Story S1-WS1-03 — Create Probe and Path Fixture

### Tasks

- create initial probe position;
- generate small path fixture;
- track visited cells;
- expose probe state for downstream tests.

### Acceptance Criteria

- probe fixture is reproducible;
- path can be consumed by Workstream 2.

---

# 8. Sprint 1 Workstream 2 Backlog

## Story S1-WS2-01 — Define Hardware Packet v1.0

**Epic:** Sensor and Embedded Systems  
**Owner:** Workstream 2  
**Reviewer:** Workstream 3  
**Priority:** MUST  

### Requirements

```text
FR-016
FR-017
FR-018
FR-019
FR-020
```

### Tasks

- implement `HardwarePacket`;
- include sequence number;
- include timestamp;
- include three sensor values;
- include availability flags;
- create valid fixture;
- create invalid fixture.

### Acceptance Criteria

- valid packet parses;
- malformed packet is rejected;
- missing sequence is detectable;
- schema matches `05_Interface_Contracts.md`.

---

## Story S1-WS2-02 — Create SensorObservation Adapter

### Tasks

Convert:

```text
HardwarePacket
```

or simulation input into:

```text
SensorObservation
```

### Acceptance Criteria

- both source types produce compatible output;
- `sourceMode` is correct;
- all required fields exist;
- range validation is implemented.

---

## Story S1-WS2-03 — Sensor Processing Skeleton

### Tasks

Create initial processing stubs/interfaces for:

- radar;
- thermal;
- acoustic.

These do not need full scientific behavior in Sprint 1.

### Acceptance Criteria

- each module receives controlled input;
- each returns normalized output;
- each has at least one unit test.

---

# 9. Sprint 1 Workstream 3 Backlog

## Story S1-WS3-01 — Implement Fusion Consumer for SensorObservation

**Epic:** HAIF and Adaptive Fusion  
**Owner:** Workstream 3  
**Reviewer:** Workstream 4  
**Priority:** MUST  

### Tasks

- parse `SensorObservation`;
- validate availability;
- validate health;
- validate quality;
- create fusion processing entry point.

### Acceptance Criteria

- valid fixture is accepted;
- invalid fixture is rejected;
- unavailable sensor is identifiable;
- module can run independently.

---

## Story S1-WS3-02 — Baseline Fusion Skeleton

### Tasks

- implement simple baseline weighted fusion;
- calculate provisional fusion score;
- calculate initial confidence;
- produce `FusionOutput`.

### Acceptance Criteria

- output matches contract v1.0;
- active weights sum approximately to 1;
- unavailable sensors receive zero operational weight;
- tests pass.

---

## Story S1-WS3-03 — HAIF Module Integration Point

### Tasks

Create stable entry point for existing HAIF implementation.

### Acceptance Criteria

- HAIF can accept standardized `SensorObservation`;
- output can be mapped to `FusionOutput`;
- existing internal algorithm is not duplicated;
- regression entry point is documented.

---

# 10. Sprint 1 Workstream 4 Backlog

## Story S1-WS4-01 — Create Evidence Map Skeleton

**Epic:** Localization and Victim Tracking  
**Owner:** Workstream 4  
**Reviewer:** Workstream 5  
**Priority:** MUST  

### Tasks

- initialize map;
- accept probe position;
- accept fusion score;
- apply first spatial evidence update;
- expose map state.

### Acceptance Criteria

- mock `FusionOutput` can update evidence map;
- output is deterministic for fixed input;
- basic unit test passes.

---

## Story S1-WS4-02 — Localization Interface v1.0

### Tasks

- consume `FusionOutput`;
- expose `LocalizationOutput`;
- support accepted and rejected result;
- create test fixture.

### Acceptance Criteria

- schema matches contract;
- rejected state supports null position;
- localization confidence is in [0,1].

---

## Story S1-WS4-03 — VictimTrack Skeleton

### Tasks

Create initial track structure with:

- ID;
- location;
- localization confidence;
- detection count;
- independent views;
- existence probability;
- temporal stability;
- status.

### Acceptance Criteria

- fixture validates;
- AI workstream can consume it.

---

# 11. Sprint 1 Workstream 5 Backlog

## Story S1-WS5-01 — Initialize AI Project Structure

**Epic:** AI Decision Intelligence  
**Owner:** Workstream 5  
**Reviewer:** Workstream 3 or 4  
**Priority:** MUST  

### Tasks

Create initial structure:

```text
ai/
├── data/
├── features/
├── training/
├── calibration/
├── uncertainty/
├── inference/
├── evaluation/
└── tests/
```

### Acceptance Criteria

- structure exists;
- basic environment setup documented;
- test command executes.

---

## Story S1-WS5-02 — CandidateFeatureRecord Loader

### Tasks

- consume candidate fixture;
- validate required metadata;
- reject forbidden oracle fields;
- expose feature vector.

### Acceptance Criteria

- valid fixture loads;
- forbidden field test fails correctly;
- feature schema version is checked.

---

## Story S1-WS5-03 — Initialize Dashboard Project

**Epic:** Operational Dashboard  

### Tasks

Initialize:

```text
React
TypeScript
Vite
```

Create page skeleton:

```text
MissionStatusStrip
OperationalMap
DecisionWorkspace
BottomIntelligenceTabs
```

### Acceptance Criteria

- dashboard launches locally;
- mock `MissionState` loads;
- mission ID appears;
- probe position appears;
- one victim fixture appears;
- no live backend is required.

---

## Story S1-WS5-04 — MissionState Fixture Renderer

### Tasks

- parse `MissionState`;
- render basic mission status;
- render sensor status;
- render AI state;
- render victim priority.

### Acceptance Criteria

- frontend uses contract only;
- frontend does not depend on MATLAB internal structures.

---

# 12. Sprint 1 Shared Integration Tasks

## Story S1-INT-01 — Create Interfaces Directory

**Epic:** System Integration  
**Owner:** Technical Lead + Team  
**Priority:** MUST  

Create:

```text
interfaces/
├── schemas/
├── fixtures/
└── examples/
```

---

## Story S1-INT-02 — Add Initial Fixtures

Create:

```text
scenario_context.json
hardware_packet.json
sensor_observation.json
fusion_output.json
localization_output.json
victim_track.json
candidate_features.json
ai_output.json
mission_state.json
```

### Acceptance Criteria

- all fixtures are valid;
- all five workstreams can consume their required fixtures.

---

## Story S1-INT-03 — First Cross-Module Integration

Target:

```text
SensorObservation
        ↓
Fusion
        ↓
FusionOutput
```

### Acceptance Criteria

- Workstream 2 fixture is consumed by Workstream 3;
- no manual field conversion is required;
- contract test passes.

---

## Story S1-INT-04 — First Dashboard Contract Integration

Target:

```text
MissionState Fixture
        ↓
Dashboard
```

### Acceptance Criteria

- dashboard renders mission state;
- schema mismatch produces controlled error;
- no hard-coded scientific output is required.

---

# 13. Sprint 1 Testing Tasks

## S1-TEST-01 — Interface Validation Smoke Tests

Validate:

```text
HardwarePacket
SensorObservation
FusionOutput
LocalizationOutput
VictimTrack
CandidateFeatureRecord
MissionState
```

---

## S1-TEST-02 — Regression Smoke Entry Point

Ensure the existing core regression suite can still be launched from the reorganized repository or documented location.

Sprint 1 does not require migration of every legacy test.

It requires preserving access to validated regression behavior.

---

## S1-TEST-03 — AI Leakage Guard

Create a first automated test that rejects candidate feature records containing forbidden oracle fields.

---

# 14. Sprint 1 Repository Tasks

## S1-REPO-01 — Create Development Branch

Recommended branches:

```text
main
develop
```

---

## S1-REPO-02 — Protect Main

Where available:

- require pull request;
- require review;
- prevent accidental direct feature work.

---

## S1-REPO-03 — Create Workstream Branches

Examples:

```text
feature/scenario-foundation
feature/sensor-acquisition
feature/fusion-interface
feature/localization-interface
feature/ai-dashboard-foundation
```

---

# 15. Sprint 1 Jira Setup

For every Sprint 1 Story, Jira should include:

```text
Summary
Epic
Description
Owner
Reviewer
Priority
Requirements
Dependencies
Acceptance Criteria
Sprint
Status
```

Recommended workflow:

```text
To Do
↓
In Progress
↓
In Review
↓
Testing
↓
Done
```

Optional:

```text
Blocked
```

---

# 16. Sprint 1 Dependency Rules

Sprint 1 must not create blocking chains.

Use this rule:

```text
If upstream code is unavailable, use the approved fixture.
```

Examples:

Workstream 3 does not wait for Workstream 2.

It uses:

```text
sensor_observation.json
```

Workstream 4 uses:

```text
fusion_output.json
```

Workstream 5 uses:

```text
candidate_features.json
mission_state.json
```

---

# 17. Sprint 1 Integration Checkpoint

Mid-sprint, the team shall verify:

1. all five branches exist;
2. all members have committed code;
3. all required fixtures exist;
4. Workstream 3 can consume Workstream 2's contract;
5. Workstream 5 dashboard can consume `MissionState`;
6. no breaking interface change has occurred silently.

---

# 18. Sprint 1 Review Demo

At Sprint Review, demonstrate:

## Demo A

```text
ScenarioContext
→ controlled sensor input
→ SensorObservation
```

## Demo B

```text
SensorObservation
→ baseline fusion
→ FusionOutput
```

## Demo C

```text
Mock FusionOutput
→ localization skeleton
→ LocalizationOutput
```

## Demo D

```text
CandidateFeatureRecord
→ AI input validation
```

## Demo E

```text
MissionState
→ Dashboard
```

The goal is not scientific performance yet.

The goal is integrated engineering readiness.

---

# 19. Sprint 1 Exit Criteria

Sprint 1 is complete when:

- all five members have a functioning workstream repository area;
- interfaces v1.0 are usable;
- required fixtures exist;
- first interface tests pass;
- at least one cross-module integration works;
- dashboard renders a real contract fixture;
- AI feature guard exists;
- PR/review workflow has been used;
- no Critical Sprint 1 blocker remains open.

---

# 20. Initial Product Backlog After Sprint 1

The following backlog items are expected to become active after Sprint 1.

## Simulation

- complete scenario engine;
- complete search path;
- random challenge generation;
- mission replay.

## Sensors / Embedded

- complete radar model;
- complete thermal model;
- complete acoustic model;
- complete firmware;
- live serial integration.

## Fusion

- integrate full HAIF;
- health/quality pipeline;
- conflict/innovation logic;
- regression protection.

## Localization

- full evidence map;
- selective localization;
- track association;
- multiple-victim handling.

## AI

- dataset ingestion;
- training;
- calibration;
- uncertainty;
- abstention;
- explainability;
- sealed-test evaluation.

## Rescue Decision

- vitality;
- rescue ranking;
- recommendation logic.

## Dashboard

- final operational map;
- AI panels;
- fusion panels;
- hardware status;
- timeline;
- evaluation view.

## Integration

- MATLAB ↔ Python;
- microcontroller ↔ core pipeline;
- core pipeline ↔ dashboard.

---

# 21. Definition of Ready for Future Stories

A story is ready to enter a sprint when:

1. purpose is clear;
2. owner is assigned;
3. reviewer is assigned;
4. required interface is known;
5. dependencies are known;
6. acceptance criteria are written;
7. required fixture exists if upstream code is unavailable;
8. requirement references exist.

---

# 22. Definition of Done

A story is Done when:

1. implementation is complete;
2. unit tests pass;
3. interface tests pass where applicable;
4. regression remains green;
5. code is committed;
6. pull request is reviewed;
7. documentation is updated;
8. acceptance criteria are demonstrated;
9. Jira status is updated.

---

# 23. Sprint 1 Risk Focus

The team shall monitor:

```text
R-001 Scope Creep
R-002 Late Integration
R-004 Interface Breakage
R-017 Isolated Development
R-021 Git Workflow Problems
R-025 Cross-Technology Schema Mismatch
```

---

# 24. What Does Not Need to Be Completed Before Starting Sprint 1

The following do not need to be fully implemented before Sprint 1:

- full HAIF development;
- final localization algorithm;
- final trained AI model;
- final dashboard;
- live sensor hardware;
- final HIL demonstration;
- complete experiment suite;
- final report;
- final presentation.

These are implementation deliverables, not pre-development documents.

---

# 25. Pre-Development Documentation Complete

Once this document is approved, the minimum required pre-development documentation set is:

```text
docs/
├── 01_Project_Charter.md
├── 02_Requirements.md
├── 03_System_Architecture.md
├── 04_Team_Workstreams.md
├── 05_Interface_Contracts.md
├── 06_Test_Strategy.md
├── 07_Risk_Register.md
└── 08_Initial_Backlog_and_Sprint1.md
```

No additional planning document is required before the team begins Sprint 1.

Additional documentation should be created only when a real implementation need appears.

---
