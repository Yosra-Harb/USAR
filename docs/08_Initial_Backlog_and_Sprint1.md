# USAR Intelligent Rescue System
## Initial Backlog and Sprint 0 Plan

**Document:** `08_Initial_Backlog_and_Sprint_Plan.md`  
**Replaces:** `08_Initial_Backlog_and_Sprint1.md`  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Delivery Model:** Hybrid Agile / Scrum-style Sprints  
**Purpose:** Convert approved project documents into executable Jira work and define Sprint 0 before feature implementation  

---

# 1. Purpose

This document defines the initial Jira backlog structure and the Sprint 0 work required before full feature implementation begins.

It is the final minimum planning document before the team moves from documentation to execution.

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
- Sprint 0 Stories;
- workstream ownership;
- dependencies;
- acceptance criteria;
- interface fixtures;
- repository readiness;
- and integration checkpoints.

Detailed future sprint scheduling belongs in Jira and may evolve through Sprint Planning.

---

# 2. Delivery Approach

The project uses a Hybrid Agile delivery model with Scrum-style iterations.

The team combines:

- stable project scope;
- explicit architecture;
- controlled interface contracts;
- short implementation sprints;
- continuous integration;
- peer review;
- regression testing;
- research-governance gates;
- HIL milestones;
- and milestone-based delivery.

Recommended implementation sprint duration:

```text
1–2 weeks
```

Sprint 0 is shorter and focuses on engineering readiness rather than major feature completion.

Recommended Sprint 0 duration:

```text
3–5 working days
```

---

# 3. Final Workstream Ownership

| Workstream | Owner Scope |
|---|---|
| WS1 | Simulation and Mission Systems |
| WS2 | Sensors, Microcontroller, Acquisition, and Signal Processing |
| WS3 | HAIF Fusion and Localization |
| WS4 | Victim Tracking, AI Intelligence, Vitality, and Rescue Decision |
| WS5 | Backend, Operational Dashboard, and System Integration |

The microcontroller/HIL path is core project scope.

---

# 4. Initial Jira Epics

Create the following Epics in Jira.

## EPIC-01 — Project Foundation

**Owner:** Entire Team / Technical Lead coordination

Includes:

- approved documentation;
- repository structure;
- contribution rules;
- contracts;
- fixtures;
- CI/test commands;
- risk governance.

---

## EPIC-02 — Simulation and Mission Engine

**Owner:** Workstream 1

Scope:

- scenario generation;
- probe initialization;
- mission state machine;
- search path;
- coverage;
- scenario fixtures;
- ground-truth isolation;
- mission events.

---

## EPIC-03 — Sensors, Microcontroller, and Acquisition

**Owner:** Workstream 2

Scope:

- UWB radar path;
- thermal path;
- acoustic path;
- simulated sensor behavior;
- signal preprocessing;
- microcontroller firmware/acquisition;
- communication protocol;
- `HardwarePacket`;
- acquisition adapter;
- packet validation;
- HIL;
- `SensorObservation`;
- `HardwareStatus`.

---

## EPIC-04 — HAIF and Adaptive Fusion

**Owner:** Workstream 3

Scope:

- sensor health;
- measurement quality;
- robust statistics;
- HAIF;
- adaptive weights;
- confidence;
- reliability diagnostics;
- fusion explainability.

---

## EPIC-05 — Localization

**Owner:** Workstream 3

Scope:

- Evidence Map;
- spatial evidence update;
- reliability masking;
- localization;
- localization confidence;
- acceptance/rejection;
- localization evaluation.

---

## EPIC-06 — Victim Tracking and AI Intelligence

**Owner:** Workstream 4

Scope:

- candidate creation;
- association;
- track management;
- candidate feature schema;
- AI dataset pipeline;
- candidate classification;
- training;
- calibration;
- uncertainty;
- abstention;
- explainability;
- AI auditability.

---

## EPIC-07 — Vitality and Rescue Decision

**Owner:** Workstream 4

Scope:

- Vitality Index;
- rescue priority;
- multi-victim ranking;
- recommendation metadata.

---

## EPIC-08 — Backend and Operational Dashboard

**Owner:** Workstream 5

Scope:

- backend service;
- telemetry ingestion/consumption;
- mission-state assembly;
- REST/API contracts;
- MATLAB/Python/HIL platform integration;
- operational map;
- mission status;
- victims;
- AI outputs;
- sensor/fusion/localization intelligence;
- vitality;
- hardware status;
- timeline;
- evaluation view.

---

## EPIC-09 — System Integration

**Owner:** Entire Team  
**Coordination:** Workstream 5 + Technical Lead

Scope:

- module adapters;
- contract validation;
- MATLAB/Python integration;
- microcontroller/core integration;
- backend/dashboard integration;
- unified `MissionState`;
- end-to-end pipeline.

---

## EPIC-10 — Testing and Validation

**Owner:** Entire Team

Scope:

- unit tests;
- interface tests;
- regression;
- AI validation;
- HIL tests;
- end-to-end tests;
- acceptance tests;
- research-validation gates.

---

## EPIC-11 — Final Demonstration and Documentation

**Owner:** Entire Team

Scope:

- final simulation demo;
- final HIL demo;
- reproducible run configuration;
- fallback demo plan;
- report;
- presentation assets;
- GitHub release documentation.

---

# 5. Backlog Priority Policy

Every backlog item shall receive one of:

```text
MUST
SHOULD
COULD
WON'T FOR CURRENT RELEASE
```

Sprint 0 contains only MUST readiness items.

---

# 6. Sprint 0 Goal

## Shared Sprint Goal

```text
All five workstreams can begin independently against approved v1.0
contracts and fixtures, the microcontroller/HIL path has a defined
working protocol boundary, and the repository/Jira workflow supports
parallel development without depending on unfinished upstream code.
```

Sprint 0 does **not** attempt to complete the scientific algorithms.

It proves:

- repository structure;
- team workflow;
- final ownership;
- interface freeze;
- fixtures;
- module entry points;
- test commands;
- PR/review flow;
- microcontroller/HIL contract readiness;
- first producer-consumer integrations.

---

# 7. Sprint 0 Entry Criteria

Sprint 0 may begin when:

- `01_Project_Charter.md` is approved;
- `02_Requirements.md` is approved;
- `03_System_Architecture.md` is approved;
- revised `04_Team_Workstreams.md` is approved;
- revised `05_Interface_Contracts.md` is approved;
- revised `06_Test_Strategy.md` is approved;
- `07_Risk_Register.md` is approved;
- the five team members are identified;
- GitHub access is confirmed;
- Jira access is confirmed.

Contracts become frozen as v1.0 **during Sprint 0**, not before the team starts Sprint 0.

---

# 8. Sprint 0 — Workstream 1 Backlog

## Story S0-WS1-01 — Validate ScenarioContext v1.0

**Epic:** Simulation and Mission Engine  
**Owner:** Workstream 1  
**Reviewer:** Workstream 2  
**Priority:** MUST

### Related Requirements

```text
FR-001
FR-002
FR-003
FR-004
```

### Tasks

- confirm scenario configuration structure;
- include scenario type;
- include random seed;
- include debris/noise/burial depth;
- include victim count/vital strength;
- include probe state;
- create `ScenarioContext` fixture;
- keep evaluation-only ground truth outside the operational fixture.

### Acceptance Criteria

- fixture conforms to contract v1.0;
- same seed/configuration can be represented reproducibly;
- no ground-truth leakage exists in operational fields;
- Workstream 2 can parse the fixture.

---

## Story S0-WS1-02 — Create Mission Controller Entry Point

### Tasks

Define initial states:

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

Create a minimal executable mission-controller skeleton or documented entry point.

### Acceptance Criteria

- initial state can be created;
- a valid state transition can be demonstrated;
- transition/event format is documented;
- future implementation has a stable entry point.

---

## Story S0-WS1-03 — Create Probe Fixture

### Tasks

- define initial probe position;
- define path/visited-cell example;
- expose probe state needed by Workstream 2.

### Acceptance Criteria

- fixture is deterministic;
- downstream sensor work can begin without the final search algorithm.

---

# 9. Sprint 0 — Workstream 2 Backlog

## Story S0-WS2-01 — Freeze HardwarePacket v1.0

**Epic:** Sensors, Microcontroller, and Acquisition  
**Owner:** Workstream 2  
**Reviewer:** Workstream 3  
**Priority:** MUST

### Related Requirements

```text
FR-016
FR-017
FR-018
FR-019
FR-020
FR-021
FR-022
FR-023
```

### Tasks

- confirm packet fields;
- define sequence behavior;
- define timestamp convention;
- define sensor-value fields;
- define availability/status flags;
- create valid packet fixture;
- create malformed packet fixture;
- document Serial/USB framing approach.

### Acceptance Criteria

- valid packet can be parsed;
- malformed packet is rejected by a smoke validator or planned test harness;
- missing sequence is detectable;
- schema matches `05_Interface_Contracts.md`;
- protocol boundary is clear enough to begin firmware/acquisition work.

---

## Story S0-WS2-02 — Freeze SensorObservation v1.0

### Tasks

Confirm the adapter boundary:

```text
Simulation Input ─┐
                  ├─→ SensorObservation
HardwarePacket ───┘
```

Create:

- one Simulation Mode fixture;
- one HIL Mode fixture.

### Acceptance Criteria

- both fixtures use the same downstream schema;
- `sourceMode` is correct;
- required fields exist;
- Workstream 3 can consume them without manual restructuring.

---

## Story S0-WS2-03 — Create Sensor Processing Entry Points

### Tasks

Create initial interfaces/skeletons for:

- radar;
- thermal;
- acoustic;
- signal preprocessing;
- hardware-status reporting.

### Acceptance Criteria

- each path has an identifiable entry point;
- mock input can return contract-compatible output;
- at least one smoke test command is documented.

---

# 10. Sprint 0 — Workstream 3 Backlog

## Story S0-WS3-01 — Create SensorObservation Consumer

**Epic:** HAIF and Adaptive Fusion  
**Owner:** Workstream 3  
**Reviewer:** Workstream 4  
**Priority:** MUST

### Tasks

- parse `SensorObservation`;
- validate modality availability;
- validate expected numeric ranges;
- establish the fusion processing entry point.

### Acceptance Criteria

- valid fixture is accepted;
- invalid fixture is rejected or flagged;
- module can run independently of Workstream 2's unfinished implementation.

---

## Story S0-WS3-02 — Freeze FusionOutput v1.0

### Tasks

Define output mapping for:

- fusion score;
- confidence;
- weights;
- effective support;
- agreement;
- diagnostics.

### Acceptance Criteria

- fixture matches contract;
- unavailable-sensor behavior is represented;
- Workstream 5 can parse the fixture.

---

## Story S0-WS3-03 — Freeze LocalizationOutput v1.0

### Tasks

- define accepted case;
- define rejected case;
- define location/confidence fields;
- create fixtures for both cases;
- document the existing/future localization entry point.

### Acceptance Criteria

- Workstream 4 can consume the fixture;
- rejected result supports `null` position;
- confidence/range rules are explicit.

---

## Story S0-WS3-04 — Preserve Research Regression Entry Point

### Tasks

- identify the command/function used to run the protected HAIF/localization regression suite;
- document how changes will be checked before merge;
- do not modify frozen research thresholds during Sprint 0.

### Acceptance Criteria

- regression entry point is documented;
- research code can be protected during team restructuring.

---

# 11. Sprint 0 — Workstream 4 Backlog

## Story S0-WS4-01 — Freeze VictimTrack v1.0

**Epic:** Victim Tracking and AI Intelligence  
**Owner:** Workstream 4  
**Reviewer:** Workstream 5  
**Priority:** MUST

### Tasks

Create initial track structure containing:

- ID;
- estimated location;
- localization confidence;
- detection count;
- independent views;
- existence probability;
- temporal stability;
- status.

### Acceptance Criteria

- fixture validates;
- AI feature builder can consume it;
- Workstream 5 can display it through mock `MissionState`.

---

## Story S0-WS4-02 — Freeze CandidateFeatureRecord and AIOutput v1.0

### Tasks

- confirm feature metadata;
- define forbidden oracle fields;
- create valid candidate fixture;
- create forbidden-field fixture;
- create AI output fixture including uncertainty and abstention.

### Acceptance Criteria

- feature schema is versioned;
- oracle fields are explicitly forbidden;
- AI output supports `ACCEPT_TRUE_TRACK`, `REJECT_HARD_NEGATIVE`, and `ABSTAIN`;
- Workstream 5 can consume the AI fixture.

---

## Story S0-WS4-03 — Initialize AI Project Structure

Create initial structure such as:

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

- structure exists or equivalent structure is approved;
- environment/setup is documented;
- test command executes;
- no sealed-test data is used for development setup.

---

## Story S0-WS4-04 — Freeze RescueDecision v1.0

### Tasks

- define vitality field;
- define priority enum;
- define recommended action;
- create fixture.

### Acceptance Criteria

- Workstream 5 can consume the fixture;
- output does not expose evaluation-only ground truth.

---

# 12. Sprint 0 — Workstream 5 Backlog

## Story S0-WS5-01 — Initialize Backend / Integration Structure

**Epic:** Backend and Operational Dashboard  
**Owner:** Workstream 5  
**Reviewer:** Workstream 1  
**Priority:** MUST

### Tasks

- confirm backend project location;
- confirm FastAPI entry point or equivalent approved backend service;
- create/verify integration modules;
- define how `MissionState` will be assembled from fixtures;
- define safe operational/evaluation boundary.

### Acceptance Criteria

- backend starts locally or a skeleton health endpoint runs;
- configuration approach is documented;
- `MissionState` can be produced from fixture data or a mock adapter;
- no scientific algorithm is duplicated in the backend.

---

## Story S0-WS5-02 — Initialize Dashboard Project

### Tasks

Confirm/initialize:

```text
React
TypeScript
Vite
```

Create/verify primary layout entry points such as:

```text
MissionStatusStrip
OperationalMap
DecisionWorkspace
VictimIntelligence
FusionIntelligence
SensorReadings
HardwareStatus
Timeline
```

### Acceptance Criteria

- dashboard launches locally;
- mock `MissionState` loads;
- mission status is visible;
- at least one victim fixture can be rendered;
- hardware state can be rendered;
- no live backend is required for this Sprint 0 story.

---

## Story S0-WS5-03 — Freeze MissionState v1.0

### Tasks

- map sensor/fusion/localization/victim/AI/decision/hardware sections;
- create `mission_state.json` fixture;
- define controlled error behavior for schema mismatch.

### Acceptance Criteria

- dashboard can parse it;
- backend can assemble it from mock inputs;
- HIL state is represented;
- AI abstention is representable;
- no ground-truth leakage exists during active mission mode.

---

## Story S0-WS5-04 — Establish Integration / CI Smoke Workflow

### Tasks

- document backend test command;
- document dashboard build/test command;
- add initial CI where feasible;
- ensure shared fixtures are reachable by integration tests.

### Acceptance Criteria

- at least one automated smoke check runs on a PR or locally through a documented command;
- repository integration path is reproducible.

---

# 13. Sprint 0 Shared Repository Tasks

## S0-REPO-01 — Create / Verify Development Branch

Recommended:

```text
main
develop
```

Feature work branches from `develop`.

---

## S0-REPO-02 — Protect Main

Where available:

- require pull request;
- require review;
- prevent accidental direct feature development;
- require selected checks before merge when feasible.

---

## S0-REPO-03 — Create Interface Directories

Create:

```text
interfaces/
├── schemas/
├── fixtures/
└── examples/
```

---

## S0-REPO-04 — Add Initial Fixtures

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
rescue_decision.json
hardware_status.json
mission_state.json
```

### Acceptance Criteria

- every fixture matches the approved contract;
- all five workstreams can consume their required fixture.

---

# 14. Sprint 0 Shared Integration Stories

## S0-INT-01 — First HIL Contract Smoke Path

Target:

```text
HardwarePacket Fixture
        ↓
Acquisition Adapter / Parser
        ↓
SensorObservation
```

### Acceptance Criteria

- valid packet maps correctly;
- invalid packet fails safely;
- no downstream scientific code is required.

---

## S0-INT-02 — First Fusion Contract Smoke Path

Target:

```text
SensorObservation Fixture
        ↓
Workstream 3 Consumer
        ↓
FusionOutput Fixture / Skeleton
```

### Acceptance Criteria

- no manual field conversion is required;
- contract test passes.

---

## S0-INT-03 — First Product Contract Smoke Path

Target:

```text
MissionState Fixture
        ↓
Backend / Dashboard
```

### Acceptance Criteria

- backend/dashboard consume the same contract;
- schema mismatch produces a controlled error;
- hardware and AI states are representable.

---

# 15. Sprint 0 Testing Tasks

## S0-TEST-01 — Interface Validation Smoke Tests

Validate:

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
```

---

## S0-TEST-02 — Regression Entry-Point Check

Ensure the protected core regression suite can still be launched from the reorganized repository or from a documented location.

Sprint 0 does not require migration of every legacy test.

It requires preserving access to validated regression behavior.

---

## S0-TEST-03 — AI Leakage Guard Skeleton

Create or define an automated check that rejects candidate feature records containing forbidden oracle fields.

---

## S0-TEST-04 — HIL Failure Fixture Set

Prepare at least:

- valid packet;
- malformed packet;
- missing-sequence case;
- disconnected hardware status.

---

# 16. Jira Setup for Sprint 0

Every Sprint 0 Story should include:

```text
Summary
Epic
Description
Owner
Reviewer
Priority
Requirement References
Dependencies
Acceptance Criteria
Story Points / Effort
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

Optional explicit status:

```text
Blocked
```

---

# 17. Sprint 0 Dependency Rule

Sprint 0 must not create blocking chains.

Use this rule:

```text
If upstream code is unavailable, use the approved fixture.
```

Examples:

- WS2 uses `scenario_context.json`;
- WS3 uses `sensor_observation.json`;
- WS4 uses `localization_output.json` and candidate fixtures;
- WS5 uses `mission_state.json`, `ai_output.json`, `hardware_status.json`, and `rescue_decision.json`.

---

# 18. Sprint 0 Mid-Sprint Integration Checkpoint

Verify:

1. all five members have active branches/issues;
2. all required fixtures exist or have assigned owners;
3. no workstream is waiting for final upstream code;
4. `HardwarePacket` → `SensorObservation` boundary is agreed;
5. Workstream 3 can consume `SensorObservation`;
6. Workstream 4 can consume localization/candidate fixtures;
7. Workstream 5 can consume `MissionState`;
8. no breaking interface change has occurred silently;
9. research/fresh/sealed data remain protected.

---

# 19. Sprint 0 Review Demo

At Sprint Review, demonstrate engineering readiness.

## Demo A — Simulation Boundary

```text
ScenarioContext Fixture
→ Sensor Layer Consumer
```

## Demo B — HIL Boundary

```text
HardwarePacket Fixture
→ Packet Validation
→ SensorObservation
```

## Demo C — Scientific Core Boundary

```text
SensorObservation
→ HAIF/Fusion Entry Point
→ FusionOutput
```

and/or:

```text
FusionOutput
→ Localization Entry Point
→ LocalizationOutput
```

## Demo D — AI Boundary

```text
CandidateFeatureRecord
→ AI Input Validation
→ AIOutput Fixture / Skeleton
```

## Demo E — Product Boundary

```text
MissionState
→ Backend / Dashboard
```

The goal is not final scientific performance.

The goal is safe parallel engineering readiness.

---

# 20. Sprint 0 Exit Criteria

Sprint 0 is complete when:

- all five workstreams have a functioning repository area/entry point;
- v1.0 interfaces are usable and frozen under change control;
- required fixtures exist;
- microcontroller packet/protocol boundary is documented;
- HIL fixtures exist;
- first interface smoke tests pass;
- at least one cross-module integration works;
- dashboard renders a real contract fixture;
- backend consumes or assembles a mock mission state;
- AI leakage guard exists or is executable as an agreed test;
- PR/review workflow has been used;
- no Critical Sprint 0 blocker remains open.

---

# 21. Initial Product Backlog After Sprint 0

The following areas become active through later sprints.

## Simulation / Mission — WS1

- complete scenario engine;
- search path;
- random challenge generation;
- coverage;
- mission replay;
- mission event generation.

## Sensors / Microcontroller — WS2

- complete radar processing/model;
- complete thermal processing/model;
- complete acoustic processing/model;
- firmware/acquisition;
- live Serial/USB integration;
- packet validation;
- HIL status telemetry;
- common `SensorObservation` adapter.

## HAIF / Localization — WS3

- full HAIF integration;
- health/quality pipeline;
- conflict/innovation logic;
- effective support;
- regression protection;
- Evidence Map;
- selective localization;
- localization validation.

## Tracking / AI / Decision — WS4

- candidate association;
- multi-victim tracking;
- dataset generation;
- training;
- calibration;
- uncertainty;
- abstention;
- explainability;
- sealed-test evaluation;
- vitality;
- rescue ranking.

## Backend / Dashboard / Integration — WS5

- complete telemetry bridge;
- mission API;
- MATLAB/Python/HIL integration;
- operational map;
- AI panels;
- fusion/localization panels;
- hardware status;
- timeline;
- evaluation view;
- live end-to-end integration.

---

# 22. Definition of Ready for Future Stories

A story is ready to enter a sprint when:

1. purpose is clear;
2. owner is assigned;
3. reviewer is assigned;
4. required interface is known;
5. dependencies are known;
6. acceptance criteria are written;
7. required fixture exists if upstream code is unavailable;
8. requirement references exist;
9. protected research/sealed data are identified if applicable.

---

# 23. Definition of Done

A story is Done when:

1. implementation is complete;
2. unit tests pass;
3. interface tests pass where applicable;
4. protected regression remains green;
5. code is committed;
6. pull request is reviewed;
7. integration is demonstrated where applicable;
8. documentation is updated;
9. acceptance criteria are demonstrated;
10. Jira status is updated.

---

# 24. Sprint 0 Risk Focus

The team shall actively monitor:

```text
R-001 Scope Creep
R-002 Late Integration
R-004 Interface Breakage
R-006 HAIF Regression
R-009 Oracle Leakage
R-011 Hardware Availability
R-017 Isolated Development
R-020 Demo Hardware Failure
R-021 Git Workflow Problems
R-025 Cross-Technology Schema Mismatch
```

---

# 25. What Does Not Need to Be Complete Before Sprint 1

Sprint 0 does not require completion of:

- full scenario engine;
- full sensor algorithms;
- final microcontroller firmware;
- physical availability of every final sensor;
- full HAIF development;
- final localization algorithm;
- final victim tracking;
- final trained AI model;
- final dashboard;
- final HIL demonstration;
- complete experiment suite;
- final report;
- final presentation.

These are implementation deliverables for later sprints.

However, the contracts, entry points, fixtures, owners, and test paths required to develop them must be ready.

---

# 26. Pre-Development Documentation Set

The minimum documentation set remains:

```text
docs/
├── 01_Project_Charter.md
├── 02_Requirements.md
├── 03_System_Architecture.md
├── 04_Team_Workstreams.md
├── 05_Interface_Contracts.md
├── 06_Test_Strategy.md
├── 07_Risk_Register.md
└── 08_Initial_Backlog_and_Sprint_Plan.md
```

Additional planning documents are not required merely for completeness. Create new documentation when a real implementation or governance need appears.

---

# 27. Next Action After Approval

1. Replace/update documents 04, 05, 06, and 08 in GitHub.
2. Rename the old `08_Initial_Backlog_and_Sprint1.md` to `08_Initial_Backlog_and_Sprint_Plan.md`.
3. Create the Jira Epics.
4. Create **Sprint 0**.
5. Add the Sprint 0 Stories from this document.
6. Assign Owners and Reviewers.
7. Estimate Story Points / effort.
8. Create/verify `develop` and feature branches.
9. Create the initial interface fixtures.
10. Start all five workstreams in parallel.

At this point, the project moves from:

```text
Planning
```

to:

```text
Sprint 0 — Engineering Readiness
```

and then to:

```text
Sprint 1 — Feature Implementation
```
