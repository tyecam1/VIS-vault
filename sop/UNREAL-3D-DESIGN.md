---
sop_id: SOP-VIVE-3D-DESIGN-01
asset_group: rc545a-vis
type: vis_sop
name: Vive 3D design visualisation pipeline
relates_to_kit: "[[KIT-VIVE-MOCAP-01]]"
purpose: A repeatable pipeline to take SolidWorks assemblies into Unreal for mixed reality visualisation on Vive headsets.
intended_users:
  - Designers conducting CAD design reviews
  - Researchers visualising robot cells and workstations
  - Staff preparing MR demonstrations in RC545a VIS
status: draft
last_tested: 25-12-4
owner: tye.cameron-robson@strath.ac.uk
---
# Unreal 3D Design Visualisation Pipeline

SolidWorks to Unreal Engine Mixed-Reality Visualisation (Vive Focus Vision, Vive OpenXR)  
RC545a Visualisation & Interaction Suite (VIS)

---

## **0. Vive Focus Vision Wireless Connection to VIS Workstation**

### **0.1 Network configuration**

Both headset and workstation must be on the same low-latency network:

- **VIS workstation PC:** `RC545-IoT-5GHz`
    
- **Vive Focus Vision headset:** `RC545-IoT-5GHz` (Settings → Network)
    
- **Password:** `RC545IoT!!`
    

### **0.2 Establish headset-to-PC connection**

1. Put on the headset.
    
2. Launch **Vive Streaming**.
    
3. Select the **VIS workstation** from detected devices.
    
4. Confirm pairing on headset and PC if prompted.
    
5. Launch SteamVR from the headset
### **0.3 Connection verification**

Before opening Unreal:

- Tracking is stable.
    
- Vive Streaming shows connected.
    
- SteamVR/OpenXR sees the headset.
    
- Passthrough toggle functions in the headset environment (if applicable).
    

---

## **1. Purpose**

A repeatable pipeline for transferring SolidWorks assemblies into **Unreal Engine 5.4.4** for MR visualisation using the 'VIS' Unreal template and interaction blueprints (grab, move, two-hand scale).

Key outcomes:

- Correct scale and orientation.
    
- Controlled file structure and naming.
    
- Reuse of the proven Unreal template without editing the canonical template assets.
    
- Reliable swap-in of new mesh + textures only.
	
- Interaction baselines established
    

---

## **2. Standard VIS Geometry Pipeline (Unreal)**

`SolidWorks → (export) → Blender → FBX → Unreal (template copy)`

### **Scope and constraints**

- Preserve meaningful assembly structure where feasible.
    
- Maintain correct scale: **1 Unreal unit = 1 cm**.
    
- Keep triangle counts suitable for VR/MR. 
    
- Photogrammetry meshes often have skewed pivots; handle via pivot/root strategy.
    

### **Tools**

- SolidWorks (assembly authoring and configuration)
    
- Blender (import, cleanup, pivot/origin, FBX export)
    
- Unreal Engine 5.4.4 (Vive OpenXR project template)
    

### **Rules of thumb**

- Small mechanism: target < 250k triangles
    
- Full workstation/cell: target < 750k triangles
    
- Prefer 2K textures for VR unless there is a clear reason for 4K (data transmission constraints)
    

---

## **3. SolidWorks Stage**

### **3.1 Create a dedicated export configuration**

1. Simplify for MR:
    
    - Suppress bolts, nuts, washers, pins, threads unless needed.
        
    - Suppress internal components not visible.
        
    - Suppress tiny fillets/chamfers that do not affect silhouette.
        
    - Keep moving subsystems as separate subassemblies if interaction is required.
        
2. Ensure stable naming of major parts/subassemblies.
    

### **3.2 Origin and orientation**

Unreal uses **Z up** by default. You will handle axis conversion in Blender.

Guidance:

- Place SolidWorks origin at a meaningful datum (robot base centre at floor level is typical).
    
- If the SolidWorks origin is not meaningful (common in messy assemblies), do not attempt to “fix it” in SolidWorks if it risks breaking the design. Fix pivot/origin in Blender or Unreal.
    

### **3.3 Export from SolidWorks**

Preferred option (cleanest to Blender):

- Export as STEP AP214/AP242 for maximum CAD detail and plan to re-mesh in Blender.  
    Alternate option:
    

Naming:

- `ProjectName_UE_export.step`
    

---

## **4. Blender Stage (STEP → FBX)**

### **4.1 Import**

1. Open Blender.
    
2. Set Units:
    
    - Scene Properties → Units:
        
        - **Unit System: Metric**
            
        - **Length: Millimeters** (or meters if you prefer, but be consistent)
            
3. Import:
    
    - If STEP: use a STEP importer add-on workflow.
        

### **4.2 Clean hierarchy**

- Group logically: either keep separate objects for major parts, or join into one object if interactivity is only at “whole assembly” level.
    
- Rename key objects consistently:
    
    - `SM_ProjectName_Main`
        
    - `SM_ProjectName_SubAssembly_A`
        
    - etc.
        

### **4.3 Fix pivot/origin (critical for VR interaction)**

The pivot/ centre of rotation is vital during VR interaction.

For each object you intend to interact with in Unreal:

1. Select object.
    
2. Right click → **Set Origin**:
    
    - Use **Origin to Geometry** for most CAD.
        
    - Use **Origin to Center of Mass** only if you need physical behaviour.
        
3. If the geometry is far from the world origin:
    
    - Move the object so it is near (0,0,0) temporarily.
        
    - Then set origin.
        
    - Then move it back if needed.
        

4. If facing complications in Blender, fix in Unreal
    

### **4.4 Apply transforms**

Before export:

- Select object(s) → Ctrl+A:
    
    - Apply **Rotation**
        
    - Apply **Scale 1/10**  
        This prevents FBX import from producing weird scale factors or rotations.
        

### **4.5 Reduce complexity if required**

- If triangle count is excessive:
    
    - Use Decimate modifier carefully, or remesh strategy.
        

### **4.6 Export FBX**

1. File → Export → FBX
    
2. Recommended export settings:
    
    - Selected Objects: **On**
        
    - Apply Transform: **On**
        
    - Forward: **-Y Forward**
        
    - Up: **Z Up**
        
    - Smoothing: Face
        
    - Add Leaf Bones: **Off**
        
    - Bake Animation: **Off**
        
3. Naming:
    
    - `ProjectName_UE_export.fbx`
        

---

## **5. Unreal Engine Stage (Template Copy + Swap Mesh/Textures)**

### **5.1 Project rule: do not edit the canonical template**

The VIS Unreal project has a working baseline with interaction logic and MR settings.  
You must:

- **Duplicate** template maps and blueprint assets into a new project folder.
    
- Modify only the duplicates.
    

This prevents breakage of the good baseline.

### **5.2 Duplicate the template map**

1. Locate the working template level (e.g. `This PC/VIS/Unreal Projects/5.4/VIS).
    
2. Copy and Paste the entire VIS project into `5.4/`
    
3. Rename the directory and the project (they should match):

    - `VIS.umap`
    to
    - `YourProject.umap`    


### **5.4 The interaction blueprints**

 blueprints used by the /contents/VIS/template are within /contents/VIS/Blueprints. They are built upon versions of the sample plugin blueprints:

- Pawn (`BP_HandInteractionPawn`)
    
- Interactable object blueprint base (`BP_Design_Object`)
    
- Passthrough Enabler (BP_PasshthroughEnabler)
    

### **5.5 Import FBX**

1. Import into:
    
    - `ProjectName/Content/VIS/Meshes/`
        
2. In FBX import options:
    
    - Import Mesh: **On**
        
    - Import Materials: **Off** (recommended)
        
    - Import Textures: **Off** (recommended)
        
    - Combine Meshes: depends on your plan:
        
        - On if you want one interactable object
            
        - Off if you want component-level selection
            
3. Save.
    

### **5.6 Create / assign materials**

You will usually rebuild materials in Unreal:

1. Import textures into `Textures/`.
    
2. Create materials in `Materials/`:
    
    - `M_ProjectName_Base`
        
3. For each mesh:
    
    - Open Static Mesh → Materials slots → assign your new material(s).
        

If you have a full PBR set:

- Base Color
    
- Normal
    
- Roughness
    
- Metallic
    
- AO (optional)
    

If you only have one diffuse texture (common in scans):

- Use it as Base Color and manually tune roughness/metallic to avoid “black/white mirror” behaviour.
    

### **5.7 Swap mesh inside the duplicated blueprint**

This is the core “template reuse” step.

In the duplicated grabbable blueprint (e.g. `BP_Design_Object`):

1. Find the mesh component used for interaction (StaticMesh in the details tab, the component under PivotRoot).
    
2. Set its Static Mesh to your imported `SM_ProjectName...`
    
3. Verify collision:
    
    - Collision Enabled: Query Only (for traces) or Query+Physics if you need physics
        
    - Collision Preset: Block the trace channel you use for grabbing (often Visibility or a custom channel)
        
	- For complex meshes, be sure to set all collisions to simple, this makes the interactable bounding box simpler and saves performance.
   

### **5.9 Check Pawn and Passthrough Enabler**

In the duplicated map or project settings:

- Ensure the Pawn has autopossess set to player 0.
    
- Do not set Auto Receive Input on the pawn.
    
- Be sure to set Auto Recieve Input player 0 on the passthrough enabler.
    

### **5.10 Test in VR Preview**

Run:

- Play → VR Preview  
    Verify:
    
- Headset tracking OK
    
- Passthrough OK
    
- Controller inputs work
    
- Grab works
    
- Two-hand scaling works
    

---

## **6. Validation Checklist (Pass/Fail)**

### **Geometry**

- Scale correct (known dimension matches real-world)
    
- Pivot behaves (grab does not “orbit” around distant point)
    
- No extreme polycount lag
    
- Materials and lighting correct (not blown out, not pure black/white)
    
### **Interaction**
    
- Grab attaches to PivotRoot (if required)
    
- One-hand move works
    
- Two-hand scale works
    
- Release leaves object where released

### **Project integrity**

- Canonical template assets untouched
    
- All edits confined to `5.4/Your Project/Content/…`
    

---

## **7. Common Failures and Fixes**

- **Object moves away when scaling:** pivot/origin wrong. Fix: move the subcomponent in unreal to be closer to the BP_Design_Object's pivot subcomponent. (PivotRoot empty-parent strategy)
    
- **One-eye shadow/ lighting glitches:** instanced stereo can break per-eye shadow behaviour; use known-good rendering settings baseline.