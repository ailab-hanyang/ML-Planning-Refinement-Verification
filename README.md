# A Refinement and Verification Pipeline for Learning-Based Planning: Temporal Consistency, Dynamics Feasibility, and Comfort

[![Status](https://img.shields.io/badge/Status-Published_in_Control_Engineering_Practice-brightgreen)]()
[![DOI](https://img.shields.io/badge/DOI-10.1016%2Fj.conengprac.2026.107320-blue)](https://doi.org/10.1016/j.conengprac.2026.107320)
[![License: MIT](https://img.shields.io/badge/License-MIT-lightgrey.svg)](LICENSE)

> Companion repository for the paper:
> *"A refinement and verification pipeline for learning-based planning: Temporal consistency, dynamics feasibility, and comfort"*
> Published in **[Control Engineering Practice](https://doi.org/10.1016/j.conengprac.2026.107320)**, Volume 178, Article 107320 · Available online 28 October 2026.
>
> **Hanyang University** · Department of Automotive Engineering (Automotive-Software Convergence)\
> **Valeo** · Driving Automation anSWer, Valeo Brain Division

---

## News

- **2026-10-28** — Paper available online in *Control Engineering Practice* (Volume 178, Article 107320).

## Paper

- 🔗 DOI: [10.1016/j.conengprac.2026.107320](https://doi.org/10.1016/j.conengprac.2026.107320)
- 📄 Publisher page: [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S0967066126005630)
- 📚 Venue: *Control Engineering Practice*, Volume 178, Article 107320, ISSN 0967-0661
- 🎥 Supplementary videos: [Real-vehicle comfort demos](#real-vehicle-comfort-demo-supplementary-videos) · [nuPlan ablation](#pipeline-ablation-on-the-nuplan-closed-loop-benchmark)

## Authors

Seungwoo Heo<sup>1</sup>, Seheon Ha<sup>1</sup>, Jahyun Koo<sup>1</sup>, Jiwon Seok<sup>2</sup>, Yuseung Na<sup>2</sup>, Paulo Resende<sup>3</sup>, Thomas Heitzmann<sup>3</sup>, and Kichun Jo<sup>2,\*</sup>

1. Department of Automotive Engineering (Automotive-Software Convergence), Hanyang University, Seoul 04763, Republic of Korea
2. Department of Automotive Engineering, Hanyang University, Seoul 04763, Republic of Korea
3. Driving Automation anSWer, Valeo Brain Division, Valeo, 6 rue Daniel Costantini, Créteil 94000, France

<sub>\* Corresponding author</sub>

## Abstract

Learning-based motion planners infer driving behavior from data, reducing reliance on hand-crafted rules. However, closed-loop deployment remains difficult because their outputs can fluctuate across planning cycles, violate vehicle dynamics and friction limits, and degrade ride comfort through unnecessary acceleration and steering oscillations. Prior safety filters and low-level control corrections can mitigate immediate violations, but they do not provide a unified mechanism that jointly addresses trajectory-level consistency, executability, and risk-aware validation at the planner output. This paper proposes a refinement-and-verification trajectory processing pipeline that improves closed-loop execution without replacing the underlying planner. The pipeline combines a Kalman filter&ndash;based consistency refinement across planning cycles, an NMPC-based Dynamic&ndash;Safety&ndash;Comfort refinement that imposes vehicle-dynamics limits as hard actuator and friction bounds and safety and comfort limits as soft constraints, and a risk-aware verification and selection stage based on friction limits, collision risk, and dynamic margin. On the NuPlan closed-loop benchmark, the full pipeline improves ride-comfort metrics by more than 10 % and increases collision- and TTC-related safety metrics by about 4 %, yielding a consistent gain in the overall planning score. Additional evaluation in CarMaker and real-vehicle experiments shows that the refined trajectories remain within the assumed dynamic limits and can be stably tracked without sacrificing task-level driving performance. These results support the role of a structured trajectory-level intermediate layer for bridging learning-based planning and real-world execution.

**Keywords:** Autonomous driving · Motion planning · Learning-based planning · Trajectory refinement · Risk-aware verification

## Overview

The pipeline is a trajectory-level intermediate layer placed **between a learning-based planner and the low-level controller**. It does not replace the planner: it takes the planner output and makes it temporally consistent, dynamically executable, and risk-checked before execution.

| Stage | Method | Output |
| :--- | :--- | :--- |
| **Con** — Consistency refinement | Kalman filter across planning cycles | Trajectory without cycle-to-cycle fluctuation |
| **DSC** — Dynamic&ndash;Safety&ndash;Comfort refinement | NMPC with vehicle-dynamics limits as hard actuator and friction bounds, safety and comfort limits as soft constraints | Executable, comfort-aware trajectory |
| **Sel** — Risk-aware verification and selection | Friction limits, collision risk, and dynamic margin | Verified trajectory handed to the controller |

## Key Results

Evaluated with **Pluto** as the base learning-based planner on the nuPlan closed-loop benchmark, and further validated in CarMaker and on a real vehicle.

| Evaluation | Result |
| :--- | :--- |
| nuPlan closed-loop — ride comfort | **more than 10 %** improvement |
| nuPlan closed-loop — collision- and TTC-related safety | **about 4 %** improvement |
| nuPlan closed-loop — overall planning score | consistent gain over the baseline planner |
| CarMaker collision-critical scenarios (S1&ndash;S3) | Unsafe planner outputs turned into executed evasive maneuvers |
| Real-vehicle experiments | Refined trajectories stay within the assumed dynamic limits and are stably tracked without sacrificing task-level driving performance |

## Pipeline Ablation on the nuPlan Closed-Loop Benchmark

The four configurations below are rendered from the same nuPlan closed-loop scenario and played in sync, so the contribution of each pipeline stage can be compared directly. All four runs use **Pluto** as the base learning-based planner, and the grid is ordered **C1 → C6 → C10 → C11**, adding one stage at a time from the baseline planner to the full pipeline.

<p align="center">
  <a href="Videos/Comparison_2x2.mp4">
    <img src="Videos/Comparison_2x2_preview.webp" width="100%" alt="2x2 comparison of ML (C1), ML + Con (C6), ML + Con + DSC (C10), and ML + Con + DSC + Sel (C11) on a nuPlan closed-loop scenario">
  </a>
  <br>
  <em>Click the preview to open the full-resolution video (2040 &times; 2216, 10 Hz, 14.9 s).</em>
</p>

### Legend

| Position | Configuration | Con | DSC | Sel | Description |
| :--- | :--- | :---: | :---: | :---: | :--- |
| Top-left | **ML (C1)** | | | | Baseline learning-based planner output, used as the reference |
| Top-right | **ML + Con (C6)** | ✓ | | | Adds temporal consistency refinement across planning cycles |
| Bottom-left | **ML + Con + DSC (C10)** | ✓ | ✓ | | Adds refinement under vehicle dynamics, safety, and comfort constraints |
| Bottom-right | **ML + Con + DSC + Sel (C11)** | ✓ | ✓ | ✓ | Full pipeline with risk-aware verification and selection |

**On-screen overlays**

- **Ego Dynamics (Rear Axle)**, at the top-left of each tile, reports the current longitudinal and lateral acceleration, jerk, longitudinal jerk, yaw acceleration, and yaw rate. The admissible bound is printed under each quantity, and a check mark indicates that the current value stays within it.
- **Metric Score**, at the bottom-left of each tile, reports the nuPlan closed-loop metric scores accumulated up to the current frame.

**Individual videos**

| Configuration | File |
| :--- | :--- |
| ML (C1) | [ML_(C1).mp4](Videos/ML_%28C1%29.mp4) |
| ML + Con (C6) | [ML_Con(C6).mp4](Videos/ML_Con%28C6%29.mp4) |
| ML + Con + DSC (C10) | [ML_Con_DSC(C10).mp4](Videos/ML_Con_DSC%28C10%29.mp4) |
| ML + Con + DSC + Sel (C11) | [ML_Con_DSC_Sel(C11).mp4](Videos/ML_Con_DSC_Sel%28C11%29.mp4) |

### Closed-Loop Rollout Comparison

<p align="center">
  <img src="Figures/Fig6_nuplan_closed_loop_rollout_C1_vs_C3.png" width="100%" alt="Closed-loop rollout comparison of Pluto (C1) and Pluto with DS refinement (C3) in a left-turn obstacle avoidance case and a right-turn case">
</p>

**Fig. 6. Closed-loop rollout comparison of Pluto (C1) and Pluto with DS refinement (C3) in two turning scenarios.** (a) Left-turn obstacle avoidance at *T* = 0.0, 1.5, and 3.0 s: C1 collides with a parked vehicle (red border), while C3 produces a collision-free evasive maneuver. (b) Right-turn case at *T* = 8.0, 10.5, and 13.0 s: C1 exhibits a driving-direction violation (blue border), while C3 restores the correct driving direction, with a temporary drivable-area violation (yellow border) observed in this example. &nbsp;[[vector PDF]](Figures/Fig6_nuplan_closed_loop_rollout_C1_vs_C3.pdf)

## Collision-Avoidance Evaluation in CarMaker

Three collision-critical scenarios (S1&ndash;S3) were reproduced in CarMaker to check whether the refinement pipeline turns an unsafe planner output into an executable evasive maneuver under a full vehicle dynamics model.

<p align="center">
  <img src="Figures/Fig7_carmaker_collision_avoidance_S1_S3.png" width="78%" alt="Comparison of baseline and refined vehicle behaviors in three CarMaker collision avoidance scenarios">
</p>

**Fig. 7. Comparison of vehicle behaviors in collision avoidance scenarios (S1&ndash;S3).** The left column ((a), (c), (e)) shows the baseline Pluto following its original trajectory, which leads to collisions across all scenarios as no effective evasive maneuver is generated. The right column ((b), (d), (f)) illustrates the refined trajectory case under the same conditions, where evasive maneuvers are executed, resulting in collision avoidance. &nbsp;[[vector PDF]](Figures/Fig7_carmaker_collision_avoidance_S1_S3.pdf)

## Real-Vehicle Collision Avoidance at K-City

<p align="center">
  <img src="Figures/Fig9_real_vehicle_S3_kcity.png" width="68%" alt="Real-vehicle reproduction of the S3 collision-avoidance scenario at K-City, showing the executed refined trajectory and the overlaid raw ML trajectory">
</p>

**Fig. 9. Real-vehicle reproduction of the S3 collision-avoidance scenario at K-City.** The ego vehicle is executed in closed loop by tracking the refined trajectory (C10, cyan). The raw ML trajectory (C1, orange) is generated in the same planning cycles but is *not executed*; it is overlaid only to visualize the difference in planned geometry. Across the snapshots from 2.0 s to 3.5 s, the executed trajectory C10 steers the ego vehicle away from the conflict region with the interacting vehicle. &nbsp;[[vector PDF]](Figures/Fig9_real_vehicle_S3_kcity.pdf)

## Real-Vehicle Comfort Demo Supplementary Videos

The real-vehicle experiments were conducted at **K-City**, the autonomous driving proving ground operated by the Korea Transportation Safety Authority (KATRI).

The experimental platform was a research-grade **Hyundai IONIQ 5** equipped with a **128-channel LiDAR, blind-spot LiDARs, a MEMS LiDAR, RTK-GNSS, and an onboard PC with a CAN interface**. The full planning and refinement pipeline was deployed onboard and operated in closed loop at **10 Hz**.

For real-vehicle comfort evaluation, two representative scenarios from the paper are shared in this repository. Each video is presented in a side-by-side format for qualitative comparison, with **ML on the left** and **Refined on the right**.

### Scenario 4: Arterial Driving

This scenario evaluates ride comfort during arterial-road driving with lane changes under the condition of **ego speed below 50 km/h**.

**YouTube Link:** [Scenario 4 Video](TBD)

### Scenario 5: Urban Driving with Stop-and-Go

This scenario evaluates ride comfort during urban driving with repeated stop-and-go behavior under the condition of **ego speed below 30 km/h**.

**YouTube Link:** [Scenario 5 Video](TBD)

## Supplementary Materials

| Material | Location | Description |
| :--- | :--- | :--- |
| nuPlan ablation videos | [`Videos/`](Videos/) | 2&times;2 synchronized comparison and the four per-configuration rollouts (C1, C6, C10, C11) |
| Paper figures | [`Figures/`](Figures/) | Figs. 6, 7 and 9 as PNG for inline display and as vector PDF for download |
| Real-vehicle comfort demos | YouTube (_links to be added_) | Scenarios 4 and 5, ML vs. Refined side by side |
| Citation metadata | [`CITATION.cff`](CITATION.cff) | Machine-readable citation used by the GitHub "Cite this repository" entry |

## Citation

```bibtex
@article{HEO2027107320,
title = {A refinement and verification pipeline for learning-based planning: Temporal consistency, dynamics feasibility, and comfort},
journal = {Control Engineering Practice},
volume = {178},
pages = {107320},
year = {2027},
issn = {0967-0661},
doi = {https://doi.org/10.1016/j.conengprac.2026.107320},
url = {https://www.sciencedirect.com/science/article/pii/S0967066126005630},
author = {Seungwoo Heo and Seheon Ha and Jahyun Koo and Jiwon Seok and Yuseung Na and Paulo Resende and Thomas Heitzmann and Kichun Jo},
keywords = {Autonomous driving, Motion planning, Learning-based planning, Trajectory refinement, Risk-aware verification}
}
```

## License

The contents of this repository are released under the [MIT License](LICENSE).
