---
sop_id: SOP-REALITYSCAN-UNREAL-01
asset_group: rc545a-vis
type: vis_sop
name: Physical object to Unreal Engine asset via RealityScan
purpose: A repeatable pipeline to generate a textured 3D model from a real object using RealityScan and ingest it into Unreal Engine as a usable, performant asset.
intended_users:
  - Staff and students capturing prototypes for visualisation
  - Researchers generating scene assets for VR/MR demos
  - Designers creating quick digital twins of physical items
status: draft
last_tested: TBD
owner: tye.cameron-robson@strath.ac.uk
---

# **SOP-PHYSICAL-TO-UNREAL-MODEL-00**

Physical Item to Unreal Engine Asset (RealityScan)  
RC545a Visualisation & Interaction Suite (VIS)

---

## **0. Setup (device, space, and constraints)**

RealityScan is photogrammetry. With some setup, you get a 3D model generated using a mobile phone.

### **0.1 Tools**

- **RealityScan Mobile** (iOS or Android)
    
- **Epic Games account** (sign-in required)
    
- **Unreal Engine 5.x**
    
- Matte white/ grey backdrop (plain cloth/card), soft lights
    

### **0.2 Object suitability (do this before you start)**

RealityScan struggles with:

- Shiny, reflective, transparent, or glossy objects (metal chrome, glass, screens)
    
- Thin features (wires, clear edges), featureless surfaces (plain white/black), repetitive patterns
    
- Anything that moves (including fabric flutter, cables, plants)
    

Solutions:

- Temporarily dull the surface (matte spray or removable powder)
    
- Add removable visual features (masking tape markers) on featureless areas
    
- Scan subcomponents separately
    

RealityScan requires the object and background to remain static during capture.

### **0.3 Environment and lighting rules**

- Use consistent lighting. Avoid harsh shadows and specular highlights.
    
- Avoid direct sunlight changes during capture.
    
- Put the object on a plain, non-reflective background with strong contrast to the object.
    
- Keep the background static, no people walking behind you.
    

---

## **1. Purpose**

Define a repeatable pipeline for capturing a physical item and producing a textured 3D asset suitable for Unreal Engine use (visualisation, VR/MR scenes, design showcases).

Primary use cases:

- Rapid capture of prototypes for concept review
    
- Environmental props for VR/MR demos
    
- Digitising physical parts for reference and layout studies
    

---

## **2. Standard pipeline overview**

`Physical object → RealityScan capture → Cloud processing → Download model → Unreal import → Asset cleanup + optimisation → Packaged UE asset`

---

## **3. RealityScan capture stage (mobile)**

### **3.1 Create a new project**

1. Open RealityScan Mobile and sign in with your Epic Games account.
    
2. Create a **new scan project** with a name:
    
    - `YYMMDD_ObjectName_V01` (example: `260114_GearboxCover_V01`)
        
3. Verify your phone has enough storage and battery (photogrammetry capture requires many photos).
    

### **3.2 Capture strategy (what actually works)**

You need high overlap between neighbouring views. Target > 60% overlap.

Use a ring-based capture:

- **Ring 1 (low angle):** walk around object at low elevation
    
- **Ring 2 (mid angle):** repeat at mid elevation
    
- **Ring 3 (high angle):** repeat at high elevation looking down
    
- **Top coverage:** add top-down passes if the geometry needs it
    

Rules:

- Move smoothly. Avoid motion blur.
    
- Keep the object at similar screen size. Do not zoom.
    
- Do not “jump” viewpoints. Small steps, consistent overlap.
    
- If auto-capture is enabled, slow down so it can actually sample properly.
    
- RealityScan Mobile has an image limit (up to 300). Do not spam photos, but do not under-sample either.
    

### **3.3 Coverage check and re-shoot**

RealityScan provides capture feedback. Use it:

- If you see weak coverage on an area, do a local loop around that region.
    
- If alignment looks unstable, stop and add more overlap, do not “push through” and hope.
    

### **3.4 Crop before export (important)**

After processing, crop the reconstruction to exclude background. Cropping does not alter the original project and improves downstream usability.

---

## **4. Processing and export**

### **4.1 Process the scan**

1. Trigger processing in RealityScan (cloud-based).
    
2. After processing, review the model for:
    
    - Missing surfaces or holes
        
    - Warped geometry
        
    - Texture smearing
        

If it failed, the fix is almost always: **more overlap**, **less glare**, **more features**, or **a static background**.

### **4.2 Export or download format (platform-specific)**

RealityScan Mobile download formats:

- **iOS:** downloads in **OBJ** format
    
- **Android:** downloads in **GLB** format
    

Also, RealityScan can share to Sketchfab (optional route if you need an easy format download and settings control).

### **4.3 File management on the workstation**

Create a project folder:

- `...\VIS\RealityScan\YYMMDD_ObjectName_V01\`
    
    - `01_source\` (raw exports)
        
    - `02_working\` (any conversions, cleanup)
        
    - `03_unreal\` (UE project imports, screenshots, notes)
        

Copy exports off the phone immediately. Do not leave “the only copy” on a device.

---

## **5. Unreal Engine import stage**

### **5.1 Preferred import route (GLB / glTF)**

If you have **GLB** (Android export, or you converted to GLB), import via Unreal’s glTF pipeline:

1. In Unreal, use **File → Import Into Level** (or import into the Content Browser depending on your workflow).
    
2. Select the `.glb` file and import into:
    
    - `Content/Scans/ObjectName/Source`
        
3. After import, open the Static Mesh asset and verify:
    
    - Scale is sensible
        
    - Materials and textures are connected
        
    - Normals are not inverted
        

Unreal has explicit glTF support docs. Use them when something behaves oddly.

### **5.2 iOS OBJ import route (OBJ)**

If you only have **OBJ** (iOS export):

Option A (fastest, usually fine):

1. Import `.obj` into Unreal as a Static Mesh.
    
2. Confirm the texture files are also imported and the material references resolve.
    

Option B (more reliable materials, slightly more work):

1. Convert OBJ → GLB using a DCC tool (clean import and consolidated packaging).
    
2. Import the GLB using the glTF route above.
    

Pick one. Do not sink time into “tweaking OBJ import settings” when the real problem is missing or mis-referenced textures.

### **5.3 Scale and units sanity check**

Unreal units are **centimetres**.

Do a physical measurement check:

1. Measure one known dimension on the real item (eg, 120 mm).
     
2. In Unreal, use the Static Mesh bounds or a measuring tool.
    
3. If scale is wrong, apply a uniform scale correction at the asset level and document it in the asset notes:
    
    - `ScaleCorrection = X.XX`
        

Do not “eyeball scale” if this asset will be used in MR or layout work.

### **5.4 Make it usable: pivot, collision, and placement**

Minimum asset prep:

- Set pivot to a meaningful physical point (base contact or centre datum)
    
- Create collision:
    
    - Simple collision for interaction and performance
        
    - Complex-as-simple only if you truly need it (usually you do not)
        

---

## **6. Optimisation for real-time use**

Photogrammetry meshes can be heavy. Treat performance as a requirement, not an afterthought.

### **6.1 Nanite (desktop and high-end VR workflows)**

If the mesh is high-poly and used as a Static Mesh, enable Nanite where appropriate.

Reality check:

- Nanite helps with polycount, not with bad textures or bad captures.
    
- For some VR constraints, you may still want simplified meshes and classic LODs.
    

### **6.2 LODs (if you need distance scaling)**

Generate LODs if the object will be seen at multiple distances or used repeatedly.

RealityScan (desktop tooling) supports LOD export concepts; Unreal also supports LOD generation internally.

### **6.3 Texture discipline**

- Prefer 2K textures for most interactive scenes unless this is a hero asset.
    
- If you see massive texture memory spikes, reduce texture size and re-test.
    
- Photogrammetry textures often include baked lighting. In Unreal, this can look wrong under new lighting. Decide whether that is acceptable for your use case.
    

---

## **7. Packaging in Unreal (standard asset structure)**

Create folders:

- `Content/Scans/ObjectName/`
    
    - `Source/`
        
    - `Materials/`
        
    - `Textures/`
        
    - `Prefabs/` (Blueprint or placed actor prefabs)
        
    - `Docs/` (screenshots, quick notes)
        

Create:

1. A cleaned **Static Mesh** asset
    
2. A **Material Instance** (so you can tweak roughness, tint, AO without rewriting graphs)
    
3. A simple **Blueprint Actor** if you need consistent placement, scale lock, or interaction hooks
    

---

## **8. Quality checklist (pass or rework)**

Reject the asset if any of the following are true:

- The silhouette is wrong (warping, melted geometry)
    
- Large holes exist on visually important faces
    
- Texture is smeared on key features (logos, edges, corners)
    
- Scale is unverified
    
- Pivot makes placement annoying or inconsistent
    
- The asset is too heavy for the target platform and you have no optimisation plan
    

Common root causes:

- Not enough overlap (>60% is the baseline)
    
- Reflective surfaces and moving shadows
    
- Background clutter captured into the reconstruction (crop properly)
    
- Object or environment movement during capture
    

---

## **9. Summary and good practice**

- RealityScan is only “fast” when you obey capture discipline: static scene, diffuse light, high overlap, feature-rich surfaces.
    
- Export formats differ by platform: iOS OBJ, Android GLB. Plan your Unreal import route accordingly.
    
- In Unreal, make the asset usable: correct scale, sensible pivot, collision, and an organised content structure.
    
- Optimise deliberately: Nanite where it helps, LODs where they matter, textures kept under control.