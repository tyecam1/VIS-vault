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

Define a repeatable, documented pipeline for transferring SolidWorks assemblies into Unity for mixed reality (MR) visualisation on Vive headsets within RC545a VIS.

Primary use cases:

- Design review of products and mechanisms
    
- Visualisation of robot cells and workstations
    
- MR demonstrations for teaching, research and external engagement
    

---

## 2. Standard pipeline

Standard VIS geometry pipeline for mechanical assemblies:

`SolidWorks → STEP → FreeCAD → OBJ + MTL → Unity`

Scope and constraints:

- Preserve assembly hierarchy so that parts and subassemblies are addressable in Unity.
    
- Maintain correct physical scale so that 1 Unity unit corresponds to 1 metre in the MR environment.
    
- Keep triangle counts low enough for smooth rendering on tethered and standalone headsets.
    
- Provide basic material differentiation via colours, with final shading managed in Unity.
    

Tools:

- SolidWorks 2021 Education (no native OBJ export)
    
- FreeCAD (STEP import, mesh generation, OBJ export)
    
- Unity (URP or Built-in Render Pipeline, units set to metres)
    

Working rules of thumb:

- Small mechanism: target < 250k triangles
    
- Full workstation / cell: target < 750k triangles
    
- Always import to Unity with Scale Factor `0.001` for mm-based CAD
    

---

## 3. SolidWorks stage

### 3.1 Create a dedicated export configuration

1. Open the assembly in SolidWorks.
    
2. Create a new configuration, for example `Unity_Export`, and switch to it.
    
3. Use this configuration to simplify the model for MR:
    
    - Suppress bolts, nuts, washers, screws, pins and other small fasteners, unless required for context.
        
    - Suppress purely internal components that will not be seen in MR (bearings, enclosed shafts, internal mechanisms).
        
    - Suppress cosmetic threads and very small fillets/chamfers that do not affect the silhouette.
        
    - Keep moving subsystems as separate subassemblies (for example `BaseFrame`, `Robot_Base`, `Robot_Link1`, `Robot_Link2`, `Gripper`, `Guarding`, `Fixture`).
        
    - Give meaningful names to parts and subassemblies; these names will appear in FreeCAD and Unity.
        

This configuration becomes the canonical source for the VIS pipeline.

### 3.2 Set origin and orientation

The goal is that assemblies drop into shared VIS scenes without corrective rotation or translation.

1. Use the default SolidWorks orientation so that in Unity:
    
    - Z is world up.
        
    - X is left–right across the room.
        
    - Y is forward–back relative to the main viewing direction.
        
2. Place the global origin at a physically meaningful point. Recommended default:
    
    - Centre of the robot base or primary fixture at floor level.
        
3. If a different origin is required (for example room corner, conveyor datum, human standing position), record this in the model notes and keep it consistent across revisions.
    

Coordinate systems should not be corrected ad hoc in Unity.

### 3.3 Assign appearances for material mapping

1. Assign appearances at part or body level so that identical materials share one appearance.
    
2. Reuse appearances wherever possible to avoid hundreds of near-duplicate materials later.
    
3. Use colour and material only to distinguish functional groups, not aesthetic variants. Final appearance will always be cleaned up in Unity.
    

Appearance data is carried in the STEP file and later converted to `.mtl` by FreeCAD.

### 3.4 Export STEP

1. With `Unity_Export` active, choose `File > Save As…` and select `STEP AP214` or `AP242`.
    
2. In **Options**:
    
    - Output: `Assembly`.
        
    - Enable export of colours (AP214/242).
        
    - Units: millimetres.
        
3. Save as:
    
    `ProjectName_Assembly_unity_export.step`
    

This STEP file is the canonical CAD export for the VIS pipeline.

---

## 4. FreeCAD stage

FreeCAD converts analytic STEP geometry into meshes and exports `OBJ + MTL`.

### 4.1 Import the STEP file

1. Open FreeCAD and choose `File > Open…`.
    
2. Load `ProjectName_Assembly_unity_export.step`.
    
3. Confirm that the Model tree reflects the SolidWorks assembly structure.
    

### 4.2 Optional: cleanup in FreeCAD

If simplification in SolidWorks was incomplete:

1. Hide or delete fully internal components and very small parts that do not contribute to the MR use case.
    
2. Use bounding boxes or selection filters to identify tiny elements. Prefer hiding rather than permanent deletion for reusable exports.
    

### 4.3 Generate meshes from CAD shapes

1. Switch to the **Part** workbench.
    
2. Select the solids to be exported (per part or logical groups).
    
3. Use `Part → Create mesh from shape`.
    

Suggested starting tessellation settings:

- Linear Deflection: 0.5 mm (tighten to 0.2 mm only where needed)
    
- Angular Deflection: 10°
    

4. Inspect the resulting mesh objects:
    
    - Ensure each part or subassembly produces a separate mesh.
        
    - Check that curved surfaces appear smooth enough in viewport previews without excessive triangle counts.
        

### 4.4 Remove solids after meshing (optional but recommended)

To avoid exporting both solids and meshes:

1. Hide or delete the original solid bodies once meshes are created and verified.
    
2. Keep only the mesh objects selected for export.
    

### 4.5 Export OBJ + MTL

1. Select all mesh objects to export.
    
2. Choose `File > Export…` and select `Wavefront OBJ (.obj)`.
    
3. Ensure that material and colour export is enabled in OBJ export options.
    
4. Export as:
    
    `ProjectName_Assembly_unity.obj`
    

FreeCAD will generate:

- `ProjectName_Assembly_unity.obj`
    
- `ProjectName_Assembly_unity.mtl`
    
- Optionally, an empty or populated textures folder
    

Units note:

- CAD and FreeCAD are kept in millimetres.
    
- OBJ is unitless. Unity will interpret the numeric coordinates with a Scale Factor on import (Section 5.2).
    

---

## 5. Unity stage

### 5.1 Import OBJ and MTL

1. In the Unity project, create a consistent folder structure, for example:
    
    - `Assets/Models/ProjectName/Source` for original OBJ + MTL
        
    - `Assets/Models/ProjectName/Prefabs` for prefabs
        
    - `Assets/Materials/ProjectName` for cleaned materials
        
2. Copy `ProjectName_Assembly_unity.obj` and `ProjectName_Assembly_unity.mtl` into the `Source` folder. Keep filenames identical except for extension.
    
3. Let Unity complete the initial import so that meshes and provisional materials are generated.
    

Avoid moving or renaming these files outside Unity to preserve `.meta` links.

### 5.2 Fix mm-to-metre scaling

1. Select the imported OBJ asset in the Project window.
    
2. In **Model Import Settings**:
    
    - Set **Scale Factor** to `0.001`.
        
3. Click **Apply**.
    
4. Validate scale by comparing a known length (for example a 1000 mm beam) with Unity’s grid. It should measure 1 Unity unit (1 m).
    

Do not rescale in FreeCAD or through additional GameObject scaling.

### 5.3 Verify assembly hierarchy

1. Drag the imported object into a test scene.
    
2. Expand the hierarchy:
    
    - Confirm that parts and subassemblies are preserved as separate child objects.
        
    - Check that names match the SolidWorks configuration in a recognisable way.
        

If the hierarchy collapses into a single mesh, revisit the FreeCAD mesh creation step and ensure per-part meshes are generated and selected for export.

### 5.4 Materials and cleanup

1. Unity will create Materials from the `.mtl` file and place them under the model asset.
    
2. Review the materials:
    
    - Remove or consolidate near-duplicate materials representing the same physical finish.
        
    - Rename materials to meaningful names where helpful (for example `Steel_Structural`, `Guarding_Polycarbonate`).
        
3. Assign shaders consistent with the project render pipeline:
    
    - For URP: use `URP/Lit` as the default.
        
    - For special MR use cases (for example ghosted geometry, outlines), document any non-standard shaders used.
        
4. If necessary, further reduce visual complexity for performance (for example single shared material per subassembly).
    

### 5.5 Prepare and store a prefab

1. With the assembly instance in the scene, add:
    
    - Colliders (mesh, box or capsule as appropriate).
        
    - Layers and tags used by the XR interaction system.
        
    - Any script components or interaction components required by the MR template.
        
2. Drag the configured GameObject into the `Prefabs` folder to create a prefab.
    
3. Reference this prefab in MR scene templates for RC545a rather than re-importing OBJ files.
    

---

## 6. Summary and good practice

- This SOP defines the standard VIS pipeline when correct physical scale, clean hierarchy and basic material differentiation are required for MR.
    
- Accepted limitations:
    
    - Geometry is triangulated and feature history is lost.
        
    - Mates and constraints do not transfer.
        
    - UVs are basic; advanced texturing requires manual work in DCC tools.
        
- Recommended practice:
    
    - Always export from a dedicated `Unity_Export` configuration in SolidWorks that strips internal and tiny parts.
        
    - Use AP214/AP242 STEP with colours enabled.
        
    - Generate meshes in FreeCAD with moderate tessellation and verify triangle counts against the targets.
        
    - Export OBJ + MTL with materials enabled, keep files together on first Unity import, and set Unity Scale Factor to `0.001`.
        
    - Standardise origin, orientation and folder structures so that assemblies can be dropped into shared RC545a MR templates with minimal adjustment.