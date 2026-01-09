---
sop_id: SOP-VIVE-3D-DESIGN-01
asset_group: rc545a-vis
type: vis_sop
name: Vive 3D design visualisation pipeline
relates_to_kit: "[[KIT-VIVE-MOCAP-01]]"
purpose: A repeatable pipeline to take SolidWorks assemblies into Unity for mixed reality visualisation on Vive headsets.
intended_users:
  - Designers conducting CAD design reviews
  - Researchers visualising robot cells and workstations
  - Staff preparing MR demonstrations in RC545a VIS
status: draft
last_tested: 25-12-4
owner: tye.cameron-robson@strath.ac.uk
---
## 0. Purpose

<<<<<<< HEAD
A repeatable pipeline to take SolidWorks assemblies into Unity for mixed reality visualisation on Vive headsets.
=======
A repeatable, documented pipeline for transferring SolidWorks assemblies into Unity for mixed reality (MR) visualisation on OpenXR-compatible headsets used within RC545a VIS (e.g. Vive Focus Vision, Valve Index, Meta Quest).
>>>>>>> origin/main

Primary use cases:

- Design review of products and mechanisms
- Visualisation of robot cells and workstations
<<<<<<< HEAD
- MR demonstrations in RC545a VIS
=======
- MR demonstrations for teaching, research and external engagement
   
---
## 1. Connecting the Vive Focus Vision to the VIS Workstation

Before using the 3D design pipeline in Unity, the Vive Focus Vision must be wirelessly linked to the VIS workstation through the Vive Hub.

### 1.1 Connect both devices to the correct network

1. On the **VIS workstation PC**, connect to:  
    `RC545-IoT-5GHz`
    
2. On the **Vive Focus Vision headset**, open **Settings → Network** and connect to:  
    **`RC545-IoT-5GHz`** using the same credentials.
### 1.2 Initiate connection via the Vive interface

1. Put on the headset.
    
2. In the Vive Focus Vision home interface, find the Vive Streaming options.
    
3. The headset will scan for available PCs on the same network.
    
4. Select the VIS workstation PC from the detected list.
    
5. Confirm the pairing request on both the headset and the PC if prompted.

Once connected, the workstation will automatically switch SteamVR/OpenXR to the wireless streaming mode.

---
## 2. Standard pipeline

Standard VIS geometry pipeline for mechanical assemblies:

`SolidWorks → STEP → FreeCAD → OBJ → Unity`

Scope and constraints:

- Preserve assembly hierarchy so that parts and subassemblies are addressable in Unity.
    
- Maintain correct physical scale so that 1 Unity unit corresponds to 1 metre in the MR environment.
    
- Keep triangle counts low enough for smooth rendering on tethered and standalone headsets.
    
- Basic material differentiation is lost, final materials and shading managed in Unity.
    

Tools:

- SolidWorks 2021 Education (no native OBJ export)
    
- FreeCAD (STEP import, OBJ export)
    
- Unity (URP or Built-in Render Pipeline, units set to metres)
    

Working rules of thumb:

- Small mechanism: target < 250k triangles
    
- Full workstation / cell: target < 750k triangles
    
- Always import to Unity with Scale Factor `0.001` for mm-based CAD
>>>>>>> origin/main
    

---

## 3. SolidWorks stage

<<<<<<< HEAD
- SolidWorks installed and licensed
- Blender installed (latest LTS recommended)
- STEP import support in Blender (built in add-on or IT managed)
- Unity (template project available at {NEEDS FILLING}
- SteamVR and Vive Hub installed on the VIS machine

---
## 3. Prepare the assembly in SolidWorks

1. Open the **SLDASM** you want to export.
2. Resolve any missing references and broken mates. The assembly must rebuild cleanly.
3. Clean up the model:
     - Suppress tiny cosmetic details that do not matter for MR (fillet logos, screws that are not needed, internal threads).
     - Hide or suppress reference geometry and construction parts.    
4. Set a sensible origin and orientation:
    - Ensure base z of the object at {Z = 0} in SolidWorks.
    - Front view in SolidWorks should correspond to forward in Unity (Z positive).
5. Check scale and units:
    - Document units: millimetres.
    - Know the approximate overall size (for sanity checking later in Unity).
6. Save a dedicated configuration for MR, for example `Config_MR_Export`, with the correct suppressed parts and visibility.

---

## 4. Export from SolidWorks to STEP
1. With the `Config_MR_Export` active, go to  
    `File > Save As`
2. Set **Save as type** to `STEP AP214 (*.step, *.stp)`.
3. Click **Options** and set:
    - Output as: `AP214` (preferred)
    - Export: `Assembly`
    - Units: `Document units` (mm)
    - Do not export hidden components (for a lighter file).
4. Save as  
    `ProjectName_ModelName_MR_v1.step`  
    in your shared VIS models folder.
=======
### 3.1 Create a dedicated export configuration

1. Open the assembly in SolidWorks.
    
2. Create a new configuration, for example `Unity_Export`, and switch to it.
    
3. Use this configuration to simplify the model for MR:
    
    - Suppress bolts, nuts, washers, screws, pins and other small fasteners, unless required for context.
        
    - Suppress purely internal components that will not be seen in MR (bearings, enclosed shafts, internal mechanisms).
        
    - Keep moving subsystems as separate subassemblies (for example `BaseFrame`, `Robot_Base`, `Robot_Link1`, `Robot_Link2`, `Gripper`, `Guarding`, `Fixture`).
        
    - Give meaningful names to parts and subassemblies; these names will appear in FreeCAD and Unity.
        

### 3.3 Assign appearances for semantic material mapping

SolidWorks appearances are lost during the conversion process, however you can create and assign materials and customise their aesthetic properties in Unity. 

1. Assign SOLIDWORKS appearances at part or body level so that identical physical materials share one appearance
   (e.g. all structural steel parts use the same “Steel” appearance, all guards use the same “Polycarbonate” appearance).

2. Reuse appearances wherever possible to avoid hundreds of near-duplicate labels later.

3. Final shading and colours are defined in Unity using the VIS engineering material palette (Section 5.5). There is a default pallette in the template, but adding your own is simple.

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

FreeCAD converts analytic STEP geometry into meshes and exports `OBJ (+ MTL)`.

### 4.1 Import the STEP file

1. Open FreeCAD and choose `File > Import…`.
    
2. Load `ProjectName_Assembly_unity_export.step`.
    
3. Confirm that the Model tree reflects the SolidWorks assembly structure.
    

### 4.2 Optional: cleanup in FreeCAD

If simplification in SolidWorks was incomplete:

1. Hide or delete fully internal components and very small parts that do not contribute to the MR use case.
    
2. Use bounding boxes or selection filters to identify tiny elements. Prefer hiding rather than permanent deletion for reusable exports.
    

### 4.3 Optional: Generate meshes from CAD shapes

1. Switch to the **Part** workbench.
    
2. Select the solids to be exported (per part or logical groups).
    
3. Use `Part → Create mesh from shape`.
    

Suggested starting tessellation settings:

- Linear Deflection: 0.5 mm (tighten to 0.2 mm only where needed)
    
- Angular Deflection: 10°
    

4. Inspect the resulting mesh objects:
    
    - Ensure each part or subassembly produces a separate mesh.
        
    - Check that curved surfaces appear smooth enough in viewport previews without excessive triangle counts.
        

### 4.4 Optional: Remove solids after meshing (recommended)

To avoid exporting both solids and meshes:

1. Hide or delete the original solid bodies once meshes are created and verified.
    
2. Keep only the mesh objects selected for export.
    

### 4.5 Export OBJ + MTL

1. Select all mesh objects to export.
    
2. Choose `File > Export…` and select `Wavefront OBJ (.obj)`.
    
3. Export as:
    
    `ProjectName_Assembly_unity.obj`
    

FreeCAD will generate:

- `ProjectName_Assembly_unity.obj``
        

Units note:

- CAD and FreeCAD are kept in millimetres.
    
- OBJ is "unitless". Unity will interpret the numeric coordinates (millimetres -> metres).
    

---

## 5. Unity stage

### 5.1 Set origin and orientation

The Unity VIS template defines a fixed MR origin located at the centre of the virtual room. This origin should remain unchanged in Unity and serves as the spatial anchor for all assemblies placed in VIS scenes (to the shape of the room).

However, the Vive “centre of play” (SteamVR / Vive Hub room calibration) can change if the headset is recalibrated or if tracking is reset. When this happens, the virtual room outline in the Unity template will no longer align with the physical room.

#### To restore accurate spatial correspondence:

1. Determine the new Vive centre of play after recalibration.  
	This represents the physical position that SteamVR now considers the origin.
    
2. Set an MR origin offset in Unity so that the virtual origin aligns with the recalibrated Vive origin.
	A typical offset is:  
		`(0, <user eye height>, 0)`  
	but adjust as necessary based on calibration.
        
3. Reposition the assembly relative to the (fixed) Unity MR origin (or vice versa)
    This preserves consistency across all VIS scenes while compensating for Vive changes.
### 5.2 Import OBJ

1. In the Unity project, create a consistent folder structure, for example:
    
    - `Assets/Models/ProjectName/Source` for original OBJ + MTL
        
    - `Assets/Models/ProjectName/Prefabs` for prefabs
        
    - `Assets/Materials/ProjectName` for cleaned materials
        
2. Copy `ProjectName_Assembly_unity.obj` and `ProjectName_Assembly_unity.mtl` into the `Source` folder. Keep filenames identical except for extension.

3. Disable "import materials" if possible.
    
4. Let Unity complete the initial import so that meshes and provisional materials are generated.
    

Avoid moving or renaming these files outside Unity to preserve `.meta` links.

### 5.3 Verify assembly hierarchy

1. Drag the imported object into a test scene.
    
2. Expand the hierarchy:
    
    - Confirm that parts and subassemblies are preserved as separate child objects.
        
    - Check that names match the SolidWorks configuration in a recognisable way.
        

If the hierarchy collapsed into a single mesh, revisit the FreeCAD mesh creation step and ensure per-part meshes are generated and selected for export (if granularity is needed).
### 5.4 Fix mm-to-metre scaling

1. Select the imported OBJ asset in the Project window.
    
2. In Model Import Settings:
    
    - Set Scale Factor to `0.001`.
    
3. Validate scale by comparing a known length with the template’s "floor" which is a similar size to the VIS room. It should measure 1 Unity unit (1 m).
    

Do not rescale in FreeCAD or through additional GameObject scaling.

### 5.5 Materials and cleanup
The Unity template uses a small, shared set of canonical engineering materials (e.g. raw steel metal, painted metal, plastics, rubber, glass, wood, safety yellow). These are defined once under `Assets/Materials/Engineering` and reused across assemblies. More can be easily defined as needed.

1. **Create or update the VIS material palette** (once per project):

   Existing materials:
   - `Metal_RawSteel`, `Metal_Painted_Dark`, `Metal_Aluminium`
   - `Plastic_Black`, `Plastic_LightGrey`
   - `Rubber_Black`
   - `Wood_Light`, `Wood_Dark`
   - `Highlight_SafetyYellow`

   Use `Standard` (Built-in) rendering, with physically plausible metallic/smoothness values.

2. **Handle imported materials from OBJ/MTL**:

   - Option A (recommended): In the model’s Import Settings, disable `Import Materials` and assign only the VIS palette materials to meshes.
   - Option B: Let Unity create materials from `.mtl`, then remap each imported material (they will all be white and not modifiable) to one of the VIS palette materials and delete the auto-generated ones.

3. **Map parts to VIS materials**:

   - At the model asset level (Model → Materials tab), map each material slot to a VIS palette material based on real-world material (metal, plastic, wood, etc).
   - Where the imported material names are not meaningful (e.g. `Material.001`), assign materials per-part in the prefab based on functional role.

4. **Clean up**:

   - Ensure that each assembly uses a small number of shared materials rather than unique materials per part, to keep draw calls and complexity low.
   - Rename any remaining model-specific materials clearly if they have to exist (e.g. `Guard-Polycarbonate-Transparent`).

### 5.6 Prepare and store a prefab

1. With the assembly instance in the scene, add:
    
    - Colliders (mesh, box or capsule as appropriate).
        
    - Any script components or interaction components required by the MR template.
        
2. Drag the configured GameObject into the `Prefabs` folder to create a prefab.
    
3. Reference this prefab in MR scene templates for RC545a rather than re-importing OBJ files.
>>>>>>> origin/main
    

---

<<<<<<< HEAD
## 5. Import STEP into Blender and clean
1. Open Blender.
2. Set scene units:
    - `Scene Properties > Units`
    - Unit System: `Metric`
    - Unit Scale: `1.0`
    - Length: `Meters`
3. Delete the default cube, camera and light for a clean scene.
4. Import the STEP file:
    - `File > Import > STEP (.stp, .step)`
    - Select your `ProjectName_ModelName_MR_v1.step`
5. After import, perform basic checks:
    - Select the main assembly and press `N` to open the properties panel.
    - Look at the dimensions in meters.
        - Example: A 1.2 metre base should show roughly 1.2 in Y or Z depending on orientation.
6. Fix orientation if needed:
    - Rotate the whole assembly so that:
        - Z axis = up
        - X axis = right
        - Y axis = forward
    - Use `R` key with 90 degree rotations and then `Ctrl+A > Apply Rotation` when correct.
7. Clean the hierarchy:
    - Remove stray empty objects that are not needed.
    - Optionally join logically related meshes (for example join fasteners to the main housing) using `Ctrl+J`.
8. Set a clean origin for the assembly:
    - Select all parts
    - `Object > Set Origin > Origin to Geometry` or `Origin to 3D Cursor` depending on your desired pivot
    - Keep the base of the object at world Z = 0 if possible.
9. Apply transforms before export:
    - Select all objects belonging to the model
    - `Ctrl+A > Apply > All Transforms`

Save a Blender file if you want a reusable source:  
`ProjectName_ModelName_MR_v1.blend`

---

## 6. Export from Blender to FBX
1. Select the top level assembly object and all children.
2. Go to `File > Export > FBX (.fbx)`.
3. In the FBX export settings panel on the right, set:
    - Limit to
        - Check `Selected Objects`    
    - Transform
        - Scale: `1.00`
        - Apply Scalings: `FBX All`
        - Forward: `-Z Forward`
        - Up: `Y Up`
        - Apply Unit: checked
    - **Geometry**
        - Smoothing: `Face` or `Normals Only`
        - Apply Modifiers: checked
    - **Armatures**
        - Add Leaf Bones: unchecked
4. Export as  
    `ProjectName_ModelName_MR_v1.fbx`

---

## 7. Import FBX into Unity
1. Copy `ProjectName_ModelName_MR_v1.fbx` into your Unity project folder:  
    `Assets/Models/ProjectName/`
2. In Unity, select the FBX in the **Project** window.
3. In the **Inspector**, set the **Model** import options:
    - Scale Factor: `1`
    - Convert Units: enabled if available
    - Mesh Compression: `Off` for now
    - Read/Write Enabled: `Off` unless you explicitly need it
    - Normals: `Calculate`
    - Smoothing Angle: `60` (typical starting value)
4. Apply the import.
5. Drag the FBX into a test scene:
    - Place at position `(0, 0, 0)`
    - Confirm the model size matches expectations in metres
    - Confirm orientation is correct relative to your XR Origin and floor.

---

## 8. Integrate into the Vive passthrough template scene
1. Open the Vive passthrough template scene in Unity. 
2. Drag the imported model prefab into the scene as a child of a `Models` or `Environment` GameObject.
3. Position the model so that:
    - The base sits on Unity Y = 0 (floor plane)
    - It is in front of the user starting position, typically two metres forward on Z.
4. Enter Play mode with the headset on and confirm:
    - Scale feels correct relative to your body in passthrough.
    - You can walk around the object safely in the physical room.
    - No major clipping or performance issues occur.

If scale is wrong, correct it at the FBX import level or in Blender, not by applying arbitrary local scale in the scene.

---

## 9. Save as a reusable VIS demo scene

1. Save the scene as  
    `Scenes/VIS_Demo_ProjectName_ModelName.unity`
2. If this is a general template, create a base scene called  
    `Scenes/VIS_Template_DesignReview.unity`  
    and document that staff should:
    - Duplicate the template scene
    - Replace the model FBX
    - Adjust position only.
=======
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
        
    - Export OBJ with materials disabled, keep files together on first Unity import, and set Unity Scale Factor to `0.001`.
        
    - Standardise origin, orientation and folder structures so that assemblies can be dropped into shared RC545a MR templates with minimal adjustment.
>>>>>>> origin/main
