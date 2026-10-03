# USAR Intelligent Rescue System
## Interface Contracts Specification

**Document:** 05_Interface_Contracts.md  
**Project:** USAR Intelligent Rescue System  
**Team Size:** 5 Members  
**Purpose:** Define stable data contracts that allow all workstreams to develop in parallel  
**Architecture Principle:** Interface-Driven, Versioned, Mockable, Testable  

---

# 1. Purpose

This document defines the data contracts exchanged between major modules of the USAR Intelligent Rescue System.

These contracts are critical because they allow the five workstreams to develop independently using mocks or fixtures while preserving compatibility during integration.

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

All shared contracts shall follow these rules.

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

- distance: meters
- time: seconds or milliseconds
- probability/confidence: [0, 1]
- normalized scores: [0, 1]
- coordinates: meters in local mission frame

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

Breaking changes require a major schema version increment.

Example:

```text
1.0 → 1.1   non-breaking
1.x → 2.0   breaking
```

---

# 3. Contract Ownership Matrix

| Contract | Primary Producer | Primary Consumer(s) |
|---|---|---|
| ScenarioContext | Workstream 1 | Workstream 2, Evaluation |
| HardwarePacket | Workstream 2 | Acquisition Adapter |
| SensorObservation | Workstream 2 | Workstream 3 |
| FusionOutput | Workstream 3 | Workstream 4, Dashboard |
| LocalizationOutput | Workstream 4 | Tracking, AI, Dashboard |
| VictimTrack | Workstream 4 | AI, Dashboard |
| CandidateFeatureRecord | Workstreams 2–4 / assembled by 5 | AI Layer |
| AIOutput | Workstream 5 | Workstream 4, Dashboard |
| RescueDecision | Workstream 4 | Dashboard |
| HardwareStatus | Workstream 2 | Dashboard |
| MissionState | Integration Layer | Dashboard |
| MissionEvent | Any major module | Timeline / Logging |

---

# 4. ScenarioContext

## 4.1 Purpose

Defines the current simulated rescue environment and mission configuration.

## 4.2 Producer

Workstream 1.

## 4.3 Consumers

- Workstream 2
- Evaluation
- Testing

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

## 5.2 Producer

Workstream 2.

## 5.3 Consumer

Acquisition Adapter.

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

- `sequence` must be monotonic where applicable.
- required sensor fields must exist or be explicitly unavailable.
- malformed packets shall be rejected.
- missing sequence numbers shall be counted as packet loss.
- invalid schema versions shall not be silently accepted.

---

# 6. SensorObservation

## 6.1 Purpose

Defines the normalized internal representation of sensor evidence.

This is the key boundary between:

```text
Sensors / Embedded
        ↓
Fusion / Reliability
```

## 6.2 Producer

Workstream 2.

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

## 6.6 Example

```json
{
  "schemaVersion": "1.0",
  "missionId": "M042",
  "sequence": 1042,
  "timestamp": 1730000012345,
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

## 6.7 Validation Rules

- all scores in [0, 1];
- all confidence-like values in [0, 1];
- unavailable sensors must not contribute operationally;
- probe coordinates must use mission coordinate convention;
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

- Workstream 4
- Dashboard
- Evaluation

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

- `fusionScore` in [0, 1];
- `confidence` in [0, 1];
- all weights >= 0;
- active weights should sum approximately to 1;
- unavailable sensors must have zero operational weight.

---

# 8. LocalizationOutput

## 8.1 Purpose

Defines the spatial estimate produced by the localization layer.

## 8.2 Producer

Workstream 4.

## 8.3 Consumers

- Victim Tracking
- AI feature generation
- Dashboard
- Evaluation

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

## 8.5 Example

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

## 8.6 Rejected Example

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

---

# 9. VictimTrack

## 9.1 Purpose

Defines the persistent operational representation of a probable or confirmed victim.

## 9.2 Producer

Workstream 4.

## 9.3 Consumers

- Workstream 5 AI
- Dashboard
- Rescue Decision

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

## 10.2 Producers

Feature values originate from Workstreams 2, 3, and 4.

Workstream 5 owns the final AI feature schema and assembly.

## 10.3 Required Metadata

```text
schemaVersion
missionId
candidateId
featureVersion
timestamp
```

## 10.4 Example Feature Families

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

The AI feature record must not include:

- true victim label as an input;
- true victim coordinates;
- distance to ground-truth victim;
- future mission outcomes;
- any derived field that directly leaks oracle information.

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

Workstream 5.

## 11.3 Consumers

- Workstream 4
- Dashboard
- Audit Log

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

Dashboard.

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

Dashboard.

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

- Logging
- Timeline
- Evaluation

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

This is the main contract consumed by the frontend.

## 15.2 Producer

Integration Layer.

## 15.3 Consumer

Dashboard.

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

# 16. Contract Dependency Chain

The core operational chain is:

```text
ScenarioContext
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

HIL inserts:

```text
HardwarePacket
        ↓
SensorObservation
```

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
- integration tests;
- frontend development;
- schema validation;
- regression tests.

---

# 18. Contract Validation

Each module shall validate incoming shared data.

Validation should cover:

- required fields;
- schema version;
- numeric range;
- enum values;
- type consistency;
- missing values;
- timestamp validity;
- identifier presence.

Invalid input must not silently become valid output.

---

# 19. Interface Test Requirements

Each boundary shall have at least one interface test.

Required initial tests:

```text
ScenarioContext → Sensor Layer
HardwarePacket → SensorObservation
SensorObservation → Fusion
FusionOutput → Localization
LocalizationOutput → Tracking
VictimTrack → CandidateFeatureRecord
CandidateFeatureRecord → AI
AIOutput → Decision
RescueDecision → MissionState
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
6. schema version update;
7. fixture update;
8. test update;
9. migration note;
10. integration check.

---

# 22. Contract Review Ownership

Recommended primary reviewers:

| Contract | Owner | Reviewer |
|---|---|---|
| ScenarioContext | WS1 | WS2 |
| HardwarePacket | WS2 | WS3 |
| SensorObservation | WS2 | WS3 |
| FusionOutput | WS3 | WS4 |
| LocalizationOutput | WS4 | WS5 |
| VictimTrack | WS4 | WS5 |
| CandidateFeatureRecord | WS5 | WS3 + WS4 |
| AIOutput | WS5 | WS4 |
| RescueDecision | WS4 | WS5 |
| HardwareStatus | WS2 | WS5 |
| MissionState | WS5 / Integration | WS1 |

---

# 23. Sprint 1 Contract Freeze

Before full Sprint 1 implementation begins, the team shall freeze the first working versions of:

```text
SensorObservation v1.0
FusionOutput v1.0
LocalizationOutput v1.0
VictimTrack v1.0
CandidateFeatureRecord v1.0
AIOutput v1.0
MissionState v1.0
```

The freeze does not mean the schemas can never change.

It means changes become controlled rather than informal.

---

# 24. Definition of Done for an Interface

An interface is considered ready when:

1. fields are documented;
2. required vs optional fields are clear;
3. units are documented;
4. enum values are documented;
5. validation rules exist;
6. example payload exists;
7. fixture exists;
8. producer can generate it;
9. consumer can parse it;
10. interface test passes.

---

# 25. Recommended Repository Location

Interface definitions should be stored under:

```text
interfaces/
├── schemas/
├── fixtures/
└── examples/
```

Suggested examples:

```text
interfaces/
├── schemas/
│   ├── sensor_observation.schema.json
│   ├── fusion_output.schema.json
│   ├── ai_output.schema.json
│   └── mission_state.schema.json
│
├── fixtures/
│   ├── sensor_observation.json
│   ├── fusion_output.json
│   ├── ai_output.json
│   └── mission_state.json
│
└── examples/
    └── README.md
```

---

# 26. Final Parallel-Development Rule

Each workstream shall develop against the contract rather than against another member's unfinished internal code.

The core rule is:

```text
Depend on interfaces, not implementations.
```

This rule is what allows all five team members to work in parallel.

---

# 27. Next Documentation Step

After the interface contracts are approved, create:

`06_Test_Strategy.md`

That document will define:

- unit testing;
- interface testing;
- integration testing;
- regression testing;
- AI validation testing;
- HIL testing;
- end-to-end testing;
- acceptance testing;
- test ownership;
- quality gates;
- and release criteria.
