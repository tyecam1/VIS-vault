---
sop_id: SOP-VIVE-3D-DESIGN-01
asset_group: rc545a-vis
type: vis_sop
name: Vive 3D design visualisation pipeline
purpose: A repeatable pipeline to take SolidWorks assemblies into Unity for mixed reality visualisation on Vive headsets.
intended_users:
  - Designers conducting CAD design reviews
  - Researchers visualising robot cells and workstations
  - Staff preparing MR demonstrations in RC545a VIS
status: draft
last_tested: 25-12-4
owner: tye.cameron-robson@strath.ac.uk
---
# **Unity 3D Design Visualisation Pipeline**

SolidWorks to Unity Mixed-Reality Visualisation (Vive Focus Vision)  
RC545a Visualisation & Interaction Suite (VIS)

---

## **0. Vive Focus Vision Wireless Connection to VIS Workstation**

Prior to launching Unity-based mixed-reality (MR) scenes, the Vive Focus Vision headset must be wirelessly connected to the VIS workstation PC.

### **0.1 Network configuration**

Both the headset and the workstation must be connected to the same low-latency wireless network:

- **VIS workstation PC:** `RC545-IoT-5GHz`
    
- **Vive Focus Vision headset:** `RC545-IoT-5GHz` (via _Settings → Network_)
    
- **Password:** RC545IoT!! 

Use of the 5 GHz IoT network is required to ensure sufficient bandwidth and stability for MR streaming and passthrough operation.

---

### **0.2 Establish headset-to-PC connection**

1. Put on the Vive Focus Vision headset.
    
2. From the headset home interface, launch **Vive Streaming**
    
3. Select the **VIS workstation PC** from the list of detected devices on the network.
    
4. Confirm the pairing request on both the headset and the PC if prompted.
    

Upon successful connection, the PC will expose the headset to SteamVR/OpenXR as a wireless MR device.

---

### **0.3 Connection verification**

Before proceeding:

- Confirm headset tracking is active and stable.
    
- Confirm SteamVR or the Vive Streaming status interface reports the headset as connected.
    
- Confirm passthrough functionality is available.
    

Once verified, proceed to the geometry and visualisation workflow below.

---

## **1. Purpose**

Define a repeatable, documented pipeline for transferring SolidWorks assemblies into Unity for mixed-reality (MR) visualisation on OpenXR-compatible headsets used within RC545a VIS (e.g. Vive Focus Vision, Vive/Index, Meta Quest).

Primary use cases:

- Design review of products and mechanisms
    
- Visualisation of robot cells and workstations
    
- MR demonstrations for teaching, research, and external engagement
    

---

## **2. Standard VIS Geometry Pipeline**

Standard pipeline for mechanical assemblies:

`SolidWorks → STEP → FreeCAD → OBJ → Unity`

### **Scope and constraints**

- Preserve assembly hierarchy so parts and subassemblies remain addressable in Unity.
    
- Maintain correct physical scale (1 Unity unit = 1 metre).
    
- Keep triangle counts suitable for real-time rendering on standalone and tethered headsets.
    
- Use SolidWorks appearances only as material hints; final materials are defined in Unity.
    

### **Tools**

- SolidWorks 2021 Education
    
- FreeCAD (STEP import, mesh generation, OBJ export)
    
- Unity (URP or Built-in Render Pipeline, units set to metres)
    

### **Rules of thumb**

- Small mechanism: target < 250k triangles
    
- Full workstation / robot cell: target < 750k triangles
    
- Always import mm-based CAD into Unity with **Scale Factor = 0.001**
    

---

## **3. SolidWorks Stage**

### **3.1 Create a dedicated export configuration**

1. Open the assembly in SolidWorks.
    
2. Create a new configuration (e.g. `Unity_Export`) and activate it.
    
3. Simplify the model for MR:
    
    - Suppress bolts, nuts, washers, screws, pins, and cosmetic fasteners unless required for context.
        
    - Suppress fully internal components not visible in MR.
        
    - Suppress cosmetic threads and very small fillets/chamfers that do not affect silhouette.
        
    - Keep moving subsystems as separate subassemblies (e.g. base, links, gripper, guarding).
        
    - Use clear, stable naming for parts and subassemblies.
        

This configuration is the canonical VIS export source.

---

### **3.2 Set origin and orientation**

The Unity VIS template defines a **fixed MR origin** aligned to the physical room. This origin should not be modified in Unity.

The Vive “centre of play” (SteamVR room calibration) may change if the tracking space is recalibrated. When this occurs, spatial alignment is restored by adjusting offsets relative to the fixed Unity origin, not by moving the Unity origin itself.

Guidance:

- Maintain Unity’s default axis convention:
    
    - **Y up**, **Z forward**, **X right**.
        
- Place the SolidWorks global origin at a physically meaningful datum (typically the centre of the robot base or primary fixture at floor level).
    
- If the Vive centre of play changes:
    
    - Apply an MR origin offset in Unity corresponding to the user’s eye height (typically `(0, user height, 0)`).
        
    - Adjust the placement of assemblies relative to the MR origin as required.
        

This preserves consistent spatial behaviour across scenes and recalibrations.

---

### **3.3 Assign appearances for materials**

SolidWorks appearances are used as indicators.

- Assign appearances at part or body level so identical physical materials share one appearance.
    
- Reuse appearances wherever possible.
    
- Use appearances to distinguish material classes and functional groups (metal, plastic, rubber, wood, safety/highlight).
    

Final appearance is always defined in Unity using the material palette.

---

### **3.4 Export STEP**

1. With `Unity_Export` active, select _File → Save As…_ and choose **STEP AP214** or **AP242**.
    
2. In options:
    
    - Output: Assembly
        
    - Enable colour export
        
    - Units: millimetres
        
3. Save as:
    

`ProjectName_Assembly_unity_export.step`

This STEP file is the canonical CAD export for VIS.

---

## **4. FreeCAD Stage**

### **4.1 Import STEP**

1. Open FreeCAD and load `ProjectName_Assembly_unity_export.step`.
    
2. Confirm that the model tree reflects the intended assembly hierarchy.
    

---

### **4.2 Optional cleanup**

If required:

- Hide or delete fully internal or irrelevant parts.
    
- Prefer hiding over deletion to preserve reusable exports.
    

---

### **4.3 Generate meshes**

1. Switch to the Part workbench.
    
2. Select solids (per part or logical group).
    
3. Use _Part → Create mesh from shape_.
    

Recommended starting values:

- Linear deflection: **0.5 mm**
    
- Angular deflection: **10°**
    

Inspect meshes to ensure acceptable smoothness without excessive triangle counts.

---

### **4.4 Remove solids (recommended)**

After verifying meshes:

- Hide or delete the original solids.
    
- Retain only mesh objects for export.
    

---

### **4.5 Export OBJ**

1. Select all mesh objects.
    
2. _File → Export…_ → **Wavefront OBJ (.obj)**.
    
3. Enable export of basic colour/material information if available.
    
4. Export as:
    

`ProjectName_Assembly_unity.obj`

The `.mtl` file, if generated, is treated as optional metadata only.

---

## **5. Unity Stage**

### **5.1 Import geometry**

1. Create folders:
    
    - `Assets/Models/ProjectName/Source`
        
    - `Assets/Models/ProjectName/Prefabs`
        
    - `Assets/Materials/Engineering`
        
2. Copy the OBJ (and optional MTL) into the Source folder.
    
3. Allow Unity to complete initial import.
    

---

### **5.2 Set scale**

1. Select the OBJ asset.
    
2. Set **Scale Factor = 0.001**.
    
3. Apply and verify against known dimensions.
    

---

### **5.3 Verify hierarchy**

- Drag the model into a test scene.
    
- Confirm that parts and subassemblies appear as separate child objects with recognisable names.
    

If collapsed, revisit FreeCAD meshing and export selection.

---

### **5.4 Materials

VIS uses a small, shared material palette defined in Unity.

Typical materials include:

- Structural and painted metals
    
- Plastics
    
- Rubber
    
- Glass
    
- Light and dark wood
    
- Safety/highlight materials
    

Procedure:

- Disable or ignore auto-generated materials where possible.
    
- Map imported material slots to VIS palette materials at the model asset or prefab level.
    
- Use as few shared materials as practical to minimise draw calls.
    

The Unity material palette is the **single source of truth** for visual appearance.

---

### **5.5 Prefab preparation**

1. Add required components (colliders, layers, tags, interaction scripts if needed).
    
2. Drag the configured assembly into the Prefabs folder.
    
3. Reference prefabs in VIS MR scenes rather than re-importing geometry.
    

---

## **6. Summary and Good Practice**

- This SOP defines the standard VIS pipeline for geometry-accurate MR visualisation.
    
- Geometry is triangulated; design history and constraints do not transfer.
    
- Materials are standardised in Unity using a shared engineering palette.
    
- Always export from a dedicated SolidWorks configuration.