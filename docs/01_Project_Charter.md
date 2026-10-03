# USAR Intelligent Rescue System
## Project Charter

**Project Type:** Computer Engineering Graduation Project  
**Team Size:** 5 Members  
**Project Status:** Project Foundation  
**Development Approach:** Hybrid Agile Software Delivery  
**System Type:** Simulation-Driven Research and Engineering Prototype  

---

# 1. Project Background

Urban Search and Rescue (USAR) operations in collapsed structures require rapid, reliable, and interpretable decision-making under highly uncertain environmental conditions.

Victims may be trapped beneath debris, hidden from direct visual inspection, or located in areas affected by noise, attenuation, occlusion, burial depth, structural obstacles, and changing sensing conditions.

Different sensing technologies provide complementary information but also have different limitations.

For example:

- UWB radar can provide information related to movement, respiration, and heartbeat, but its measurements can be affected by debris attenuation, burial depth, and weak vital signs.
- Thermal sensing can provide evidence of human presence but may be affected by occlusion and environmental temperature conditions.
- Acoustic sensing can detect human-generated sounds but is particularly vulnerable to environmental and structural noise.

Relying on a single sensing modality may therefore result in missed detections, false alarms, inaccurate localization, or delayed rescue decisions.

This project develops an intelligent multi-sensor search-and-rescue decision-support system that combines simulation, sensor processing, adaptive sensor fusion, victim localization, artificial intelligence, rescue-priority estimation, hardware-in-the-loop integration, and an operational dashboard.

The system is initially developed and evaluated within a controlled simulation environment while maintaining an architecture that supports future integration with physical sensing hardware.

---

# 2. Problem Statement

Urban search and rescue operations in collapsed structures are highly time-sensitive and technically challenging.

Individual sensing modalities may become unreliable because of debris, attenuation, environmental noise, occlusion, burial depth, or weak victim signals.

A major challenge is that a sensor may remain operational while the quality of its current measurements becomes poor. Treating sensor availability, sensor condition, and measurement quality as equivalent can cause unreliable sensor information to influence the final decision.

Conventional fixed-weight multi-sensor fusion approaches may therefore fail to adapt appropriately when sensing conditions change during a rescue mission.

In addition, detecting a possible victim is not sufficient. A rescue-support system must also determine:

- whether the observed evidence corresponds to a credible victim candidate;
- where the victim is likely located;
- how confident the system is in that location;
- whether the available evidence is sufficiently reliable for an automated recommendation;
- which detected victim should receive rescue priority;
- and how these decisions can be communicated clearly to rescue operators.

The project therefore addresses the need for an intelligent and interpretable search-and-rescue decision-support system capable of:

1. combining heterogeneous sensing modalities;
2. evaluating sensor reliability and measurement quality dynamically;
3. adaptively fusing available evidence;
4. detecting and localizing probable victims;
5. estimating confidence and uncertainty;
6. applying an AI layer to validate candidate victim tracks;
7. abstaining from uncertain AI decisions when evidence is insufficient;
8. estimating victim vitality and rescue priority;
9. supporting simulation and hardware-in-the-loop operation;
10. and presenting operational information through an interactive dashboard.

The resulting system is intended as a research and engineering prototype rather than a field-certified rescue system.

Future work will include validation using real sensing hardware and realistic field environments.

---

# 3. Project Objectives

## 3.1 Primary Objective

To design, implement, integrate, and evaluate an intelligent multi-sensor decision-support system for locating and prioritizing victims in collapsed structures.

## 3.2 Technical Objectives

The project aims to:

1. Develop a configurable simulation environment representing collapsed-structure rescue scenarios.

2. Generate different operational conditions including:
   - dense debris;
   - high environmental noise;
   - deep burial;
   - weak vital signs;
   - multiple victims;
   - and relatively clean sensing conditions.

3. Model multiple sensing modalities including:
   - UWB radar;
   - thermal sensing;
   - acoustic sensing.

4. Implement signal-processing mechanisms for extracting useful sensing evidence.

5. Distinguish between:
   - sensor availability;
   - sensor health;
   - and measurement quality.

6. Develop an adaptive multi-sensor fusion mechanism capable of reducing the influence of unreliable measurements.

7. Implement health-aware and quality-aware fusion using the HAIF framework.

8. Develop an evidence-based localization mechanism for estimating probable victim positions.

9. Maintain and update victim candidates and tracks across multiple observations.

10. Estimate a Vitality Index for confirmed victims.

11. Prioritize victims for rescue based on available evidence and estimated vitality.

12. Develop an AI decision-support layer for:
    - candidate validity classification;
    - probability calibration;
    - uncertainty estimation;
    - decision abstention;
    - and decision explanation.

13. Support communication with a microcontroller-based acquisition layer.

14. Support two system operating modes:
    - Simulation Mode;
    - Hardware-in-the-Loop Mode.

15. Develop an operational dashboard that presents:
    - mission status;
    - probe position and path;
    - sensor information;
    - fusion information;
    - victim locations;
    - AI decisions;
    - uncertainty;
    - vitality;
    - rescue priority;
    - and system status.

16. Evaluate the system using quantitative performance metrics.

---

# 4. Project Scope

## 4.1 In Scope

The project includes:

### Simulation

- Collapsed-structure environment simulation.
- Configurable rescue scenarios.
- Victim generation.
- Debris conditions.
- Environmental noise.
- Burial depth.
- Weak vital-sign conditions.
- Multiple-victim scenarios.
- Ground-truth generation.
- Probe search-path simulation.

### Sensor Layer

- UWB radar modeling.
- Thermal sensor modeling.
- Acoustic sensor modeling.
- Sensor degradation modeling.
- Signal preprocessing.
- Measurement normalization.
- Sensor availability monitoring.
- Measurement-quality estimation.

### Multi-Sensor Fusion

- Robust signal aggregation.
- Adaptive sensor weighting.
- Sensor agreement analysis.
- Sensor-health estimation.
- Measurement-quality estimation.
- Conflict analysis.
- Innovation analysis.
- HAIF-based fusion.
- Fusion confidence estimation.

### Localization

- Evidence-map construction.
- Evidence accumulation and decay.
- Reliable evidence masking.
- Evidence peak detection.
- Weighted localization estimation.
- Localization-confidence estimation.

### Victim Intelligence

- Victim-candidate creation.
- Candidate association.
- Victim-track maintenance.
- Temporal evidence accumulation.
- Existence-probability estimation.
- Vitality Index estimation.
- Rescue-priority determination.

### Artificial Intelligence

- Candidate validity classification.
- TRUE_TRACK classification.
- HARD_NEGATIVE classification.
- Exclusion of ambiguous samples from primary classifier training.
- Probability calibration.
- Uncertainty estimation.
- Abstention policy.
- Explainable AI outputs.

### Embedded / Hardware Integration

- Microcontroller communication interface.
- Sensor-data packet definition.
- Serial or equivalent communication.
- Data-acquisition interface.
- Hardware connection monitoring.
- Hardware-in-the-loop operation.

### Dashboard

- Mission monitoring.
- Operational map.
- Probe trajectory.
- Evidence visualization.
- Victim selection.
- Rescue-priority display.
- Sensor-health visualization.
- Fusion visualization.
- AI probability and uncertainty.
- AI explanation.
- Localization information.
- Vitality information.
- Mission timeline.
- Hardware status.
- Experimental evaluation view.

### Evaluation

The project will evaluate metrics including:

- Precision.
- Recall.
- F1-score.
- False Positives.
- False Negatives.
- Critical Success Index.
- Localization Error.
- Response Time.
- AI classification performance.
- Probability calibration.
- Abstention behavior.
- System reliability under sensor degradation.

---

# 4.2 Out of Scope

The current project does not aim to provide:

- a field-certified emergency rescue product;
- autonomous structural safety assessment;
- autonomous physical victim extraction;
- deployment in uncontrolled real disaster environments;
- certified medical diagnosis;
- certified clinical vitality assessment;
- production-grade emergency-service infrastructure.

Real-world field validation using physical sensing hardware is considered future work.

---

# 5. Stakeholders

The major project stakeholders include:

## 5.1 Project Team

The five-member engineering team responsible for:

- system design;
- development;
- testing;
- integration;
- documentation;
- evaluation;
- and presentation.

## 5.2 Academic Supervisor

Responsible for providing academic and technical guidance and reviewing project quality.

## 5.3 Graduation Evaluation Committee

Responsible for evaluating:

- engineering quality;
- technical depth;
- research contribution;
- system integration;
- validation;
- and project presentation.

## 5.4 Researchers

Potential researchers interested in:

- multi-sensor fusion;
- search and rescue;
- victim localization;
- uncertainty-aware AI;
- and resilient sensing systems.

## 5.5 Rescue Operators

Future potential users who need clear operational information such as:

- probable victim location;
- confidence;
- victim priority;
- system uncertainty;
- and recommended next actions.

## 5.6 Future Hardware Partners

Potential organizations or laboratories that may support physical sensing and real-world validation in future stages.

---

# 6. Success Criteria

The project will be considered successful when the integrated prototype demonstrates the ability to:

1. Run complete rescue missions inside the simulation environment.

2. Generate multi-sensor observations from UWB, thermal, and acoustic sensing models.

3. Adapt sensor influence when sensing quality deteriorates.

4. Produce interpretable multi-sensor fusion results.

5. Detect probable victim candidates.

6. Estimate victim locations and quantify localization performance.

7. Maintain victim tracks over multiple observations.

8. Estimate victim vitality and rescue priority.

9. Apply an AI layer capable of distinguishing credible victim tracks from hard negative candidates.

10. Quantify AI confidence and uncertainty.

11. Abstain from automated AI decisions when evidence is insufficient.

12. Operate through a common pipeline using simulated or microcontroller-provided data.

13. Display system information through an integrated operational dashboard.

14. Generate measurable evaluation results across multiple controlled rescue scenarios.

15. Execute the complete pipeline without requiring manual transfer of intermediate results between individual system modules.

The desired final workflow is:

Simulation / Sensor Input  
→ Data Acquisition  
→ Signal Processing  
→ Sensor Reliability Analysis  
→ Adaptive Fusion  
→ Localization  
→ Candidate Tracking  
→ AI Validation  
→ Vitality Assessment  
→ Rescue Prioritization  
→ Dashboard

---

# 7. Constraints and Assumptions

## 7.1 Constraints

The project currently operates under the following constraints:

- Limited access to specialized physical rescue sensing hardware.
- Primary development based on simulation.
- Limited project duration associated with a graduation project.
- Five-person development team.
- Computational-resource limitations.
- Hardware availability may restrict full real-sensor validation.
- Real disaster environments cannot currently be reproduced completely.

## 7.2 Assumptions

The project assumes that:

- multiple sensing modalities provide complementary information;
- sensor reliability may vary during a mission;
- sensor availability does not necessarily imply reliable measurement quality;
- simulation can be used for controlled algorithm development and comparative evaluation;
- future real sensor data can be integrated through standardized system interfaces;
- human rescue operators remain responsible for final operational decisions.

---

# 8. High-Level System Overview

The proposed system follows the high-level processing pipeline:

```text
Collapsed-Structure Scenario
            ↓
Mission / Probe Controller
            ↓
Multi-Sensor Layer
(UWB + Thermal + Acoustic)
            ↓
Microcontroller / Data Acquisition Interface
            ↓
Signal Processing
            ↓
Sensor Health & Measurement Quality
            ↓
Adaptive Multi-Sensor Fusion / HAIF
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
Abstention Decision
            ↓
Vitality Assessment
            ↓
Rescue Prioritization
            ↓
Operational Dashboard
