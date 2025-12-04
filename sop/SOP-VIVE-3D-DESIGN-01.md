---
sop_id: SOP-VIVE-3D-DESIGN-01
asset_group: rc545a-vis
type: vis_sop
name: Vive 3D design visualisation pipeline
relates_to_kit: ""
purpose: |
  Define a repeatable pipeline to take SolidWorks assemblies into Unity for mixed reality visualisation on Vive headsets.
intended_users:
  - Designers conducting CAD design reviews
  - Researchers visualising robot cells and workstations
  - Staff preparing MR demonstrations in RC545a VIS
status: draft
last_tested:
owner: ""
---

## 1. Purpose

Define a repeatable pipeline to take SolidWorks assemblies into Unity for mixed reality visualisation on Vive headsets.

Target use cases:

- Design review of products and mechanisms

- Visualisation of robot cells and workstations

- MR demonstrations in RC545a VIS


---

## 2. Standard pipeline overview

Standard MR geometry pipeline for mechanical assemblies:

`SolidWorks → STEP → FreeCAD → OBJ (+MTL) → Unity`

Scope and constraints:

- Assembly granularity must be preserved so Unity retains separate components.
- Performance must remain acceptable (avoid extreme polycounts).
- Physical scale must be correct (mm → m conversion handled in Unity import settings).
- Basic materials come via `.mtl` for quick differentiation; expect cleanup in Unity.

Tools:

- SolidWorks 2021 Education (no OBJ exporter)
- FreeCAD (with STEP import and OBJ export)
- Unity (URP or Standard, units in metres)

Unit convention: **1 Unity unit = 1 metre** (OBJ comes in mm; apply 0.001 scale factor in Unity).

---

## 3. SolidWorks stage

### 3.1 Create a dedicated export configuration

1. Open the assembly in SolidWorks.
2. Create a new configuration (for example `Unity_Export`) and switch to it before editing.
3. Use this configuration to slim the model for MR:
   - Suppress bolts, nuts, washers, pins, screws and other small hardware (leave only a few for context if needed).
   - Hide or suppress purely internal components that will not be visible (bearings, internal shafts, enclosed mechanisms).
   - Suppress cosmetic threads and tiny fillets/chamfers that do not affect silhouette.
   - Keep moving subsystems as separate subassemblies (e.g. BaseFrame, Robot_Base, Robot_Link1, Robot_Link2, Gripper, Guarding, Fixture).
   - Give sensible names to parts and subassemblies; names may propagate downstream.

### 3.2 Set origin and orientation

1. Decide the Unity origin:
   - Standalone machine: origin at floor level, centered under the machine.
   - Lab layout: origin at a known room reference (e.g. robot base centre).
2. Align planes so Top ≈ floor/up, and Front/Right are reasonable for Unity view. Minor corrections can be handled later in Unity.

### 3.3 Assign appearances for material mapping

1. Assign appearances at part or body level so that identical materials share one appearance.
2. Use a small, intentional palette (e.g. structural frame, guards, moving tooling, fixtures/jigs, floor plate).
3. Avoid excessive colour variations; STEP will carry these appearances into FreeCAD and then into the `.mtl`.

### 3.4 Export STEP

1. With `Unity_Export` active, go to `File > Save As…` and choose `STEP AP214` or `AP242`.
2. In **Options** set:
   - Output: `Assembly` (not individual parts).
   - Include AP214/AP242 colours where available.
   - Document units (mm).
3. Save as `ProjectName_Assembly_unity_export.step`. This is the canonical CAD export for the VIS pipeline.

---

## 4. FreeCAD stage

FreeCAD converts the analytic STEP geometry into meshes and exports OBJ + MTL.

### 4.1 Import the STEP file

1. Open FreeCAD and `File > Open…` the `ProjectName_Assembly_unity_export.step` file.
2. Confirm the Model tree reflects the SolidWorks structure.

### 4.2 Optional cleanup in FreeCAD

1. If anything was missed in SolidWorks, hide or delete fully internal or tiny parts.
2. Use bounding boxes to identify very small components if needed; prefer hiding to keep options for future exports.

### 4.3 Generate meshes from CAD shapes

1. Switch to the **Part** workbench.
2. Select solids (per part or groups) and choose `Part → Create mesh from shape`.
3. Suggested tessellation settings:
   - Linear Deflection: start at 0.5 mm (or 0.2 mm for tighter curvature).
   - Angular Deflection: ~10°.
4. Review the resulting mesh objects; keep per-part meshes separate to preserve assembly granularity.

### 4.4 Optional: remove solids

Hide or delete the original solids once meshes exist to avoid exporting both solids and meshes.

### 4.5 Export OBJ (+ MTL)

1. Select all mesh objects to export.
2. `File > Export…` → `Wavefront OBJ (.obj)`.
3. Ensure material/colour export is enabled so a `.mtl` file is produced.
4. Export as `ProjectName_Assembly_unity.obj`; FreeCAD will write `ProjectName_Assembly_unity.mtl` alongside it (and possibly an empty textures folder).

Units note: FreeCAD uses millimetres; OBJ coordinates will be in mm and must be scaled in Unity.

---

## 5. Unity stage

### 5.1 Import OBJ and MTL

1. Copy `ProjectName_Assembly_unity.obj` and `ProjectName_Assembly_unity.mtl` into your Unity project (e.g. `Assets/Models/ProjectName/`).
2. Keep OBJ and MTL together on first import so Unity auto-generates meshes and materials.

### 5.2 Fix mm-to-metre scaling

1. Select the imported OBJ asset in Unity’s Project window.
2. In **Model Import Settings**, set **Scale Factor** to `0.001` (mm → m).
3. Apply, then verify a 1000 mm CAD length appears as 1 Unity unit in scene.

### 5.3 Verify assembly granularity

1. Drag the OBJ into a scene and expand its hierarchy.
2. Confirm parts/subassemblies remain separate. If everything is one mesh, re-export from FreeCAD keeping meshes per part.

### 5.4 Materials and cleanup

1. Unity auto-creates Materials from the `.mtl`; review them under the OBJ asset.
2. Rename and swap shaders to URP/Lit (or project standard).
3. If too many similar materials appear, reduce appearance variety in SolidWorks, edit the `.mtl`, or consolidate materials in Unity.

### 5.5 Prepare a prefab

1. Turn the imported object into a prefab (drag scene instance into a Prefabs folder).
2. Add colliders, assign layers, and attach interaction components per your XR workflow.

---

## 6. Summary and good practice

- This pipeline is the supported VIS workflow when you need correct physical scale, clean-enough hierarchy, and basic material differentiation.
- Accepted limitations: geometry is triangulated; no feature history, mates, or advanced UVs; `.mtl` materials are basic and need Unity cleanup.
- Workflow reminders to document:
  - SolidWorks 2021 Education cannot export OBJ directly; use the STEP → FreeCAD → OBJ (+MTL) path.
  - Always work from a dedicated `Unity_Export` configuration and strip internal/tiny parts in SolidWorks/FreeCAD.
  - Export STEP AP214/AP242 with colours; generate meshes in FreeCAD with moderate tessellation; export OBJ with materials enabled.
  - Import OBJ + MTL together and set Unity Scale Factor = 0.001 for mm-based models.
