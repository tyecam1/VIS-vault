## Delivery Work Log (Summary)

**Role / remit:** Establish RC545a as a usable VIS space using existing departmental hardware, with practical, repeatable workflows for **collaboration**, **capturing**, and **conceptualising**, plus storage/access protocols and user-facing guidance.

**Primary workload:** Troubleshooting and learning new software UIs.

**Main takeaways:** 
- Unity is a better medium for research and implementation speed, it is 100% code oriented. 
- Unreal is more robust with many more configuration options, it is built in Cpp and the editor has 'Blueprints' as a interface, which comes with a steeper learning curve.
- The Vive plugins are currently more stable for Unity than Unreal.
- Both pathways now have templates and guides for quick class implementation.
- HTC Vive needed to be built upon Vive->SteamVR->OpenXR pipelines, whereas oculus is oculusXR->OpenXR and valve is SteamVR->OpenXR. Hence largest device/plugin/engine configuration workload is complete.

**Reporting period:** 07 Nov 2025 to 28 Jan 2026  
**Total time logged:** **80 hours** (vs. 70 hours estimated)

---

## 1) Executive summary of progress

### Delivered

- **Stable Unity MR baseline** for **HTC Vive Focus Vision** (passthrough-ready demo scene aligned to the physical room; repeatable CAD import route).
    
- **Repeatable CAD-to-realtime pipelines** established and documented [(SolidWorks → STEP → FreeCAD → OBJ → Unity), (SolidWorks → STEP → FreeCAD → FBX → Unreal)].
    
- **UE 5.4 Focus Vision MR baseline** established with **passthrough validated** and environment meshes transferrable into the passthrough template map despite engine incompatibility constraints.
    
- **Core MR interactions prototyped and stabilised** in UE 5.4: controller-driven tracing, grab diagnostics, and **two-hand scaling** logic with state gating and safety clamps.
    
- **Inventory/documentation** (asset notes, dashboards, SOP consolidation, and storage protocol clarity). Logged to GitHub.
    

### Key risks / constraints encountered

- **Engine/plugin compatibility**: passthrough constrained to UE 5.4 while an existing project was authored in UE 5.5, blocking direct migration.
    
- **Asset quality constraints**: The conversion from SOLIDWORKS to Unity loses all material information, and they must be reassigned from within the Unity editor. Similar but less convoluted process in Unreal (you can apply the textures more conveniently). Configuring texture sets increases manual material reconstruction overhead.
    
- **Interaction**: Grab and scale functionalities implemented into the Unreal pipeline
    

### Immediate next actions

- Produce a short VIS “How to run a session” guide for staff/students (Unity MR demo, UE MR demo, CAD import, start-up/shutdown, storage protocol).

---

## 2) Work log by date

### 2025-11-07 | 4 hours

**Activity:** Induction and onboarding with Andrew and Alan.  
**Outcome:** Scope clarified, current equipment state reviewed, initial priorities discussed. 

### 2025-11-14 | 8 hours

**Activity:** Troubleshooting existing HTC Vive + Unity configuration.  
**Outcome:** Identified blockers preventing a stable and repeatable workflow.

### 2025-11-21 | 8 hours

**Activity:** Re-established HTC Vive Unity pipeline from a clean baseline.  
**Delivered:**

- Initial asset notes created.
	
- Repository/file structure outlined for maintainability.
    

### 2025-11-28 | 2 hours

**Activity:** Early workflow consolidation under constrained access.  
**Delivered:**

- Initial SOP drafted for Vive + SolidWorks 3D design workflow.
    
- Installation monitoring and follow-up planning.  
    **Blockers:** Software installs delayed due to admin rights availability constraints (Jim out of office).
    

### 2025-12-04 | 8 hours

**Delivery milestone:** Unity MR baseline for Focus Vision.  
**Delivered:**

- Unity XR pipeline finalised for **HTC Vive Focus Vision** (untethered MR): passthrough-ready scene, validated demo, CAD import workflow documented.
    
- CAD conversion route validated: **STEP (AP214/AP242) → FreeCAD → OBJ** (Blender not required for core workflow).
    
- Inventory/dashboard updates: Valve Index entry created; serial/base station details flagged as missing.
    
- Documentation standardised: work-log format normalised; SOP rewritten to full end-to-end workflow with mesh cleaning, tessellation, scale correction, material remapping.  
    **Open issues:**
    
- Standardise/document HTC Vive SteamVR setup.
    
- Unreal install pending for colleague.
    
- Meta Quest 2 not yet validated.
    

### 2025-12-09 | 6 hours

**Activity:** Demo polish + room operations.  
**Delivered:**

- Unity canonical material palette applied (metals/plastics/rubber/wood/safety) to demo assembly for consistent visual quality.
    
- SOP extended: corrected origin/orientation guidance; documented palette usage; added Focus Vision connection procedure (RC545 IoT 5 GHz).
    
- Documented approach for handling Vive “centre of play” changes while preserving fixed MR origin.
    
- Storage protocol improved: secured VR locker and clarified access/return guidance.
    

### 2026-01-09 | 8 hours

**Activity:** Platform upgrade to enable higher-intensity Unreal workloads.  
**Delivered:**

- Reset and full reinstall/reconfiguration on a more powerful PC to support Unreal MR workflows. Specs: 24GB GPU, 256 GB ram @ 4800, Xeon(z) processor.
    
- Valve Index brackets installed in RC545a.
    
- Unreal SOP work initiated (human-machine interaction focus).
    

### 2026-01-14 | 9 hours

**Delivery milestone:** UE 5.4 Focus Vision passthrough baseline + environment integration.  
**Delivered:**

- Passthrough validated in UE 5.4 using HTC sample project/map (not available in existing farm project configuration).
    
- Imported and configured Costa's Farm project as a sample mesh to work with.
    
- Materials rebuilt from limited texture sources; “washed out” visuals diagnosed as lighting-build state; lighting stabilised for large-scale assets (Lightmass Importance Volume management).  
    **Blockers:**
    
- UE 5.5 → UE 5.4 incompatibility constrained reuse.
    
- No raw FBX + incomplete PBR texture set increased manual overhead.
    

### 2026-01-23 | 8 hours

**Activity:** UE 5.4 (Vive OpenXR) passthrough + interaction workflow integration.  
**Delivered:** Begun combining sample Blueprints into a usable VIS baseline for interaction.

### 2026-01-26 | 8 hours

**Activity:** UE 5.4 Focus Vision input + interaction, two-hand scaling integration.  
**Delivered:**

- Two-hand scaling pipeline implemented (Start/Update/Stop) with smoothing, clamped scale bounds, and state gating.
    
- Interaction aiming/tracing debugged for left/right with hit validation.
    
- Blueprint control-flow issue fixed to restore reliable sequencing.  
    **Outcome:** Functional two-hand scaling + visible debug primitives.
    

### 2026-01-27 | 5 hours

**Activity:** Interaction debugging, plugin configuration validation, motion source selection, grabbable object diagnosis.  
**Delivered:**

- Resolved some headset-only issues where one monocle would receive harsh visual glitches through plugin and setting configuration debugging.
    
- Motion source strategy clarified: controller-based sources used as reliable fallback when hand interaction poses not produced. Hand (not controller) interaction dropped from project.
    
- Grab debugging isolated to object-side configuration (collision/filtering/interface routing), not pawn trace logic.  
    **Issues observed:** Vive OpenXR passthrough layer warnings appear in logs; not blocking execution but indicate runtime/profile limitations.
    

### 2026-01-28 |7 hours

**Activity:** MR interaction stabilisation (grab + two-hand scaling) and input validation.  
**Delivered / outcomes:**

- Two-hand scaling stabilised by refactoring attachment/state handling; prevented snap-back on release.
    
- Tracing/grip detection confirmed reliable via debug.
    
- Resolved “sticky grab” as input mapping/state sequencing; scaling currently trigger-only.  
    
- Created the [[UNREAL-3D-DESIGN]] SOP.
---

## 3) Current status against VIS capability pillars

### Collaboration (MR meeting / shared review)

- **Status:** Foundation in place (Focus Vision MR scenes and room alignment). PC now capable of more powerful simulations and recording/ streaming.
    
- **Gap:** Packaged Multi-user workflow not made.
    
### Capturing (recording / scanning / mocap)

- **Status:** OBS installed on PC, capable of recording and streaming 2D visualisations.
	
- **Gap:** End-to-end validated capture workflows still need consolidation and demonstration packaging. Not sure how to approach multi-institution collaboration.
    

### Conceptualisation (CAD → immersive review / interaction)

- **Status:** Strong progress: CAD conversion route validated; Unity pipeline stable; Unreal pipeline stable; core interaction implemented in unreal.
    

---

## 4) Time and scope note (why 81 hours)

Exceeded the estimate because there was extensive platform instability and incompatibility work that was not optional if we want “repeatable workflows” instead of one-off demos:

- engine/plugin configurations created forced rework,
    
- no existing tutorials or demos, aside from game developer text forums,
    
- inputs and motion source mismatch required deep debugging for reliable interaction.
    
