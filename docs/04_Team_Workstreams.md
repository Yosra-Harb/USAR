USAR Intelligent Rescue System
Team Workstreams and Ownership Plan
Document: 04_Team_Workstreams.md
Project: USAR Intelligent Rescue System
Team Size: 5 Members
Delivery Model: Hybrid Agile
Primary Goal: Balanced ownership with maximum parallel development and controlled integration  
1. Purpose
This document defines how the USAR Intelligent Rescue System is divided across five team members.
The allocation is designed to achieve the following goals:
- distribute technical workload as evenly as practical;
- allow all five members to work in parallel from the beginning;
- avoid assigning one member only documentation or coordination work;
- preserve clear technical ownership;
- reduce blocking dependencies;
- require peer review across workstreams;
- maintain continuous integration;
- and ensure that the final result is one integrated system rather than five isolated student projects.
The project architecture is intentionally interface-driven so every workstream can develop against mocks, fixtures, or recorded data before upstream modules are fully available.
2. Team Structure
The project is divided into five primary technical workstreams:
1. Simulation and Mission Systems
2. Sensors, Embedded Integration, and Signal Processing
3. Adaptive Fusion and Reliability Intelligence
4. Localization, Victim Tracking, and Rescue Decision
5. AI Intelligence and Operational Dashboard
Each member owns one primary workstream.
Every workstream includes:
- implementation;
- testing;
- documentation;
- integration support;
- code review;
- and evidence of completion.
No member is assigned only project-management or documentation responsibilities.
3. Workload Balancing Principle
Workload balance shall be evaluated based on:
- algorithmic complexity;
- implementation effort;
- integration difficulty;
- testing burden;
- debugging risk;
- experimental responsibility;
- and expected maintenance effort.
The project shall not be considered balanced merely because each person receives the same number of tasks.
The team should periodically review actual workload during sprint planning and redistribute secondary tasks when one workstream becomes significantly heavier.
4. Workstream 1 — Simulation and Mission Systems
Owner
Team Member 1
Role Title
Simulation and Mission Systems Engineer
Mission
Build and maintain the rescue simulation environment and mission-control infrastructure used by the rest of the system.
Core Responsibilities
4.1 Scenario Generation
Implement and maintain scenario generation for:
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
- victim position;
- vital strength;
- sensor-degradation settings;
- structural obstacles;
- random seed.
4.2 Ground Truth
Maintain simulation-only ground truth including:
- true victim count;
- true victim coordinates;
- victim identity;
- scenario configuration;
- simulated condition metadata.
Ground truth must not leak into operational algorithms.
4.3 Probe Model
Implement and maintain:
- probe initialization;
- probe state;
- current position;
- trajectory;
- visited cells;
- coverage state.
4.4 Search Path
Implement systematic mission coverage such as:
Boustrophedon Search
and maintain:
- path history;
- visited cells;
- coverage percentage;
- next waypoint logic.
4.5 Mission Controller
Maintain mission states such as:
INITIALIZING
MOVING
SENSING
PROCESSING
DECISION
UPDATE
COMPLETED
ERROR
4.6 Simulation Events
Emit mission events required by:
- processing;
- dashboard;
- testing;
- evaluation.
Expected Core Modules
Examples:
createScenario
createProbe
missionController
runProbeSimulation
Inputs
- scenario configuration;
- seed;
- mission settings.
Outputs
- environment state;
- probe state;
- sensing context;
- mission state;
- evaluation-only ground truth.
Main Deliverables
- configurable scenario engine;
- mission controller;
- search-path implementation;
- coverage tracking;
- mission-state logging;
- scenario fixtures;
- simulation tests.
Testing Responsibilities
- deterministic seed tests;
- scenario-generation tests;
- victim-placement tests;
- mission-state transition tests;
- path-coverage tests;
- no-ground-truth-leakage tests.
Parallel Development Strategy
This workstream can begin immediately.
Other members do not need to wait for the final simulation engine because mock inputs shall be provided through shared fixtures.
5. Workstream 2 — Sensors, Embedded Integration, and Signal Processing
Owner
Team Member 2
Role Title
Sensor and Embedded Systems Engineer
Mission
Own the system input layer from physical or simulated sensor observations through validated and normalized sensing evidence.
Core Responsibilities
5.1 UWB Radar Processing
Implement or maintain processing related to:
- respiration evidence;
- heartbeat evidence;
- periodicity;
- signal strength;
- attenuation;
- burial-depth effects;
- distance falloff;
- weak vital-sign behavior.
5.2 Thermal Processing
Implement or maintain:
- thermal evidence;
- thermal contrast;
- environmental effects;
- occlusion behavior;
- normalization.
5.3 Acoustic Processing
Implement or maintain:
- acoustic filtering;
- envelope extraction;
- human-related sound evidence;
- periodicity;
- noise handling.
5.4 Signal Preprocessing
Own:
- filtering;
- normalization;
- feature extraction;
- validity checks;
- missing-value handling at acquisition level.
5.5 Sensor Availability
Determine whether a sensor is available.
5.6 Microcontroller Firmware / Acquisition
Own the microcontroller integration layer, including:
- sensor acquisition;
- packet construction;
- sequence numbering;
- device-state reporting;
- communication status.
5.7 Communication Protocol
Implement:
- Serial / USB Serial communication;
- packet parsing;
- packet validation;
- connection monitoring;
- packet-loss detection;
- malformed-packet handling.
5.8 Common Sensor Observation Adapter
Convert both hardware and simulated readings into the shared:
SensorObservation
contract.
Expected Core Modules
Examples:
signalProcessingManager
embedded/firmware
embedded/protocol
embedded/acquisition
sensorObservationAdapter
Inputs
Simulation Mode:
Scenario + Probe State
HIL Mode:
Microcontroller Packets
Outputs
Standardized sensor observations containing:
- radar evidence;
- thermal evidence;
- acoustic evidence;
- availability;
- initial quality indicators;
- timestamps;
- probe position;
- hardware status.
Main Deliverables
- three sensor-processing pipelines;
- acquisition protocol;
- microcontroller communication;
- HIL adapter;
- packet schema;
- packet validator;
- sensor-status telemetry;
- sensor test fixtures.
Testing Responsibilities
- radar-processing tests;
- thermal-processing tests;
- acoustic-processing tests;
- sensor-degradation tests;
- serial packet tests;
- malformed-packet tests;
- packet-loss tests;
- disconnect/reconnect tests;
- HIL smoke tests.
Parallel Development Strategy
The workstream begins using:
- simulated sensor values;
- protocol fixtures;
- emulated serial packets.
Physical sensing hardware is not required for initial development.
6. Workstream 3 — Adaptive Fusion and Reliability Intelligence
Owner
Team Member 3
Role Title
Multi-Sensor Fusion and Reliability Engineer
Mission
Own the system intelligence responsible for deciding how much each sensor should influence the final evidence under changing sensing conditions.
Core Responsibilities
6.1 Sensor Health
Estimate sensor operational condition independently from current measurement quality.
6.2 Measurement Quality
Estimate observation-specific quality.
A sensor may be:
Healthy sensor
+
Poor measurement
and the system must preserve this distinction.
6.3 Robust Statistical Processing
Maintain methods including:
- median;
- MAD;
- robust scaling;
- Cauchy weighting where applicable.
6.4 Sensor Agreement
Estimate agreement among:
- radar;
- thermal;
- acoustic evidence.
6.5 Adaptive Weighting
Calculate dynamic modality weights.
6.6 HAIF
Own the HAIF implementation and maintenance.
The HAIF pipeline includes concepts such as:
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
6.7 Fusion Score
Generate fused victim evidence.
6.8 Fusion Confidence
Calculate confidence using approved system factors.
6.9 Fusion Explainability
Expose:
- weights;
- quality;
- health;
- agreement;
- conflict;
- innovation;
- effective support.
Expected Core Modules
Examples:
calculateAdaptiveWeights
adaptiveFusion
confidenceEstimation
HAIF
Inputs
SensorObservation
Outputs
FusionOutput
including:
- fusion score;
- confidence;
- modality weights;
- reliability information;
- sensor agreement;
- effective support.
Main Deliverables
- baseline fusion;
- robust fusion baseline;
- HAIF;
- confidence estimation;
- reliability diagnostics;
- fusion visual telemetry;
- robustness experiments.
Testing Responsibilities
- clean-condition tests;
- attenuation tests;
- sensor-dropout tests;
- degraded-quality tests;
- one-sided anomaly tests;
- weight-response tests;
- regression tests;
- baseline-comparison tests.
Research Responsibility
This owner is the primary technical maintainer of the HAIF research contribution.
However, experimental review shall involve at least one second team member.
Parallel Development Strategy
This workstream begins using:
interfaces/fixtures/mock_sensor_observation.*
without waiting for Workstream 2 to finish.
7. Workstream 4 — Localization, Victim Tracking, and Rescue Decision
Owner
Team Member 4
Role Title
Localization and Rescue Decision Engineer
Mission
Own the conversion of fused evidence into spatial victim estimates, persistent victim tracks, vitality assessment, and rescue priority.
This workstream is intentionally not combined with dashboard development because localization is one of the most technically demanding modules in the project.
Core Responsibilities
7.1 Evidence Map
Maintain spatial evidence accumulation.
7.2 Gaussian Spatial Update
Apply spatial kernels around observations.
7.3 Evidence Decay
Maintain time-dependent evidence behavior where required.
7.4 Reliability Masking
Prevent unreliable evidence from dominating the map.
7.5 Evidence Peak Search
Identify probable victim regions.
7.6 Localization
Estimate victim coordinates using the approved method, including weighted centroid logic where applicable.
7.7 Localization Confidence
Estimate confidence associated with the location result.
7.8 Localization Acceptance
Reject unstable or insufficient localization output.
7.9 Victim Candidate Creation
Create candidates from accumulated evidence.
7.10 Candidate Association
Associate observations with existing tracks.
7.11 Candidate Merge
Merge duplicate victim hypotheses.
7.12 Victim Track Management
Maintain:
- detection count;
- independent views;
- temporal stability;
- location history;
- existence probability.
7.13 Vitality Index
Calculate the approved Vitality Index using system evidence.
7.14 Rescue Priority
Generate rescue priority for confirmed victims.
7.15 Multi-Victim Ranking
Maintain ordered rescue recommendations.
Expected Core Modules
Examples:
updateEvidenceMap
localizationManager
updateVictimDatabase
vitalityManager
decisionEngine
Inputs
- FusionOutput;
- probe position;
- observation history;
- AI validation state where required for final decision.
Outputs
- estimated victim locations;
- localization confidence;
- victim tracks;
- vitality index;
- rescue priority;
- recommendation metadata.
Main Deliverables
- Evidence Map;
- localization pipeline;
- candidate association;
- tracking;
- vitality engine;
- rescue-priority logic;
- route/recommendation metadata;
- localization evaluation.
Testing Responsibilities
- synthetic-location tests;
- spatial-evidence tests;
- localization-stability tests;
- candidate-association tests;
- duplicate-merge tests;
- multi-victim tests;
- vitality tests;
- rescue-ranking tests.
Parallel Development Strategy
This workstream begins using:
interfaces/fixtures/mock_fusion_output.*
without waiting for HAIF integration.
8. Workstream 5 — AI Intelligence and Operational Dashboard
Owner
Team Member 5
Role Title
AI and Product Intelligence Engineer
Mission
Own the learned candidate-validation layer and the operator-facing product layer.
Because this workstream contains both AI and dashboard responsibilities, supporting feature generation and scientific signals are produced by other module owners rather than duplicated here.
Part A — AI Responsibilities
8.1 Candidate Dataset Consumption
Consume the approved candidate dataset generated by the project.
8.2 Candidate Classification
Train and integrate classification for:
TRUE_TRACK
HARD_NEGATIVE
while excluding:
AMBIGUOUS_IGNORE
from primary supervised training.
8.3 AI Data Integrity
Verify:
- split integrity;
- missingness;
- leaked columns;
- oracle leakage;
- duplicate mission contamination.
8.4 Model Training
Implement reproducible model training.
8.5 Probability Calibration
Calibrate classifier probabilities.
8.6 Uncertainty Estimation
Estimate model uncertainty.
8.7 Abstention
Implement:
ACCEPT_TRUE_TRACK
REJECT_HARD_NEGATIVE
ABSTAIN
8.8 Explainability
Produce operator-facing explanations.
8.9 Auditability
Track:
- model version;
- feature version;
- probability;
- uncertainty;
- threshold;
- abstention;
- timestamp.
8.10 AI Evaluation
Evaluate:
- precision;
- recall;
- F1;
- confusion matrix;
- false positives;
- false negatives;
- calibration;
- abstention;
- selective performance.
Part B — Dashboard Responsibilities
8.11 Frontend Stack
Own implementation using:
React
TypeScript
Vite
8.12 Mission Status
Display:
- mission ID;
- mode;
- status;
- elapsed time;
- coverage;
- reliability;
- AI status;
- victim count.
8.13 Operational Map
Display:
- structure;
- probe;
- path;
- coverage;
- evidence heatmap;
- victim locations;
- selected victim;
- rescue route.
8.14 Victim Intelligence
Display:
- victim identity;
- location;
- localization confidence;
- AI probability;
- uncertainty;
- vitality;
- priority.
8.15 AI Assessment
Display:
- classification;
- probability;
- uncertainty;
- abstention;
- supporting factors;
- risk factors.
8.16 Fusion Intelligence
Visualize data supplied by Workstream 3.
8.17 Sensor Health
Visualize data supplied by Workstream 2 and Workstream 3.
8.18 Localization
Visualize data supplied by Workstream 4.
8.19 Hardware Status
Display HIL information supplied by Workstream 2.
8.20 Timeline
Display mission events.
8.21 Evaluation View
Display research and performance metrics.
Expected Dashboard Components
Examples:
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
Inputs
AI:
CandidateFeatureRecord
Dashboard:
MissionState
Outputs
AI:
AIOutput
Dashboard:
operator-facing visualization and interactions.
Main Deliverables
- AI training pipeline;
- trained model artifact;
- calibration;
- uncertainty;
- abstention;
- explainability;
- AI evaluation;
- complete dashboard;
- dashboard integration;
- UI tests.
Testing Responsibilities
AI:
- feature-schema tests;
- leakage tests;
- split tests;
- reproducibility tests;
- calibration tests;
- inference tests;
- abstention tests.
Dashboard:
- component tests;
- payload parsing;
- victim selection;
- map synchronization;
- error-state rendering;
- HIL status rendering;
- AI state rendering.
Workload Control
If this workstream becomes overloaded, secondary dashboard visualization tasks may be temporarily supported by another member.
The owner remains responsible for final integration and consistency.
9. Cross-Workstream AI Feature Ownership
AI feature generation is a shared responsibility.
The AI owner does not recreate scientific features already owned by other modules.
Feature ownership is divided as follows:
Feature Family	Primary Owner
Sensor signal features	Workstream 2
Sensor health / quality	Workstream 3
Fusion weights / confidence	Workstream 3
Localization stability	Workstream 4
Candidate temporal features	Workstream 4
Candidate dataset pipeline	Workstream 5
Model preprocessing	Workstream 5
Calibration / uncertainty	Workstream 5


This prevents duplication and improves traceability.
10. Cross-Workstream Integration Responsibilities
Integration is a team responsibility.
No workstream may state:
"My module works, so my work is complete."
A module is complete only when it works through its approved interface with the rest of the system.
11. Reviewer Matrix
Every primary owner has a designated peer reviewer.
Recommended review rotation:
Owner	Primary Reviewer
Workstream 1	Workstream 2
Workstream 2	Workstream 3
Workstream 3	Workstream 4
Workstream 4	Workstream 5
Workstream 5	Workstream 1


A second reviewer may be requested for high-risk changes.
12. Technical Lead Role
One team member may act as:
Technical Lead / Delivery Coordinator
This role is additional to their technical workstream.
The Technical Lead is responsible for:
- architecture consistency;
- sprint-goal alignment;
- dependency resolution;
- integration planning;
- interface-change approval;
- risk escalation;
- ensuring tests are run;
- and coordinating final system builds.
The Technical Lead shall not act as the sole decision-maker for all technical work.
Major architectural changes should be reviewed by the team.
13. Parallel Development Model
All five members begin work in parallel.
Workstream 1
Uses the real simulation engine.
Workstream 2
Uses simulated sensor conditions and protocol fixtures.
Workstream 3
Uses mock SensorObservation.
Workstream 4
Uses mock FusionOutput.
Workstream 5
Uses:
- mock CandidateFeatureRecord;
- mock MissionState.
Therefore:
No team member waits for another member to finish the full module.
14. Shared Fixtures
The repository shall contain:
interfaces/fixtures/
with representative test data.
Recommended fixtures:
mock_sensor_observation.json
mock_fusion_output.json
mock_candidate_features.csv
mock_ai_output.json
mock_victim_track.json
mock_mission_state.json
mock_hardware_packet.json
Every fixture shall conform to the same schema used by production modules.
15. Shared Interface Contracts
The five workstreams are connected through these major contracts:
ScenarioContext
SensorObservation
FusionOutput
LocalizationOutput
VictimTrack
CandidateFeatureRecord
AIOutput
RescueDecision
HardwareStatus
MissionState
Detailed field definitions will be created in:
05_Interface_Contracts.md
16. Definition of Done — All Workstreams
A task or feature is not considered Done merely because code has been written.
A work item is Done only when applicable criteria are satisfied:
1. implementation completed;
2. local tests pass;
3. interface contract respected;
4. code committed;
5. pull request created;
6. peer review completed;
7. integration tests pass;
8. regression tests remain green;
9. documentation updated;
10. Jira issue updated;
11. acceptance criteria demonstrated.
17. Git Workflow
The team shall not develop directly on main for normal feature work.
Recommended flow:
main
  ↑
develop
  ↑
feature/*
Examples:
feature/scenario-engine
feature/radar-processing
feature/haif-fusion
feature/evidence-map
feature/ai-calibration
feature/dashboard-map
feature/microcontroller-protocol
Typical workflow:
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
18. Commit Convention
Recommended commit examples:
feat: add radar attenuation model
feat: implement candidate abstention
fix: correct localization centroid calculation
test: add sensor dropout regression
docs: update sensor observation contract
refactor: isolate acquisition adapter
Commits should describe meaningful changes.
19. Jira Ownership
Each implementation issue shall contain at least:
- Summary;
- Description;
- Requirement references;
- Assignee;
- Reviewer;
- Priority;
- Acceptance criteria;
- Dependencies;
- Sprint;
- Definition of Done.
Example:
Story:
Implement Hardware Packet Validation

Owner:
Workstream 2

Reviewer:
Workstream 3

Requirements:
FR-019
FR-020
NFR-019

Acceptance:
- malformed packets rejected
- missing sequence detected
- valid packets converted to SensorObservation
- tests pass
20. Recommended Jira Epics
Initial epics should include:
EPIC 1 — Project Foundation
EPIC 2 — Simulation and Mission Engine
EPIC 3 — Sensor and Embedded Systems
EPIC 4 — HAIF and Adaptive Fusion
EPIC 5 — Localization and Victim Tracking
EPIC 6 — AI Decision Intelligence
EPIC 7 — Vitality and Rescue Decision
EPIC 8 — Operational Dashboard
EPIC 9 — System Integration
EPIC 10 — Testing and Validation
EPIC 11 — Final Demonstration and Documentation
21. Sprint Structure
Recommended sprint duration:
1–2 weeks
Each sprint shall have one shared system-level goal.
Bad sprint goal:
Everyone completes their assigned tasks.
Preferred sprint goal:
The integrated system can propagate one complete sensor observation
from acquisition through fusion and expose the result through the
shared interface.
22. Integration Cadence
At least one integration checkpoint shall occur during each sprint.
Recommended cadence:
Development
    ↓
Mid-Sprint Integration Check
    ↓
Development / Fixes
    ↓
End-of-Sprint Integrated Build
Integration issues shall be treated as project work, not as one member's private problem.
23. Suggested Early Sprint Allocation
Sprint 1 — Interface-Ready Foundations
Workstream 1
- scenario configuration;
- mission skeleton;
- probe fixture;
- ScenarioContext mock.
Workstream 2
- sensor input schemas;
- microcontroller packet draft;
- radar / thermal / acoustic processing skeleton;
- acquisition mock.
Workstream 3
- SensorObservation consumer;
- baseline fusion;
- HAIF module skeleton;
- FusionOutput fixture.
Workstream 4
- evidence-map skeleton;
- localization interface;
- VictimTrack fixture;
- localization tests.
Workstream 5
- AI project structure;
- CandidateFeatureRecord loader;
- dashboard project skeleton;
- MissionState mock renderer.
Sprint 1 Shared Goal
All five workstreams can execute independently against agreed mock interfaces.
24. Suggested Integration Sprint
A later integration sprint should target:
Simulation
    ↓
Sensors
    ↓
Fusion
    ↓
Localization
    ↓
Victim Track
    ↓
AI
    ↓
Vitality
    ↓
Rescue Priority
    ↓
Dashboard
The HIL path is then connected through the same acquisition interface.
25. Workstream Dependency Matrix
Workstream	Main Upstream Dependency	Can Start Without It?
1 Simulation	None	Yes
2 Sensors / Embedded	Scenario interface	Yes
3 Fusion	SensorObservation	Yes, with mock
4 Localization / Decision	FusionOutput	Yes, with mock
5 AI / Dashboard	Candidate + MissionState	Yes, with mocks


Therefore all five workstreams can start in parallel.
26. Workload Rebalancing Rules
During sprint planning, the team should review:
- open issue count;
- technical risk;
- blocked tasks;
- testing burden;
- bug count;
- integration burden.
If one workstream becomes overloaded:
- secondary tasks may be reassigned;
- testing can be shared;
- dashboard visual components can be delegated;
- experiment automation can be shared;
- documentation support can be redistributed.
Core algorithm ownership should remain stable unless there is a clear reason to change it.
27. Team Communication
Recommended recurring communication:
Short Daily Check-In
Each member answers:
What did I complete?
What am I doing next?
What is blocking me?
Did I change any shared interface?
Sprint Planning
Define:
- Sprint Goal;
- stories;
- owners;
- dependencies;
- risks.
Integration Review
Review:
- interface mismatches;
- failed tests;
- integration blockers;
- schema changes.
Sprint Review
Demonstrate the integrated increment.
Retrospective
Discuss:
- what worked;
- what did not;
- what should change next sprint.
28. Interface Change Policy
Shared interfaces shall not be changed silently.
Any breaking change to:
SensorObservation
FusionOutput
LocalizationOutput
VictimTrack
AIOutput
MissionState
must include:
1. proposed change;
2. reason;
3. affected workstreams;
4. reviewer approval;
5. updated schema;
6. updated fixtures;
7. updated tests.
29. Ownership Does Not Mean Isolation
An owner is responsible for ensuring a module succeeds.
Ownership does not mean:
Only this person may understand or edit the module.
At least one reviewer should understand each critical module.
This reduces single-person dependency and improves maintainability.
30. Final Ownership Summary
Team Member 1
Simulation and Mission Systems Engineer
Owns:
Simulation
Scenario Engine
Probe
Search Path
Mission Controller
Ground Truth Firewall
Team Member 2
Sensor and Embedded Systems Engineer
Owns:
UWB
Thermal
Acoustic
Signal Processing
Microcontroller
Serial Communication
Acquisition
Team Member 3
Multi-Sensor Fusion and Reliability Engineer
Owns:
Sensor Health
Measurement Quality
HAIF
Adaptive Weights
Fusion
Confidence
Reliability Diagnostics
Team Member 4
Localization and Rescue Decision Engineer
Owns:
Evidence Map
Localization
Candidate Association
Victim Tracking
Vitality
Rescue Priority
Team Member 5
AI and Product Intelligence Engineer
Owns:
Candidate Classifier
Calibration
Uncertainty
Abstention
Explainability
AI Evaluation
Operational Dashboard
31. Approval Criteria for Team Division
This workstream division is approved when:
1. each member has one clear primary technical ownership area;
2. all members can begin work in parallel;
3. no workstream depends entirely on unfinished upstream code;
4. dashboard work is treated as a full product responsibility;
5. localization remains a dedicated heavy technical responsibility;
6. embedded integration is represented explicitly;
7. AI is represented as a complete pipeline rather than a single classifier;
8. workload is reviewed after each sprint;
9. peer review exists across workstreams;
10. integration remains a shared responsibility.
