## 1. Purpose

Define a repeatable pipeline to take SolidWorks assemblies into Unity for mixed reality visualisation on Vive headsets.

Target use cases:

- Design review of products and mechanisms
    
- Visualisation of robot cells and workstations
    
- MR demonstrations in RC545a VIS
    

---

## 2. Prerequisites

- SolidWorks installed and licensed
    
- Blender installed (latest LTS recommended)
    
- STEP import support in Blender (built in add-on or IT managed)
    
- Unity (project already set up with OpenXR, Vive plugin, and passthrough working)
    
- SteamVR and Vive Hub installed on the VIS machine
    

Unit convention for the pipeline: **1 Unity unit = 1 metre**

---

## 3. Step 1 – Prepare the assembly in SolidWorks

1. Open the **SLDASM** you want to export.
    
2. Resolve any **missing references** and **broken mates**. The assembly must rebuild cleanly.
    
3. Clean up the model:
    
    - Suppress tiny cosmetic details that do not matter for MR (fillet logos, screws that are not needed, internal threads).
        
    - Hide or suppress reference geometry and construction parts.
        
4. Set a sensible **origin and orientation**:
    
    - Base of the object at Z = 0 in SolidWorks.
        
    - Front view in SolidWorks should correspond to forward in Unity (Z positive).
        
5. Check **scale and units**:
    
    - Document units: millimetres.
        
    - Know the approximate overall size (for sanity checking later in Unity).
        
6. Save a dedicated configuration for MR, for example `Config_MR_Export`, with the correct suppressed parts and visibility.
    

---

## 4. Step 2 – Export from SolidWorks to STEP

1. With the `Config_MR_Export` active, go to  
    `File > Save As`
    
2. Set **Save as type** to `STEP AP214 (*.step, *.stp)`.
    
3. Click **Options** and set:
    
    - Output as: `AP214` (preferred)
        
    - Export: `Assembly`
        
    - Units: `Document units` (mm)
        
    - Do not export hidden components if you want a lighter file.
        
4. Save as  
    `ProjectName_ModelName_MR_v1.step`  
    in your shared VIS models folder.
    

---

## 5. Step 3 – Import STEP into Blender and clean

1. Open **Blender**.
    
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
        
        - Example: A 1.2 metre robot base should show roughly 1.2 in Y or Z depending on orientation.
            
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

## 6. Step 4 – Export from Blender to FBX

1. Select the top level assembly object and all children.
    
2. Go to `File > Export > FBX (.fbx)`.
    
3. In the FBX export settings panel on the right, set:
    
    - **Limit to**
        
        - Check `Selected Objects`
            
    - **Transform**
        
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

## 7. Step 5 – Import FBX into Unity

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

## 8. Step 6 – Integrate into the Vive passthrough template scene

1. Open your **Vive passthrough template scene** in Unity.
    
2. Locate the **XR Origin** and ensure it is at `(0, 0, 0)` or your chosen world origin.
    
3. Drag the imported model prefab into the scene as a child of a `Models` or `Environment` GameObject.
    
4. Position the model so that:
    
    - The base sits on Unity Y = 0 (floor plane)
        
    - It is in front of the user starting position, typically two metres forward on Z.
        
5. Enter Play mode with the headset on and confirm:
    
    - Scale feels correct relative to your body in passthrough.
        
    - You can walk around the object safely in the physical room.
        
    - No major clipping or performance issues occur.
        

If scale is wrong, correct it at the FBX import level or in Blender, not by applying arbitrary local scale in the scene.

---

## 9. Step 7 – Save as a reusable VIS demo scene

1. Save the scene as  
    `Scenes/VIS_Demo_ProjectName_ModelName.unity`
    
2. If this is a general template, create a base scene called  
    `Scenes/VIS_Template_DesignReview.unity`  
    and document that staff should:
    
    - Duplicate the template scene
        
    - Replace the model FBX
        
    - Adjust position only.