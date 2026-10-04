# USAR Intelligent Rescue System
## Interface Contracts Specification

**Document:** `05_Interface_Contracts.md`  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Purpose:** Define stable data contracts that allow all workstreams to develop in parallel  
**Architecture Principle:** Interface-Driven, Versioned, Mockable, Testable  

---

# 1. Purpose

This document defines the data contracts exchanged between major modules of the USAR Intelligent Rescue System.

These contracts allow the five workstreams to develop independently using mocks or fixtures while preserving compatibility during integration.

The microcontroller and Hardware-in-the-Loop path are core scope. Simulation and HIL shall converge into the same downstream processing pipeline through `SensorObservation` wherever practical.

The main shared contracts are:

1. `ScenarioContext`
2. `HardwarePacket`
3. `SensorObservation`
4. `FusionOutput`
5. `LocalizationOutput`
6. `VictimTrack`
7. `CandidateFeatureRecord`
8. `AIOutput`
9. `RescueDecision`
10. `HardwareStatus`
11. `MissionState`
12. `MissionEvent`

Each contract includes:

- ownership;
- producer;
- consumer;
- required fields;
- optional fields;
- validation rules;
- example payload;
- versioning policy.

---

# 2. General Contract Rules

## 2.1 Schema Version

Every top-level payload shall include:

```text
schemaVersion
```

Example:

```json
{
  "schemaVersion": "1.0"
}
```

## 2.2 Mission Identifier

Operational payloads shall include:

```text
missionId
```

where applicable.

## 2.3 Timestamp

Timestamps shall use one documented convention.

Recommended format:

```text
Unix epoch milliseconds
```

Example:

```json
"timestamp": 1730000012345
```

## 2.4 Units

Units shall be explicit and consistent.

Recommended conventions:

- distance: meters;
- time: seconds or milliseconds;
- probability/confidence: `[0, 1]`;
- normalized scores: `[0, 1]`;
- coordinates: meters in local mission frame.

## 2.5 Missing Values

Missing data shall not silently use arbitrary defaults.

Allowed patterns:

```text
null
```

or explicit status fields such as:

```text
available = false
```

## 2.6 Enumerations

Shared states shall use explicit enumerated strings rather than free-form text.

## 2.7 Backward Compatibility

Non-breaking fields may be added within a minor version.

Breaking changes require a major schema-version increment.

```text
1.0 → 1.1   non-breaking
1.x → 2.0   breaking
```

## 2.8 Source Independence

After creation of a valid `SensorObservation`, downstream processing shall not require separate algorithm implementations for Simulation Mode and HIL Mode.

---

# 3. Contract Ownership Matrix

| Contract | Primary Producer / Owner | Primary Consumer(s) |
|---|---|---|
| `ScenarioContext` | Workstream 1 | Workstream 2, Evaluation |
| `HardwarePacket` | Workstream 2 | Workstream 2 Acquisition Adapter |
| `SensorObservation` | Workstream 2 | Workstream 3 |
| `FusionOutput` | Workstream 3 | Workstream 3 Localization, Workstream 5, Evaluation |
| `LocalizationOutput` | Workstream 3 | Workstream 4, Workstream 5, Evaluation |
| `VictimTrack` | Workstream 4 | Workstream 4 AI/Decision, Workstream 5 |
| `CandidateFeatureRecord` | Workstream 4, using approved features from WS2–WS4 | Workstream 4 AI Layer |
| `AIOutput` | Workstream 4 | Workstream 4 Decision, Workstream 5 |
| `RescueDecision` | Workstream 4 | Workstream 5 |
| `HardwareStatus` | Workstream 2 | Workstream 5 |
| `MissionState` | Workstream 5 Integration Layer | Workstream 5 Dashboard |
| `MissionEvent` | Any major module | Workstream 5 Timeline / Logging, Evaluation |

---

# 4. ScenarioContext

## 4.1 Purpose

Defines the current simulated rescue environment and mission configuration.

## 4.2 Producer

Workstream 1.

## 4.3 Consumers

- Workstream 2;
- Evaluation;
- Testing.

## 4.4 Required Fields

```text
schemaVersion
missionId
scenarioType
seed
timestamp
environment
probe
```

## 4.5 Environment Fields

```text
debrisDensity
noiseLevel
burialDepth
victimCount
vitalStrength
```

Optional:

```text
occlusionLevel
structuralComplexity
sensorDegradationProfile
```

## 4.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "scenarioType": "DENSE_DEBRIS",
  "seed": 284,
  "timestamp": 1730000012345,
  "environment": {
    "debrisDensity": 0.75,
    "noiseLevel": 0.60,
    "burialDepth": 0.80,
    "victimCount": 5,
    "vitalStrength": 0.30,
    "occlusionLevel": 0.65
  },
  "probe": {
    "x": 3.2,
    "y": 5.8,
    "state": "MOVING"
  }
}
```

---

# 5. HardwarePacket

## 5.1 Purpose

Defines the raw packet transmitted from the microcontroller to the host acquisition layer.

This is a core HIL contract.

## 5.2 Producer

Workstream 2 microcontroller / embedded acquisition path.

## 5.3 Consumer

Workstream 2 Acquisition Adapter.

## 5.4 Required Fields

```text
schemaVersion
sequence
timestamp
deviceId
sensorData
status
```

## 5.5 Example

```json
{
  "schemaVersion": "1.0",
  "sequence": 1042,
  "timestamp": 1730000012345,
  "deviceId": "MCU-01",
  "sensorData": {
    "radar": 0.74,
    "thermal": 0.63,
    "acoustic": 0.28
  },
  "status": {
    "radarAvailable": true,
    "thermalAvailable": true,
    "acousticAvailable": true
  }
}
```

## 5.6 Validation Rules

- `sequence` shall be monotonic where applicable;
- required sensor fields shall exist or be explicitly unavailable;
- malformed packets shall be rejected;
- missing sequence numbers shall be counted as packet loss;
- duplicate packets shall be detectable;
- delayed packets shall not silently overwrite newer state;
- unsupported schema versions shall not be silently accepted.

---

# 6. SensorObservation

## 6.1 Purpose

Defines the normalized internal representation of sensor evidence.

This is the key convergence boundary:

```text
Simulation Sensor Path ─┐
                        ├─→ SensorObservation → HAIF / Localization
HardwarePacket Path ────┘
```

## 6.2 Producer

Workstream 2.

The producer may use either:

- simulation inputs derived from `ScenarioContext`;
- validated `HardwarePacket` data.

## 6.3 Consumer

Workstream 3.

## 6.4 Required Fields

```text
schemaVersion
missionId
sequence
timestamp
sourceMode
probe
radar
thermal
acoustic
```

## 6.5 Sensor Substructure

Each modality should expose:

```text
available
rawScore
health
quality
```

Optional:

```text
signalStrength
periodicity
snr
diagnostics
```

The exact ownership of health/quality computation may evolve internally, but the contract fields remain stable. Workstream 3 is the scientific owner of HAIF health/quality logic; Workstream 2 may provide acquisition-level indicators and raw diagnostics.

## 6.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "sequence": 1042,
  "timestamp": 1730000012345,
  "sourceMode": "HIL",
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

## 6.7 Validation Rules

- all normalized scores in `[0,1]`;
- all confidence-like values in `[0,1]`;
- unavailable sensors shall not contribute operationally;
- probe coordinates shall use the mission coordinate convention;
- `sourceMode` shall be one of:

```text
SIMULATION
HIL
```

---

# 7. FusionOutput

## 7.1 Purpose

Defines the output of the adaptive fusion and reliability layer.

## 7.2 Producer

Workstream 3.

## 7.3 Consumers

- Workstream 3 Localization;
- Workstream 5 Dashboard/Backend;
- Evaluation.

## 7.4 Required Fields

```text
schemaVersion
missionId
timestamp
fusionScore
confidence
weights
effectiveSupport
sensorAgreement
```

## 7.5 Optional Fields

```text
conflict
innovation
qualityMemory
anomalyFlags
diagnostics
```

## 7.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "timestamp": 1730000012345,
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
  "sensorAgreement": 0.79,
  "anomalyFlags": {
    "radar": false,
    "thermal": false,
    "acoustic": true
  }
}
```

## 7.7 Validation Rules

- `fusionScore` in `[0,1]`;
- `confidence` in `[0,1]`;
- all weights `>= 0`;
- active weights should sum approximately to `1`;
- unavailable sensors shall have zero operational weight.

---

# 8. LocalizationOutput

## 8.1 Purpose

Defines the spatial estimate produced by Workstream 3 localization.

## 8.2 Producer

Workstream 3.

## 8.3 Consumers

- Workstream 4 Victim Tracking / AI;
- Workstream 5 Dashboard/Backend;
- Evaluation.

## 8.4 Required Fields

```text
schemaVersion
missionId
timestamp
accepted
estimatedPosition
localizationConfidence
evidencePeak
```

## 8.5 Example — Accepted

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "timestamp": 1730000012345,
  "accepted": true,
  "estimatedPosition": {
    "x": 12.4,
    "y": 6.7
  },
  "localizationConfidence": 0.91,
  "evidencePeak": 0.87,
  "reason": "SUFFICIENT_SPATIAL_SUPPORT"
}
```

## 8.6 Example — Rejected

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "timestamp": 1730000012345,
  "accepted": false,
  "estimatedPosition": null,
  "localizationConfidence": 0.31,
  "evidencePeak": 0.22,
  "reason": "INSUFFICIENT_EVIDENCE"
}
```

## 8.7 Validation Rules

- confidence in `[0,1]`;
- accepted results require a valid estimated position;
- rejected results may use `null` position;
- reason shall use a documented enum/string convention.

---

# 9. VictimTrack

## 9.1 Purpose

Defines the persistent operational representation of a probable or confirmed victim.

## 9.2 Producer

Workstream 4.

## 9.3 Consumers

- Workstream 4 AI / Vitality / Rescue Decision;
- Workstream 5 Dashboard/Backend.

## 9.4 Required Fields

```text
schemaVersion
missionId
victimTrackId
status
estimatedPosition
localizationConfidence
detectionCount
independentViews
existenceProbability
temporalStability
```

## 9.5 Supported Status Values

```text
NEW
OBSERVED
SUPPORTED
AI_PENDING
CONFIRMED
REJECTED
ABSTAINED
```

## 9.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "victimTrackId": "VT-003",
  "status": "AI_PENDING",
  "estimatedPosition": {
    "x": 12.4,
    "y": 6.7
  },
  "localizationConfidence": 0.91,
  "detectionCount": 6,
  "independentViews": 4,
  "existenceProbability": 0.93,
  "temporalStability": 0.88
}
```

---

# 10. CandidateFeatureRecord

## 10.1 Purpose

Defines the candidate-level feature vector consumed by the AI classifier.

## 10.2 Owner / Assembler

Workstream 4 owns the final AI feature schema and assembly.

Feature values may originate from:

- Workstream 2 sensor processing;
- Workstream 3 health/quality/fusion/localization;
- Workstream 4 tracking history.

## 10.3 Required Metadata

```text
schemaVersion
missionId
candidateId
featureVersion
timestamp
```

## 10.4 Approved Feature Families

### Sensor Features

```text
radarQuality
thermalQuality
acousticQuality
radarHealth
thermalHealth
acousticHealth
```

### Fusion Features

```text
fusionScore
fusionConfidence
radarWeight
thermalWeight
acousticWeight
sensorAgreement
```

### Tracking Features

```text
updateCount
independentViews
existenceProbability
temporalStability
```

### Localization Features

```text
localizationConfidence
spatialStability
```

### Additional Approved Features

```text
logBF-related features
missingness indicators
reliability indicators
```

## 10.5 Forbidden Features

The AI feature record shall not include:

- true victim label as an input feature;
- true victim coordinates;
- distance to ground-truth victim;
- future mission outcomes;
- any field derived directly from oracle information.

## 10.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "candidateId": "C17",
  "featureVersion": "4.0",
  "timestamp": 1730000012345,
  "features": {
    "fusionScore": 0.78,
    "fusionConfidence": 0.86,
    "radarWeight": 0.44,
    "thermalWeight": 0.38,
    "acousticWeight": 0.18,
    "sensorAgreement": 0.79,
    "existenceProbability": 0.93,
    "updateCount": 6,
    "independentViews": 4,
    "localizationConfidence": 0.91,
    "temporalStability": 0.88
  }
}
```

---

# 11. AIOutput

## 11.1 Purpose

Defines the runtime output of the AI decision-support layer.

## 11.2 Producer

Workstream 4.

## 11.3 Consumers

- Workstream 4 Rescue Decision;
- Workstream 5 Backend/Dashboard;
- Audit Log.

## 11.4 Required Fields

```text
schemaVersion
missionId
candidateId
classification
probability
uncertainty
decision
abstained
modelVersion
featureVersion
timestamp
```

## 11.5 Allowed Classification Values

```text
TRUE_TRACK
HARD_NEGATIVE
```

## 11.6 Allowed Decision Values

```text
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
```

## 11.7 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "candidateId": "C17",
  "classification": "TRUE_TRACK",
  "probability": 0.94,
  "uncertainty": 0.08,
  "decision": "ACCEPT_TRUE_TRACK",
  "abstained": false,
  "modelVersion": "candidate-model-v1",
  "featureVersion": "4.0",
  "timestamp": 1730000012345,
  "explanation": {
    "supportingFactors": [
      "multi_sensor_consistency",
      "stable_localization",
      "high_existence_probability"
    ],
    "riskFactors": [
      "low_acoustic_quality"
    ]
  }
}
```

## 11.8 Abstention Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "candidateId": "C21",
  "classification": "TRUE_TRACK",
  "probability": 0.58,
  "uncertainty": 0.42,
  "decision": "ABSTAIN",
  "abstained": true,
  "modelVersion": "candidate-model-v1",
  "featureVersion": "4.0",
  "timestamp": 1730000012345,
  "explanation": {
    "supportingFactors": [
      "moderate_fusion_support"
    ],
    "riskFactors": [
      "high_uncertainty",
      "insufficient_independent_views"
    ]
  }
}
```

---

# 12. RescueDecision

## 12.1 Purpose

Defines the operational recommendation associated with a confirmed or evaluated victim.

## 12.2 Producer

Workstream 4.

## 12.3 Consumer

Workstream 5 Backend/Dashboard.

## 12.4 Required Fields

```text
schemaVersion
missionId
victimId
status
vitalityIndex
priority
recommendedAction
timestamp
```

## 12.5 Priority Values

Recommended initial values:

```text
CRITICAL
HIGH
MODERATE
LOW
UNDETERMINED
```

## 12.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "victimId": "V2",
  "status": "CONFIRMED",
  "vitalityIndex": 0.28,
  "priority": "CRITICAL",
  "recommendedAction": "RESCUE_FIRST",
  "timestamp": 1730000012345
}
```

---

# 13. HardwareStatus

## 13.1 Purpose

Defines the live status of the microcontroller and acquisition path.

## 13.2 Producer

Workstream 2.

## 13.3 Consumer

Workstream 5 Backend/Dashboard.

## 13.4 Required Fields

```text
schemaVersion
connectionState
deviceId
timestamp
```

## 13.5 Optional Fields

```text
port
packetRateHz
packetLossRate
temperatureC
supplyVoltage
lastPacketSequence
sensorStatus
```

## 13.6 Connection States

```text
CONNECTED
DISCONNECTED
DEGRADED
ERROR
```

## 13.7 Example

```json
{
  "schemaVersion": "1.0",
  "connectionState": "CONNECTED",
  "deviceId": "MCU-01",
  "timestamp": 1730000012345,
  "port": "COM3",
  "packetRateHz": 48.0,
  "packetLossRate": 0.003,
  "temperatureC": 42.0,
  "supplyVoltage": 24.1,
  "lastPacketSequence": 1042
}
```

---

# 14. MissionEvent

## 14.1 Purpose

Defines a chronological event for logs and dashboard timeline.

## 14.2 Producer

Any major module.

## 14.3 Consumers

- Workstream 5 Logging/Timeline;
- Evaluation.

## 14.4 Required Fields

```text
schemaVersion
missionId
timestamp
component
eventType
message
```

## 14.5 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "timestamp": 1730000012345,
  "component": "LOCALIZATION",
  "eventType": "VICTIM_LOCALIZED",
  "message": "Candidate C17 localized with confidence 0.91"
}
```

---

# 15. MissionState

## 15.1 Purpose

Defines the unified dashboard-facing state of the mission.

This is the main contract assembled by Workstream 5 and consumed by the frontend.

## 15.2 Producer

Workstream 5 Integration/Backend Layer.

## 15.3 Consumer

Workstream 5 Dashboard.

## 15.4 Required Top-Level Fields

```text
schemaVersion
mission
probe
sensors
fusion
localization
victims
ai
rescuePriority
hardware
timeline
```

## 15.5 Conceptual Example

```json
{
  "schemaVersion": "1.0",
  "mission": {
    "missionId": "M042",
    "mode": "HIL",
    "status": "SEARCHING",
    "elapsedSeconds": 522,
    "coverage": 0.67,
    "systemReliability": 0.82
  },
  "probe": {
    "x": 4.2,
    "y": 7.1
  },
  "sensors": {
    "radar": {
      "health": 0.92,
      "quality": 0.88
    },
    "thermal": {
      "health": 0.96,
      "quality": 0.91
    },
    "acoustic": {
      "health": 0.89,
      "quality": 0.36
    }
  },
  "fusion": {
    "fusionScore": 0.81,
    "confidence": 0.88
  },
  "localization": {
    "activeCandidate": "C17"
  },
  "victims": [
    {
      "victimId": "V2",
      "x": 12.4,
      "y": 6.7,
      "localizationConfidence": 0.91,
      "vitalityIndex": 0.28,
      "priority": "CRITICAL"
    }
  ],
  "ai": {
    "candidateId": "C17",
    "probability": 0.94,
    "uncertainty": 0.08,
    "decision": "ACCEPT_TRUE_TRACK"
  },
  "rescuePriority": [
    "V2",
    "V1",
    "V3"
  ],
  "hardware": {
    "connectionState": "CONNECTED",
    "packetRateHz": 48.0,
    "packetLossRate": 0.003
  },
  "timeline": []
}
```

---

# 16. Contract Dependency Chains

## 16.1 Simulation Mode

```text
ScenarioContext
    ↓
Simulation Sensor Path
    ↓
SensorObservation
    ↓
FusionOutput
    ↓
LocalizationOutput
    ↓
VictimTrack
    ↓
CandidateFeatureRecord
    ↓
AIOutput
    ↓
RescueDecision
    ↓
MissionState
```

## 16.2 Hardware-in-the-Loop Mode

```text
Physical / Emulated Sensors
    ↓
Microcontroller
    ↓
HardwarePacket
    ↓
Acquisition Adapter
    ↓
SensorObservation
    ↓
Same Downstream Chain
```

The microcontroller/HIL path is a required project path, not a future-only extension.

---

# 17. Mock and Fixture Policy

Every shared contract shall have at least one representative fixture.

Required initial fixtures:

```text
interfaces/fixtures/scenario_context.json
interfaces/fixtures/hardware_packet.json
interfaces/fixtures/sensor_observation.json
interfaces/fixtures/fusion_output.json
interfaces/fixtures/localization_output.json
interfaces/fixtures/victim_track.json
interfaces/fixtures/candidate_features.json
interfaces/fixtures/ai_output.json
interfaces/fixtures/rescue_decision.json
interfaces/fixtures/hardware_status.json
interfaces/fixtures/mission_state.json
```

These fixtures shall be used for:

- parallel development;
- interface tests;
- integration tests;
- frontend development;
- backend development;
- schema validation;
- regression tests;
- HIL emulation before physical sensors are available.

---

# 18. Contract Validation

Each module shall validate incoming shared data.

Validation shall cover:

- required fields;
- schema version;
- numeric range;
- enum values;
- type consistency;
- missing values;
- timestamp validity;
- identifier presence;
- source mode where applicable.

Invalid input must not silently become valid output.

---

# 19. Interface Test Requirements

Each boundary shall have at least one interface test.

Required initial tests:

```text
ScenarioContext → Sensor Layer
HardwarePacket → Acquisition Adapter
Acquisition Adapter → SensorObservation
SensorObservation → HAIF/Fusion
FusionOutput → Localization
LocalizationOutput → Victim Tracking
VictimTrack → CandidateFeatureRecord
CandidateFeatureRecord → AI
AIOutput → Rescue Decision
RescueDecision → MissionState
HardwareStatus → MissionState
MissionState → Dashboard
```

---

# 20. Error Contract

Modules should expose explicit failure states.

Recommended common structure:

```json
{
  "success": false,
  "error": {
    "code": "SCHEMA_MISMATCH",
    "message": "Expected SensorObservation schema v1.x"
  }
}
```

Recommended error codes include:

```text
INVALID_PACKET
SCHEMA_MISMATCH
MISSING_REQUIRED_FIELD
OUT_OF_RANGE
SENSOR_UNAVAILABLE
INSUFFICIENT_EVIDENCE
LOCALIZATION_REJECTED
MODEL_UNAVAILABLE
INVALID_FEATURE_VECTOR
HIGH_UNCERTAINTY
COMMUNICATION_LOSS
PACKET_LOSS
HARDWARE_DEGRADED
```

---

# 21. Interface Change Process

No shared contract shall be changed silently.

A breaking change requires:

1. Jira issue;
2. rationale;
3. affected contracts;
4. affected workstreams;
5. reviewer approval;
6. schema-version update;
7. fixture update;
8. test update;
9. migration note;
10. integration check.

---

# 22. Contract Review Ownership

Recommended primary reviewers:

| Contract | Owner | Reviewer |
|---|---|---|
| `ScenarioContext` | WS1 | WS2 |
| `HardwarePacket` | WS2 | WS3 |
| `SensorObservation` | WS2 | WS3 |
| `FusionOutput` | WS3 | WS4 |
| `LocalizationOutput` | WS3 | WS4 |
| `VictimTrack` | WS4 | WS5 |
| `CandidateFeatureRecord` | WS4 | WS3 + WS5 |
| `AIOutput` | WS4 | WS5 |
| `RescueDecision` | WS4 | WS5 |
| `HardwareStatus` | WS2 | WS5 |
| `MissionState` | WS5 | WS1 |
| `MissionEvent` | Producing WS | WS5 / Integration |

---

# 23. Sprint 0 Contract Freeze

Before Sprint 1 feature implementation begins, the team shall freeze the first working versions of:

```text
ScenarioContext v1.0
HardwarePacket v1.0
SensorObservation v1.0
FusionOutput v1.0
LocalizationOutput v1.0
VictimTrack v1.0
CandidateFeatureRecord v1.0
AIOutput v1.0
RescueDecision v1.0
HardwareStatus v1.0
MissionState v1.0
```

The freeze does not mean the schemas can never change.

It means changes become controlled rather than informal.

---

# 24. Definition of Done for an Interface

An interface is considered ready when:

1. fields are documented;
2. producer is identified;
3. consumer is identified;
4. required/optional fields are explicit;
5. validation rules are documented;
6. at least one valid fixture exists;
7. at least one invalid case exists where appropriate;
8. an interface test exists or is planned in Sprint 0;
9. schema version is assigned;
10. owner and reviewer approve it.

---

# 25. Ground-Truth Safety Rule

Operational contracts shall not expose evaluation-only ground truth during an active mission.

Forbidden operational exposure includes, unless explicitly released post-mission for evaluation:

- true victim coordinates;
- true victim count used as decision input;
- true class label used as AI input;
- distance to true victim;
- oracle-derived outcomes.

The backend/dashboard shall preserve this safety boundary.

---

# 26. Contract Acceptance Criteria

This specification is approved for Sprint 0 when:

1. the ownership matrix matches `04_Team_Workstreams.md`;
2. the microcontroller/HIL path is represented as core scope;
3. Simulation and HIL converge through `SensorObservation`;
4. AI and dashboard ownership are separated;
5. Workstream 3 owns both fusion and localization outputs;
6. Workstream 4 owns victim/AI/decision outputs;
7. Workstream 5 owns `MissionState` assembly and product-facing integration;
8. fixtures can be created for every required boundary;
9. no operational contract leaks ground truth;
10. all five workstreams can begin independently using the frozen v1.0 fixtures.
