# USAR Intelligent Rescue System
## System Architecture Specification

**Document:** 03_System_Architecture.md  
**Project:** USAR Intelligent Rescue System  
**Project Type:** Computer Engineering Graduation Project  
**Team Size:** 5 Members  
**Development Approach:** Hybrid Agile Software Delivery  
**Architecture Style:** Modular, Interface-Driven, Simulation + Hardware-in-the-Loop  

---

# 1. Purpose

This document defines the high-level and component-level architecture of the USAR Intelligent Rescue System.

The architecture is designed to:

- support parallel development by a five-member team;
- isolate module responsibilities;
- separate simulation logic from operational processing;
- support both Simulation Mode and Hardware-in-the-Loop Mode;
- integrate MATLAB, Python-based AI, a microcontroller acquisition layer, and a web dashboard;
- maintain traceability from sensor observations to rescue recommendations;
- preserve research integrity by isolating ground truth from operational decision logic;
- and allow future replacement of simulated sensors with physical sensing hardware.

This document focuses on system structure, component boundaries, data flow, interfaces, runtime responsibilities, and integration rules.

---

# 2. Architectural Principles

The system architecture shall follow these principles.

## 2.1 Modularity

Each major technical capability shall be implemented as a separate module with explicit responsibilities.

## 2.2 Interface-Driven Design

Modules shall exchange data through documented contracts rather than directly depending on each other's internal implementation.

## 2.3 Parallel Development

Each major workstream shall be independently testable using mocks, fixtures, or recorded data.

## 2.4 Shared Downstream Pipeline

Simulation and hardware inputs shall converge into a common sensor-observation interface so downstream processing does not depend on the data source.

## 2.5 Research Integrity

Ground truth shall remain isolated from operational processing and shall be accessible only by evaluation and testing modules.

## 2.6 Explainability

Fusion, AI, localization, vitality, and rescue-priority outputs shall expose supporting metadata for interpretation and audit.

## 2.7 Graceful Degradation

Failure or degradation of one sensor or subsystem should not necessarily cause complete system failure when sufficient valid evidence remains.

## 2.8 Reproducibility

Simulation, evaluation, and AI experiments shall be reproducible using explicit configuration, seed, model version, and schema version information.

---

# 3. High-Level System Architecture

The complete system is organized into the following layers:

```text
┌─────────────────────────────────────────────┐
│              USER / OPERATOR                │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│          OPERATIONAL DASHBOARD              │
│ Mission | Map | AI | Fusion | Victims      │
│ Vitality | Timeline | Hardware | Evaluation │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│       RESCUE DECISION & VICTIM LAYER        │
│ Tracking | Vitality | Rescue Priority       │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│                 AI LAYER                    │
│ Classification | Calibration | Uncertainty │
│ Abstention | Explainability | Auditability  │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│           LOCALIZATION LAYER                │
│ Evidence Map | Peak Search | Position Est.  │
│ Localization Confidence                    │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│        FUSION & RELIABILITY LAYER           │
│ Health | Quality | HAIF | Confidence        │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│        SIGNAL PROCESSING LAYER              │
│ Radar | Thermal | Acoustic Processing       │
└──────────────────────┬──────────────────────┘
                       ↓
┌─────────────────────────────────────────────┐
│       COMMON ACQUISITION INTERFACE          │
└───────────────┬─────────────────┬───────────┘
                ↑                 ↑
                │                 │
┌───────────────┴───────┐ ┌───────┴──────────┐
│   SIMULATION MODE     │ │      HIL MODE     │
│ Scenario + Sensors    │ │ Microcontroller   │
└───────────────────────┘ └───────────────────┘
```

---

# 4. Runtime Modes

The system supports two primary runtime modes.

## 4.1 Simulation Mode

Simulation Mode is used for controlled testing, algorithm development, research experiments, regression testing, and reproducible evaluation.

```text
Scenario Generator
        ↓
Mission / Probe Controller
        ↓
Simulated Sensor Models
        ↓
Common Sensor Observation Interface
        ↓
Core Processing Pipeline
```

Simulation Mode may generate:

- collapsed-structure geometry;
- debris configuration;
- noise levels;
- victim count and locations;
- burial depth;
- vital strength;
- sensor attenuation;
- occlusion;
- sensor dropout;
- and ground truth.

Ground truth must not enter the operational pipeline.

---

## 4.2 Hardware-in-the-Loop Mode

Hardware-in-the-Loop Mode is used to connect the processing system to a microcontroller-based acquisition unit.

```text
Physical / Emulated Sensor Source
        ↓
Microcontroller
        ↓
Communication Protocol
        ↓
Packet Validation
        ↓
Acquisition Adapter
        ↓
Common Sensor Observation Interface
        ↓
Core Processing Pipeline
```

The processing stages after the common sensor-observation interface should remain identical to Simulation Mode wherever practical.

---

# 5. Major Architectural Components

The system is divided into the following major components.

---

## 5.1 Scenario and Mission Engine

### Responsibility

The Scenario and Mission Engine controls the simulated rescue environment and mission progression.

### Main Responsibilities

- create rescue scenarios;
- generate victims and ground truth;
- configure debris, noise, burial depth, and vital strength;
- initialize the probe;
- generate and execute the search path;
- maintain visited cells;
- calculate coverage;
- manage mission state;
- trigger sensing cycles;
- and record mission-level events.

### Core Functions

Expected functions include:

```text
createScenario
createProbe
missionController
runProbeSimulation
```

### Inputs

- scenario configuration;
- random seed;
- mission parameters.

### Outputs

- mission state;
- probe position;
- environment state;
- simulated sensor context;
- ground truth for evaluation only.

### Important Rule

Ground truth shall be routed only to evaluation and test modules.

---

## 5.2 Sensor Simulation and Signal Processing Layer

### Responsibility

This layer converts scenario or hardware measurements into normalized sensor evidence.

### Sensor Modalities

- UWB Radar
- Thermal
- Acoustic

### UWB Processing

May include:

- respiration-related evidence;
- heartbeat-related evidence;
- periodicity;
- amplitude;
- attenuation;
- distance falloff;
- burial-depth effects.

### Thermal Processing

May include:

- thermal contrast;
- Gaussian-like response;
- occlusion effects;
- environmental-temperature influence.

### Acoustic Processing

May include:

- band-pass filtering;
- envelope extraction;
- periodicity;
- human-generated acoustic evidence;
- background-noise suppression.

### Main Function

```text
signalProcessingManager
```

### Outputs

A normalized `SensorObservation` object.

---

# 6. Common Sensor Observation Interface

All sensor sources must produce a common internal representation before entering the fusion layer.

Conceptual structure:

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "sequence": 1042,
  "timestamp": 1730000012,
  "sourceMode": "SIMULATION",
  "probe": {
    "x": 4.2,
    "y": 7.1
  },
  "radar": {
    "available": true,
    "rawScore": 0.74,
    "health": 0.93,
    "quality": 0.84
  },
  "thermal": {
    "available": true,
    "rawScore": 0.63,
    "health": 0.96,
    "quality": 0.91
  },
  "acoustic": {
    "available": true,
    "rawScore": 0.28,
    "health": 0.89,
    "quality": 0.36
  }
}
```

This contract is finalized in:

`05_Interface_Contracts.md`

---

# 7. Microcontroller and Acquisition Architecture

The microcontroller layer acts as a hardware gateway.

## 7.1 Responsibilities

The microcontroller integration shall support:

- sensor reading acquisition;
- timestamp or sequence assignment;
- packet construction;
- sensor status reporting;
- communication with the host computer;
- connection-state monitoring;
- packet-loss detection;
- and error reporting.

## 7.2 Communication Path

```text
Sensor Source
    ↓
Microcontroller
    ↓
Serial / USB Serial
    ↓
Host Acquisition Adapter
    ↓
Packet Parser
    ↓
Packet Validator
    ↓
SensorObservation Adapter
```

## 7.3 Hardware Gateway Boundary

The microcontroller shall not implement high-level rescue decisions.

Its main role is:

- acquisition;
- packaging;
- transport;
- low-level device status.

Fusion, localization, AI, vitality, and rescue-priority logic remain on the host processing system.

## 7.4 Packet Metadata

Each packet should support:

- schema version;
- sequence number;
- timestamp;
- sensor values;
- availability flags;
- sensor status;
- optional device telemetry.

---

# 8. Sensor Reliability and HAIF Fusion Layer

## 8.1 Responsibility

This layer evaluates observation reliability and combines multi-sensor evidence.

## 8.2 Core Concepts

The layer separates:

- availability;
- sensor health;
- measurement quality;
- inter-sensor agreement;
- conflict;
- innovation;
- quality memory;
- and anomaly behavior.

## 8.3 HAIF Processing

Conceptually:

```text
Sensor Observation
        ↓
Availability Assessment
        ↓
Health Assessment
        ↓
Measurement Quality
        ↓
Conflict / Innovation Analysis
        ↓
Quality Memory
        ↓
One-Sided Anomaly Logic
        ↓
Effective Sensor Support
        ↓
Adaptive Weights
        ↓
Fusion Score
        ↓
Fusion Confidence
```

## 8.4 Core Functions

Expected functions include:

```text
calculateAdaptiveWeights
adaptiveFusion
confidenceEstimation
```

## 8.5 Outputs

Conceptual `FusionOutput`:

```json
{
  "fusionScore": 0.78,
  "confidence": 0.86,
  "weights": {
    "radar": 0.44,
    "thermal": 0.38,
    "acoustic": 0.18
  },
  "effectiveSupport": {
    "radar": 0.72,
    "thermal": 0.81,
    "acoustic": 0.29
  },
  "sensorAgreement": 0.79
}
```

---

# 9. Localization Architecture

## 9.1 Responsibility

The localization layer converts accumulated evidence into probable victim locations.

## 9.2 Processing Flow

```text
Fusion Evidence
        ↓
Spatial Evidence Update
        ↓
Evidence Map
        ↓
Evidence Decay
        ↓
Reliability Mask
        ↓
Peak Search
        ↓
Local Region Selection
        ↓
Weighted Position Estimate
        ↓
Localization Confidence
        ↓
Acceptance / Rejection
```

## 9.3 Core Functions

Expected functions include:

```text
updateEvidenceMap
localizationManager
```

## 9.4 Key Outputs

- estimated X coordinate;
- estimated Y coordinate;
- localization confidence;
- accepted / rejected localization state;
- evidence peak;
- spatial-support metadata.

## 9.5 Evaluation Boundary

Ground-truth coordinates are available only to the evaluation module for localization-error calculation.

---

# 10. Victim Candidate and Tracking Architecture

## 10.1 Responsibility

This layer converts repeated detections into persistent victim candidates and tracks.

## 10.2 Responsibilities

- create candidates;
- associate new observations with existing candidates;
- merge candidates when appropriate;
- maintain detection count;
- maintain independent-view count;
- maintain temporal stability;
- maintain existence probability;
- store localization history;
- and maintain victim-track status.

## 10.3 Core Function

```text
updateVictimDatabase
```

## 10.4 Candidate State

A candidate may conceptually pass through states such as:

```text
NEW
OBSERVED
SUPPORTED
AI_PENDING
CONFIRMED
REJECTED
ABSTAINED
```

Exact states will be finalized in the interface and state-model documents.

---

# 11. AI Layer Architecture

The AI layer operates above the deterministic sensing, fusion, localization, and tracking pipeline.

Its purpose is not to replace the physics-based system.

Its purpose is to validate victim candidates and quantify decision uncertainty.

---

## 11.1 AI Input

The AI layer consumes candidate-level features derived from the operational pipeline.

Examples:

- fusion score;
- fusion confidence;
- sensor weights;
- sensor agreement;
- sensor health;
- measurement quality;
- update count;
- independent views;
- existence probability;
- temporal stability;
- localization confidence;
- spatial stability;
- LogBF-related features;
- approved reliability indicators.

Oracle or ground-truth features are forbidden.

---

## 11.2 AI Candidate Classifier

The classifier predicts:

```text
TRUE_TRACK
HARD_NEGATIVE
```

Ambiguous samples remain excluded from primary supervised training:

```text
AMBIGUOUS_IGNORE
```

---

## 11.3 Probability Calibration

Raw classifier scores are passed through a calibration stage.

The calibrated output represents:

```text
P(TRUE_TRACK)
```

---

## 11.4 Uncertainty Estimation

The system estimates prediction uncertainty using the approved uncertainty method.

The uncertainty output is consumed by the abstention policy.

---

## 11.5 Abstention Policy

The AI layer can return:

```text
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
```

Abstention is used when evidence is insufficient or uncertainty exceeds approved limits.

---

## 11.6 Explainability

The AI layer shall expose supporting and opposing factors.

Examples:

```text
Supporting:
- strong multi-sensor consistency
- stable localization
- repeated independent observations

Risk:
- degraded acoustic quality
- weak evidence persistence
```

---

## 11.7 AI Audit Metadata

Each AI decision shall be associated with:

- model version;
- feature schema version;
- candidate ID;
- probability;
- uncertainty;
- threshold;
- abstention state;
- timestamp;
- explanation metadata.

---

# 12. AI Training and Evaluation Architecture

The AI development pipeline is separated from runtime inference.

```text
Dataset Generation
        ↓
Candidate Feature Export
        ↓
Data Quality Validation
        ↓
Train / Validation / Sealed Test Split
        ↓
Model Training
        ↓
Model Selection
        ↓
Probability Calibration
        ↓
Uncertainty Configuration
        ↓
Abstention Policy
        ↓
Sealed Test Evaluation
        ↓
Model Artifact
        ↓
Runtime Inference
```

The sealed test set shall not be used for tuning.

---

# 13. Vitality Architecture

## 13.1 Responsibility

The vitality component estimates victim condition using operational evidence.

## 13.2 Inputs

Approved inputs include:

- fusion score;
- fusion confidence;
- localization confidence;
- temporal stability;
- track evidence.

## 13.3 Output

A normalized Vitality Index.

The project currently treats lower vitality as potentially indicating greater rescue urgency, subject to confidence and approved decision rules.

## 13.4 Core Function

```text
vitalityManager
```

---

# 14. Rescue Decision Architecture

## 14.1 Responsibility

The rescue decision layer converts validated victim information into operator-facing recommendations.

## 14.2 Responsibilities

- determine candidate acceptance;
- consider AI decision state;
- consider localization confidence;
- calculate rescue priority;
- rank multiple confirmed victims;
- maintain exit criteria;
- and generate recommendation metadata.

## 14.3 Core Function

```text
decisionEngine
```

## 14.4 Example Output

```json
{
  "victimId": "V2",
  "status": "CONFIRMED",
  "vitalityIndex": 0.28,
  "priority": "CRITICAL",
  "recommendedAction": "RESCUE_FIRST"
}
```

---

# 15. Dashboard Architecture

The dashboard is the operational product layer.

## 15.1 Frontend Technology

Planned frontend stack:

```text
React
TypeScript
Vite
```

## 15.2 Dashboard Responsibilities

The dashboard shall visualize:

- mission status;
- operating mode;
- probe position;
- search path;
- coverage;
- evidence map;
- victims;
- localization confidence;
- sensor health;
- measurement quality;
- fusion weights;
- fusion confidence;
- AI classification;
- calibrated probability;
- uncertainty;
- abstention state;
- vitality;
- rescue priority;
- hardware status;
- mission events;
- evaluation metrics.

## 15.3 Main Dashboard Components

Expected components include:

```text
MissionStatusStrip
OperationalMap
MapCanvas
VictimList
DecisionWorkspace
DecisionSummary
RecommendationCard
AIExplanation
DecisionActions
Timeline
VictimIntelligence
FusionIntelligence
SensorReadings
HardwareStatus
EvaluationView
```

## 15.4 Dashboard Principle

The primary operational question is:

```text
What is happening?
Where is it happening?
How certain is the system?
What should the operator do next?
```

The dashboard should prioritize these questions over raw technical telemetry.

---

# 16. Dashboard Data Architecture

The frontend shall not directly depend on MATLAB internal structures.

Instead, the system shall expose a unified mission-state payload.

Conceptual structure:

```json
{
  "mission": {},
  "probe": {},
  "sensors": {},
  "fusion": {},
  "localization": {},
  "victims": [],
  "ai": {},
  "rescuePriority": [],
  "hardware": {},
  "timeline": []
}
```

This payload shall be serialized using a documented schema.

The exact contract will be defined in:

`05_Interface_Contracts.md`

---

# 17. Technology Boundaries

The system contains several technology domains.

## 17.1 MATLAB

MATLAB is responsible for the core simulation and algorithmic pipeline, including:

- scenario generation;
- mission control;
- sensor simulation;
- signal processing;
- HAIF;
- fusion;
- confidence;
- evidence map;
- localization;
- candidate tracking;
- vitality;
- decision logic;
- experiment generation;
- and regression testing.

## 17.2 Python

Python is responsible for the AI pipeline, including:

- candidate dataset ingestion;
- preprocessing;
- classifier training;
- model validation;
- calibration;
- uncertainty estimation;
- abstention policy;
- explainability;
- sealed-test evaluation;
- model artifact generation;
- and runtime AI inference.

## 17.3 Microcontroller

The microcontroller is responsible for:

- sensor acquisition;
- low-level status collection;
- packet construction;
- data transport;
- and hardware communication.

## 17.4 Web Frontend

The React/TypeScript/Vite frontend is responsible for:

- mission visualization;
- user interaction;
- decision presentation;
- system telemetry;
- hardware visibility;
- and evaluation presentation.

---

# 18. Integration Architecture

Integration shall occur through explicit boundaries.

```text
MATLAB Core
    ↓
Candidate / Mission Data Export
    ↓
Python AI
    ↓
AI Output
    ↓
Unified Mission State
    ↓
Dashboard
```

For HIL:

```text
Microcontroller
    ↓
Acquisition Adapter
    ↓
Common SensorObservation
    ↓
MATLAB Core
    ↓
AI Layer
    ↓
Dashboard
```

---

# 19. Integration Strategy

The team shall not wait for all modules to be completed before integration.

Integration shall be incremental.

## Integration Stage 1

```text
Scenario → Sensor Output
```

## Integration Stage 2

```text
Scenario → Sensors → Fusion
```

## Integration Stage 3

```text
Scenario → Sensors → Fusion → Localization
```

## Integration Stage 4

```text
Localization → Candidate Tracking
```

## Integration Stage 5

```text
Candidate Tracking → AI
```

## Integration Stage 6

```text
AI → Vitality → Rescue Priority
```

## Integration Stage 7

```text
Core Pipeline → Dashboard
```

## Integration Stage 8

```text
Microcontroller → Common Interface → Full Pipeline
```

---

# 20. Parallel Development Strategy

Parallel work is enabled through mocks.

Each downstream team member shall be able to work before upstream components are complete.

Examples:

```text
Sensor Module
→ produces mock SensorObservation

Fusion Module
→ consumes mock SensorObservation

Localization Module
→ consumes mock FusionOutput

AI Module
→ consumes mock CandidateFeatureRecord

Dashboard
→ consumes mock MissionState JSON
```

Mocks must follow the same contracts as production data.

---

# 21. Suggested Repository Structure

The final repository should evolve toward a structure similar to:

```text
USAR/
│
├── matlab/
│   ├── simulation/
│   ├── mission/
│   ├── sensors/
│   ├── processing/
│   ├── fusion/
│   ├── localization/
│   ├── tracking/
│   ├── vitality/
│   ├── decision/
│   ├── experiments/
│   └── tests/
│
├── ai/
│   ├── data/
│   ├── features/
│   ├── training/
│   ├── calibration/
│   ├── uncertainty/
│   ├── inference/
│   ├── evaluation/
│   └── tests/
│
├── embedded/
│   ├── firmware/
│   ├── protocol/
│   ├── acquisition/
│   └── tests/
│
├── dashboard/
│   ├── src/
│   ├── components/
│   ├── services/
│   ├── types/
│   └── tests/
│
├── interfaces/
│   ├── schemas/
│   ├── fixtures/
│   └── examples/
│
├── docs/
│   ├── 01_Project_Charter.md
│   ├── 02_Requirements.md
│   ├── 03_System_Architecture.md
│   ├── 04_Team_Workstreams.md
│   ├── 05_Interface_Contracts.md
│   ├── 06_Test_Strategy.md
│   └── 07_Risk_Register.md
│
└── README.md
```

The exact physical layout may differ from the current repository and should be migrated gradually rather than destructively.

---

# 22. Dependency Rules

The following architectural dependency rules shall apply:

1. Dashboard must not depend on MATLAB internal objects.
2. AI must not access ground truth during runtime inference.
3. Fusion must not depend on dashboard implementation.
4. Localization must consume fusion outputs through a defined interface.
5. Microcontroller integration must terminate at the acquisition boundary.
6. Hardware-specific code must not spread into higher-level algorithm modules.
7. Ground truth must remain accessible only to simulation, testing, and evaluation.
8. Shared schemas shall be versioned.
9. Breaking interface changes require review.
10. Every major module shall be independently testable.

---

# 23. Error and Failure Architecture

The system shall explicitly handle failures.

## 23.1 Sensor Failure

Examples:

```text
SENSOR_UNAVAILABLE
LOW_MEASUREMENT_QUALITY
SENSOR_DEGRADED
```

## 23.2 Communication Failure

Examples:

```text
SERIAL_DISCONNECTED
INVALID_PACKET
PACKET_LOSS
SCHEMA_MISMATCH
```

## 23.3 AI Failure

Examples:

```text
MODEL_UNAVAILABLE
INVALID_FEATURE_VECTOR
HIGH_UNCERTAINTY
ABSTAIN
```

## 23.4 Localization Failure

Examples:

```text
INSUFFICIENT_EVIDENCE
UNSTABLE_LOCATION
LOCALIZATION_REJECTED
```

Failures must not be silently converted into valid decisions.

---

# 24. Logging and Audit Architecture

The system shall maintain logs across the pipeline.

Recommended logical logs:

```text
mission.log
sensor.log
fusion.log
localization.log
candidate.log
ai.log
decision.log
hardware.log
evaluation.log
```

Each log entry should include:

- timestamp;
- mission ID;
- component;
- event type;
- relevant entity ID;
- and message or structured payload.

---

# 25. Testing Architecture

Testing is distributed across layers.

## 25.1 Unit Tests

Each component shall have local tests.

## 25.2 Interface Tests

Schemas and adapters shall be validated.

## 25.3 Integration Tests

Major subsystem boundaries shall be exercised.

## 25.4 Regression Tests

Previously validated MATLAB behavior shall remain protected.

## 25.5 AI Tests

AI tests shall include:

- schema validation;
- no-oracle checks;
- split integrity;
- training reproducibility;
- calibration checks;
- inference checks;
- abstention checks.

## 25.6 HIL Tests

The acquisition path shall be tested for:

- connection loss;
- malformed packets;
- packet loss;
- delayed packets;
- sensor dropout.

## 25.7 End-to-End Tests

The full pipeline shall be tested from mission input to dashboard output.

---

# 26. Security and Safety Boundary

The project is a research and engineering prototype.

The architecture shall:

- avoid unnecessary remote-control interfaces;
- validate incoming hardware data;
- avoid presenting uncertain outputs as facts;
- preserve AI abstention;
- clearly distinguish operational output from evaluation ground truth;
- and keep a human operator in the final decision loop.

---

# 27. Deployment View

A practical prototype deployment may consist of:

```text
┌──────────────────────────┐
│ Microcontroller          │
│ Sensors / Emulated Input │
└─────────────┬────────────┘
              │ Serial/USB
              ↓
┌──────────────────────────┐
│ Host Computer            │
│                          │
│ MATLAB Core              │
│ Python AI                │
│ Integration Layer        │
└─────────────┬────────────┘
              │ Local API / File / Stream
              ↓
┌──────────────────────────┐
│ Web Dashboard            │
│ React + TypeScript       │
└──────────────────────────┘
```

For Simulation Mode, the microcontroller path can be bypassed.

---

# 28. Architectural Decisions

The following decisions are considered current project decisions.

### AD-01 — Common Observation Contract
Simulation and HIL data converge into the same internal schema.

### AD-02 — AI Above Deterministic Pipeline
AI validates victim candidates instead of replacing the entire sensing and fusion pipeline.

### AD-03 — Explicit Uncertainty
AI outputs include uncertainty and abstention.

### AD-04 — Ground Truth Firewall
Ground truth remains isolated from operational logic.

### AD-05 — Dashboard as Product Layer
The dashboard consumes standardized outputs and does not implement scientific algorithms.

### AD-06 — Microcontroller as Gateway
The microcontroller handles acquisition and transport, not high-level rescue intelligence.

### AD-07 — Incremental Integration
Modules are integrated continuously, not only at project completion.

---

# 29. Architecture Acceptance Criteria

This architecture is considered acceptable when:

1. all major system modules have explicit responsibilities;
2. simulation and HIL modes converge into a common processing interface;
3. MATLAB, Python AI, microcontroller, and dashboard boundaries are clear;
4. ground truth cannot leak into runtime decision logic;
5. all major module inputs and outputs can be defined as contracts;
6. downstream modules can be developed using mocks;
7. team workstreams can be assigned without excessive blocking dependencies;
8. the architecture supports end-to-end testing;
9. the dashboard can consume a unified system state;
10. AI uncertainty and abstention remain first-class outputs.

---

# 30. Next Documentation Step

After this architecture is approved, create:

`04_Team_Workstreams.md`

That document will define the five-member team structure, including:

- workstream ownership;
- responsibilities;
- module boundaries;
- deliverables;
- reviewer relationships;
- dependencies;
- parallel-development rules;
- and Definition of Done for each workstream.

After team ownership is defined, create:

`05_Interface_Contracts.md`

to freeze the data contracts that allow all five members to work in parallel.
