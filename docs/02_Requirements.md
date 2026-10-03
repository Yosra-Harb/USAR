USAR Intelligent Rescue System
System Requirements Specification
Document: 02_Requirements.md
Project: USAR Intelligent Rescue System
Project Type: Computer Engineering Graduation Project
Team Size: 5 Members
Development Approach: Hybrid Agile Software Delivery
System Type: Simulation + AI + Embedded / Hardware-in-the-Loop Research Prototype  
1. Purpose
This document defines the functional and non-functional requirements of the USAR Intelligent Rescue System.
The system is designed to support search-and-rescue operations in collapsed structures by integrating:
- rescue-environment simulation;
- UWB radar sensing;
- thermal sensing;
- acoustic sensing;
- microcontroller-based data acquisition;
- signal processing;
- sensor health and measurement-quality assessment;
- adaptive multi-sensor fusion using HAIF;
- evidence-based victim localization;
- victim tracking;
- AI-based candidate validation;
- probability calibration;
- uncertainty estimation;
- AI abstention;
- explainability;
- vitality estimation;
- rescue prioritization;
- and a real-time operational dashboard.
The system shall support both simulated missions and Hardware-in-the-Loop operation using a common downstream processing pipeline.
2. System Context
The high-level system workflow is:
Collapsed Structure Scenario
        ↓
Mission / Probe Controller
        ↓
Sensor Sources
(UWB + Thermal + Acoustic)
        ↓
Microcontroller / Acquisition Interface
        ↓
Signal Processing
        ↓
Sensor Health + Measurement Quality
        ↓
HAIF Adaptive Fusion
        ↓
Evidence Map
        ↓
Victim Localization
        ↓
Victim Candidate / Track Management
        ↓
AI Candidate Validation
        ↓
Probability Calibration
        ↓
Uncertainty Estimation
        ↓
AI Abstention
        ↓
Vitality Estimation
        ↓
Rescue Prioritization
        ↓
Operational Dashboard
The system shall support two main operating modes:
1. Simulation Mode
2. Hardware-in-the-Loop Mode
Both modes shall use the same interfaces after data acquisition.
3. Functional Requirements
FR-001 — Mission Creation
The system shall allow a rescue mission to be created with a unique mission identifier.
A mission shall include:
- scenario type;
- random seed;
- environmental conditions;
- victim configuration;
- sensor configuration;
- mission state;
- and operating mode.
FR-002 — Configurable Scenario Generation
The system shall generate collapsed-structure scenarios with configurable parameters including:
- debris density;
- environmental noise;
- burial depth;
- victim count;
- victim location;
- victim vital strength;
- sensor degradation;
- structural obstacles;
- and environmental conditions.
FR-003 — Supported Scenario Types
The system shall support, at minimum:
- Ideal;
- Dense Debris;
- High Noise;
- Deep Burial;
- Multiple Victims;
- Weak Vital Signs.
The architecture shall support future addition of new scenario types without redesigning the entire processing pipeline.
FR-004 — Ground Truth Generation
The simulation environment shall maintain ground-truth information including:
- true victim count;
- true victim locations;
- victim identity;
- simulated vitality parameters;
- environmental conditions;
- and scenario metadata.
Ground truth shall be used only for evaluation and shall not be available to detection, localization, fusion, or AI decision logic.
FR-005 — Probe Initialization
The system shall initialize a search probe with:
- initial position;
- current mission state;
- search trajectory;
- visited locations;
- sensing configuration;
- and operating status.
FR-006 — Probe Search Path
The system shall support systematic search-path execution, including boustrophedon-style coverage.
The system shall maintain:
- current probe position;
- visited cells;
- unvisited cells;
- coverage path;
- and total coverage percentage.
FR-007 — Mission State Management
The mission controller shall support states including:
INITIALIZING
MOVING
SENSING
PROCESSING
DECISION
UPDATE
COMPLETED
ERROR
State transitions shall be logged.
4. Sensor and Acquisition Requirements
FR-008 — UWB Radar Input
The system shall process UWB radar-related measurements representing evidence such as:
- respiration;
- heartbeat;
- periodic motion;
- signal strength;
- and distance-dependent response.
FR-009 — Thermal Sensor Input
The system shall process thermal measurements representing possible human thermal signatures.
FR-010 — Acoustic Sensor Input
The system shall process acoustic measurements representing potential human-generated sound evidence.
FR-011 — Sensor Degradation Modeling
In Simulation Mode, the system shall model degradation effects including:
- attenuation;
- noise;
- occlusion;
- burial depth;
- distance falloff;
- weak vital signs;
- and sensor dropout.
FR-012 — Signal Preprocessing
The system shall preprocess sensor measurements before fusion.
Preprocessing may include:
- filtering;
- normalization;
- envelope extraction;
- periodicity estimation;
- noise reduction;
- feature extraction;
- and validity checks.
FR-013 — Sensor Availability
The system shall determine whether each sensor is available for the current observation.
Availability shall not be treated as equivalent to measurement quality.
FR-014 — Sensor Health
The system shall estimate the operational condition of each sensor independently from measurement quality.
FR-015 — Measurement Quality
The system shall estimate the quality of the current measurement produced by each sensor.
A sensor may be healthy while producing low-quality measurements.
5. Microcontroller and Hardware-in-the-Loop Requirements
FR-016 — Microcontroller Interface
The system shall support communication with a microcontroller-based acquisition unit.
The microcontroller shall act as an interface between physical or emulated sensing sources and the processing system.
FR-017 — Supported Acquisition Data
The microcontroller communication layer shall support transmission of:
- sensor values;
- timestamps;
- sensor identifiers;
- sensor status;
- packet sequence numbers;
- and acquisition metadata.
FR-018 — Communication Protocol
The acquisition layer shall support a defined communication protocol such as:
- serial communication;
- USB serial;
- or an equivalent supported communication channel.
The protocol shall use a documented packet format.
FR-019 — Data Packet Validation
The system shall validate incoming packets for:
- valid format;
- required fields;
- packet sequence;
- timestamp validity;
- and corrupted or incomplete data.
FR-020 — Packet-Loss Detection
The system shall detect missing or skipped packets using packet sequence information.
FR-021 — Connection Monitoring
The system shall monitor the microcontroller connection state.
Supported states shall include:
- CONNECTED;
- DISCONNECTED;
- DEGRADED;
- ERROR.
FR-022 — Hardware Status Telemetry
The system shall display hardware-related telemetry including, when available:
- connection state;
- communication port;
- packet rate;
- packet-loss rate;
- sensor availability;
- controller status;
- and power or temperature telemetry.
FR-023 — Common Processing Interface
Data received through the microcontroller shall be converted into the same internal sensor-observation schema used by Simulation Mode.
No downstream module shall require separate implementations for simulation and hardware data.
6. Multi-Sensor Fusion Requirements
FR-024 — Sensor Observation Structure
Each sensing observation shall include, where applicable:
- timestamp;
- probe position;
- radar score;
- thermal score;
- acoustic score;
- sensor availability;
- sensor health;
- measurement quality;
- and supporting features.
FR-025 — Robust Sensor Statistics
The system shall support robust statistical mechanisms including median- and MAD-based processing where applicable.
FR-026 — Adaptive Sensor Weighting
The system shall dynamically calculate sensor influence rather than relying only on fixed sensor weights.
FR-027 — Sensor Agreement
The fusion system shall estimate agreement or disagreement among sensing modalities.
FR-028 — HAIF Fusion
The system shall implement the Health-Aware / quality-aware adaptive fusion mechanism developed for the project.
The fusion mechanism shall consider factors including:
- availability;
- sensor health;
- measurement quality;
- inter-sensor conflict;
- innovation;
- quality memory;
- and anomaly indicators.
FR-029 — Quality-Gated Conflict Handling
The fusion system shall reduce the impact of conflict information when the associated measurement quality is insufficient to justify that conflict.
FR-030 — One-Sided Anomaly Handling
The system shall support one-sided anomaly handling to avoid penalizing healthy modalities solely because another modality becomes degraded or anomalous.
FR-031 — Sensor Dropout
A sensor that is unavailable shall receive no effective contribution to the fusion decision.
FR-032 — Fusion Score
The fusion layer shall produce an overall normalized fusion score.
FR-033 — Fusion Confidence
The system shall estimate fusion confidence using factors such as:
- measurement quality;
- sensor agreement;
- sensor-weight balance;
- and available evidence.
FR-034 — Fusion Explainability
The system shall expose:
- final modality weights;
- quality indicators;
- reliability indicators;
- conflict indicators;
- and relevant support values
for inspection and visualization.
7. Localization Requirements
FR-035 — Evidence Map
The system shall maintain a spatial evidence map representing accumulated victim-related evidence.
FR-036 — Spatial Evidence Update
The evidence map shall support spatial evidence spreading using a defined kernel such as a Gaussian kernel.
FR-037 — Evidence Decay
The localization layer shall support evidence decay over time where required.
FR-038 — Reliability Masking
Low-reliability observations shall be prevented from contributing excessive spatial evidence.
FR-039 — Evidence Peak Detection
The system shall identify the most probable victim-related evidence region.
FR-040 — Position Estimation
The system shall estimate victim position using a localization mechanism such as weighted centroid estimation around high-evidence regions.
FR-041 — Localization Confidence
The system shall estimate confidence associated with each victim-location estimate.
FR-042 — Localization Acceptance
The system shall reject or withhold localization output when evidence does not meet required reliability criteria.
FR-043 — Localization Performance Evaluation
In evaluation mode, the system shall calculate localization error relative to ground truth.
8. Victim Candidate and Tracking Requirements
FR-044 — Candidate Creation
The system shall create victim candidates when evidence satisfies candidate-generation conditions.
FR-045 — Candidate Association
New observations shall be associated with existing candidate tracks based on spatial and temporal criteria.
FR-046 — Candidate Merge
Candidates representing the same probable victim shall be mergeable according to defined association rules.
FR-047 — Candidate History
The system shall maintain a temporal history for each candidate.
FR-048 — Detection Count
The system shall maintain the number of supporting detections for each candidate.
FR-049 — Independent Observation Count
The system shall record supporting independent views or observations.
FR-050 — Existence Probability
Each candidate shall maintain an estimated probability or confidence that the candidate represents a real victim.
9. AI Layer Requirements
FR-051 — AI Candidate Classification
The AI layer shall classify eligible candidate tracks as:
TRUE_TRACK
HARD_NEGATIVE
FR-052 — Ambiguous Samples
Candidates labeled as ambiguous shall not be treated as standard positive or negative training samples for the primary classifier.
They shall be represented as:
AMBIGUOUS_IGNORE
FR-053 — AI Feature Input
The AI model shall use non-oracle features derived from the operational pipeline.
Features may include:
- fusion score;
- fusion confidence;
- sensor weights;
- sensor agreement;
- sensor health;
- measurement quality;
- candidate update count;
- independent observations;
- existence probability;
- temporal consistency;
- spatial stability;
- localization confidence;
- Log Bayes Factor-related features;
- and other approved non-leaking system features.
FR-054 — Oracle Leakage Prevention
Ground-truth victim labels, ground-truth coordinates, or equivalent future-information variables shall not be available as model input features.
FR-055 — AI Training Pipeline
The project shall include a reproducible AI training pipeline.
The pipeline shall support:
- dataset loading;
- preprocessing;
- feature selection;
- model training;
- validation;
- test evaluation;
- artifact saving;
- and version tracking.
FR-056 — Dataset Splitting
AI datasets shall maintain separated:
- training;
- validation;
- and sealed test
subsets.
The sealed test subset shall not be used during model development or tuning.
FR-057 — Candidate Validity Probability
The model shall output an estimated probability that a candidate is a credible victim track.
Example:
P(TRUE_TRACK) = 0.91
FR-058 — Probability Calibration
The AI pipeline shall include probability calibration so that model probabilities better reflect observed outcome frequencies.
FR-059 — Calibration Evaluation
Calibration shall be evaluated using suitable metrics and reliability analysis.
FR-060 — Uncertainty Estimation
The AI layer shall estimate uncertainty associated with predictions.
FR-061 — AI Abstention
The AI layer shall support an abstention decision when:
- model confidence is insufficient;
- uncertainty is excessive;
- evidence is incomplete;
- or the input is outside acceptable operating conditions.
FR-062 — Abstention Output
The AI decision interface shall support at least:
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
FR-063 — AI Explanation
For every non-trivial AI decision, the system shall expose relevant supporting and opposing evidence.
The explanation may include:
- strongest contributing features;
- supporting sensor evidence;
- temporal stability;
- localization stability;
- and identified risk indicators.
FR-064 — AI Auditability
AI decisions shall be traceable to:
- model version;
- feature version;
- prediction probability;
- uncertainty estimate;
- decision threshold;
- and timestamp.
FR-065 — AI Missing-Data Handling
The AI layer shall handle missing or unavailable feature values according to documented preprocessing rules.
FR-066 — AI Performance Evaluation
The AI layer shall be evaluated using appropriate classification metrics including:
- precision;
- recall;
- F1-score;
- confusion matrix;
- false positive rate;
- false negative rate;
- and calibration-related metrics.
10. Vitality and Rescue Decision Requirements
FR-067 — Vitality Index
The system shall calculate a Vitality Index using approved inputs such as:
- fusion evidence;
- fusion confidence;
- localization confidence;
- temporal stability;
- and relevant victim-track evidence.
FR-068 — Vitality Range
The Vitality Index shall be represented in a normalized and documented range.
FR-069 — Rescue Priority
The system shall assign rescue priority based on the approved rescue-priority policy.
Lower estimated vitality may indicate higher rescue urgency, subject to system confidence and decision rules.
FR-070 — Multiple-Victim Ranking
When multiple confirmed victims exist, the system shall generate an ordered rescue-priority list.
FR-071 — Decision Explainability
The rescue recommendation shall expose the factors contributing to the priority decision.
FR-072 — Human Decision Support
The system shall provide recommendations to human operators.
The system shall not claim to autonomously replace trained rescue personnel.
11. Dashboard Requirements
FR-073 — Mission Status Dashboard
The dashboard shall display:
- mission identifier;
- mission status;
- operating mode;
- elapsed time;
- coverage;
- system reliability;
- AI status;
- and detected victim count.
FR-074 — Operational Map
The dashboard shall provide an operational map displaying:
- structural environment;
- probe position;
- probe trajectory;
- visited regions;
- unvisited regions;
- evidence map;
- suspected victim regions;
- confirmed victims;
- and rescue route where available.
FR-075 — Victim Selection
Users shall be able to select a victim from:
- the operational map;
- or the victim list.
Selection shall synchronize related dashboard panels.
FR-076 — Victim Intelligence Panel
For a selected victim, the dashboard shall display:
- victim identifier;
- candidate or confirmed status;
- estimated location;
- localization confidence;
- AI probability;
- AI uncertainty;
- AI decision;
- Vitality Index;
- and rescue priority.
FR-077 — Rescue Priority Panel
The dashboard shall display the current rescue-priority ordering for confirmed victims.
FR-078 — AI Assessment Panel
The dashboard shall display:
- AI classification;
- calibrated probability;
- uncertainty;
- abstention status;
- supporting evidence;
- and risk indicators.
FR-079 — Sensor Health Panel
The dashboard shall display sensor health separately from measurement quality.
FR-080 — Fusion Intelligence Panel
The dashboard shall display:
- modality scores;
- modality weights;
- fusion score;
- confidence;
- and reliability indicators.
FR-081 — Localization Panel
The dashboard shall display:
- evidence information;
- estimated coordinates;
- localization confidence;
- and localization error in evaluation mode.
FR-082 — Vitality Panel
The dashboard shall display the components contributing to the Vitality Index.
FR-083 — Mission Timeline
The dashboard shall maintain a chronological mission event timeline.
FR-084 — Hardware Status Panel
The dashboard shall display:
- microcontroller connection;
- communication state;
- packet rate;
- packet loss;
- and available hardware telemetry.
FR-085 — Evaluation Mode
The dashboard shall support an evaluation view containing experimental metrics.
FR-086 — Ground-Truth Visibility
Ground truth shall be hidden during operational mode.
Ground truth may be shown explicitly only in evaluation or developer mode.
12. Evaluation Requirements
FR-087 — Detection Metrics
The evaluation framework shall calculate:
- Precision;
- Recall;
- F1-score;
- False Positives;
- False Negatives;
- and Critical Success Index.
FR-088 — Localization Metrics
The framework shall calculate localization error statistics.
FR-089 — Response-Time Metrics
The system shall record relevant processing and decision response times.
FR-090 — Scenario-Based Evaluation
The evaluation system shall support repeated experiments across multiple scenarios and random seeds.
FR-091 — Baseline Comparison
The system shall support comparison among approved fusion approaches, including baseline methods and HAIF.
FR-092 — Sensor-Degradation Evaluation
The system shall evaluate behavior under sensor attenuation, degradation, and dropout.
FR-093 — AI Evaluation
The evaluation framework shall report:
- candidate-classification metrics;
- calibration performance;
- uncertainty behavior;
- abstention rate;
- selective performance;
- and error characteristics.
FR-094 — Experiment Reproducibility
Each experiment shall record sufficient configuration metadata to allow reproduction.
This shall include, where applicable:
- seed;
- scenario;
- configuration;
- algorithm version;
- model version;
- thresholds;
- and dataset version.
13. Data and Logging Requirements
FR-095 — Mission Logging
The system shall log important mission events.
FR-096 — Sensor Logging
Sensor observations shall be loggable for debugging and evaluation.
FR-097 — Fusion Logging
The system shall log fusion outputs and sensor weights.
FR-098 — Localization Logging
Localization outputs shall be logged.
FR-099 — AI Logging
AI predictions shall be logged together with:
- model version;
- probability;
- uncertainty;
- abstention state;
- and explanation metadata.
FR-100 — Audit Trail
The system shall maintain an auditable sequence from raw observation to final rescue recommendation where technically feasible.
14. Non-Functional Requirements
NFR-001 — Modularity
The system shall be implemented as modular components with explicit responsibilities.
NFR-002 — Loose Coupling
Modules shall interact through documented interfaces rather than direct dependence on internal implementations.
NFR-003 — Parallel Development
The architecture shall allow team members to develop major modules in parallel using mocks or fixtures when upstream modules are unavailable.
NFR-004 — Extensibility
New sensors, models, scenarios, and AI algorithms shall be addable without redesigning the entire system.
NFR-005 — Maintainability
Code shall be organized, documented, and version controlled.
NFR-006 — Reproducibility
Simulation experiments and AI experiments shall be reproducible using stored configuration and seed information.
NFR-007 — Traceability
Major requirements shall be traceable to implementation modules, test cases, and evaluation evidence.
NFR-008 — Reliability
The system shall handle degraded or missing sensor inputs without uncontrolled failure.
NFR-009 — Graceful Degradation
Loss or degradation of one sensing modality shall not necessarily cause complete mission failure when other valid evidence remains available.
NFR-010 — Fail-Safe AI Behavior
When uncertainty exceeds approved limits, the AI layer shall prefer abstention over unsupported high-confidence decisions.
NFR-011 — Interpretability
System recommendations shall provide enough information for a human operator to understand the main basis for the recommendation.
NFR-012 — Performance
The processing pipeline shall be designed for near-real-time mission updates within the computational limits of the prototype environment.
NFR-013 — Responsiveness
The dashboard shall update mission information without requiring manual page refresh during active operation.
NFR-014 — Data Consistency
All modules shall use consistent units, timestamps, coordinate conventions, identifiers, and schema versions.
NFR-015 — Interface Versioning
Shared data interfaces shall be versioned when breaking changes occur.
NFR-016 — Testing
Each major module shall include appropriate automated tests.
NFR-017 — Regression Protection
Previously validated core behavior shall be protected by regression testing.
NFR-018 — Integration Testing
The system shall include end-to-end tests covering communication among major modules.
NFR-019 — Error Handling
Communication errors, invalid packets, missing values, and processing failures shall be handled explicitly.
NFR-020 — Security Boundary
The prototype shall avoid exposing unnecessary external control interfaces. Any future remote-control capability shall require separate security design and review.
NFR-021 — Usability
The dashboard shall prioritize operationally relevant information and avoid unnecessary visual complexity.
NFR-022 — Visual Priority
Critical rescue information shall be visually distinguishable from informational telemetry.
NFR-023 — Research Integrity
Evaluation logic shall remain isolated from operational decision logic so that ground truth cannot influence predictions.
NFR-024 — AI Data Integrity
Training and evaluation data shall be checked for:
- split overlap;
- oracle leakage;
- duplicated mission contamination;
- missingness;
- and schema inconsistencies.
NFR-025 — Documentation
Architecture, interfaces, testing procedures, and major algorithms shall be documented.
15. External Interface Requirements
15.1 Microcontroller Interface
The microcontroller interface shall expose a standardized sensor packet.
Conceptual example:
{
  "schemaVersion": "1.0",
  "sequence": 1042,
  "timestamp": 1730000012,
  "probe": {
    "x": 4.2,
    "y": 7.1
  },
  "sensors": {
    "radar": {
      "available": true,
      "value": 0.74
    },
    "thermal": {
      "available": true,
      "value": 0.63
    },
    "acoustic": {
      "available": true,
      "value": 0.28
    }
  }
}
The exact protocol shall be finalized in the Interface Contracts document.
15.2 Fusion Output Interface
Conceptual fusion output:
{
  "timestamp": 1730000012,
  "fusionScore": 0.78,
  "confidence": 0.86,
  "weights": {
    "radar": 0.44,
    "thermal": 0.38,
    "acoustic": 0.18
  },
  "sensorHealth": {
    "radar": 0.93,
    "thermal": 0.96,
    "acoustic": 0.89
  },
  "measurementQuality": {
    "radar": 0.84,
    "thermal": 0.91,
    "acoustic": 0.36
  }
}
15.3 AI Output Interface
Conceptual AI output:
{
  "candidateId": "C17",
  "classification": "TRUE_TRACK",
  "probability": 0.94,
  "uncertainty": 0.08,
  "decision": "ACCEPT_TRUE_TRACK",
  "abstained": false,
  "modelVersion": "candidate-model-v1",
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
15.4 Dashboard Mission Interface
The dashboard shall receive a unified mission-state payload containing:
- mission state;
- probe state;
- sensor state;
- fusion state;
- victim tracks;
- AI outputs;
- rescue priority;
- hardware state;
- and system events.
The exact schema shall be defined in:
05_Interface_Contracts.md
16. System Modes
16.1 Simulation Mode
Scenario Generator
        ↓
Simulated Sensor Models
        ↓
Common Sensor Observation Interface
        ↓
Core Processing Pipeline
16.2 Hardware-in-the-Loop Mode
Sensor / Emulated Input
        ↓
Microcontroller
        ↓
Communication Layer
        ↓
Common Sensor Observation Interface
        ↓
Core Processing Pipeline
The processing stages after the common observation interface shall remain identical wherever practical.
17. Safety and Decision-Support Requirements
The system is a decision-support prototype.
It shall not present itself as:
- a certified rescue device;
- an autonomous emergency authority;
- a medical diagnostic device;
- or a replacement for trained rescue operators.
AI confidence shall not be treated as proof of victim presence.
Low-confidence or high-uncertainty conditions shall be explicitly communicated.
18. Acceptance Criteria
AC-01
A complete simulated rescue mission can execute end-to-end.
AC-02
All three sensing modalities can generate or provide valid observations.
AC-03
Sensor health and measurement quality are represented separately.
AC-04
HAIF produces adaptive fusion outputs and modality weights.
AC-05
The localization pipeline estimates probable victim positions.
AC-06
Victim candidates can be created, associated, and maintained over time.
AC-07
The AI layer produces candidate classification, calibrated probability, uncertainty, and abstention output.
AC-08
No oracle ground-truth features are used in operational AI inference.
AC-09
The system produces a Vitality Index and rescue-priority ordering.
AC-10
The dashboard visualizes the complete operational pipeline.
AC-11
A microcontroller can transmit data through the acquisition interface.
AC-12
The same downstream processing pipeline accepts both simulation and Hardware-in-the-Loop input.
AC-13
The system records experimental metrics and logs.
AC-14
Automated tests verify critical module behavior.
AC-15
An end-to-end demonstration can be executed reproducibly.
19. Requirement Priority
Requirements shall use:
- MUST
- SHOULD
- COULD
- WON'T FOR CURRENT RELEASE
Detailed priority mapping will be maintained in the project backlog.
20. Traceability
Each implementation task created in Jira should reference one or more requirements.
Example:
Jira Story:
Implement AI abstention policy

Related Requirements:
FR-060
FR-061
FR-062
NFR-010
Important tests should also reference the requirement they verify.
21. Definition of Done for a Requirement
A requirement shall be considered implemented only when:
1. the implementation exists;
2. relevant tests pass;
3. the interface is documented;
4. code review is complete;
5. integration does not break existing regression tests;
6. required logs or telemetry are available;
7. relevant documentation is updated;
8. and acceptance criteria are demonstrated where applicable.
