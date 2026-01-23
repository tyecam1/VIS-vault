# RC545a VIS Work Log

## 2025-11-07

- **Hours:** 4
    
- **Activity:** Induction and contextual onboarding with Andrew and Alan.
    
- **Outcomes:** Clarified RC545a VIS scope, current equipment state, and immediate priorities.
    
- **Blockers:** None recorded.
    

## 2025-11-14

- **Hours:** 8
    
- **Activity:** Troubleshot existing HTC Vive and Unity configuration previously established.
    
- **Outcomes:** Identified blockers preventing a stable, repeatable workflow.
    

## 2025-11-21

- **Hours:** 8
    
- **Activity:** Restarted and re-established a working HTC Vive-Unity pipeline.
    
- **Outputs:**
    
    - Initiated asset notes.
        
    - Outlined repository and file structure for future maintenance and expansion.
    

## 2025-11-28

- **Hours:** 2
    
- **Activity:** Early-stage workflow consolidation under constrained access.
    
- **Outputs/Outcomes:**
    
    - Produced an initial SOP for Vive + SolidWorks 3D design workflow (baseline procedure; later expanded).
        
    - Monitored software installation progress and planned follow-up work pending environment availability.
        
- **Blockers:** Software installations pending due to limited availability of support (Jim out of office), delaying installations.
    

## 2025-12-04

- **Hours:** 8
    
- **Delivered:**
    
    - Finalised Unity XR pipeline for HTC Vive Focus Vision (untethered MR), including:
        
        - Passthrough-ready scene aligned to the physical room.
            
        - Validated demonstration scene.
            
        - Documented import workflow for engineering CAD models.
            
    - Reliable SolidWorks assembly conversion route: STEP (AP214/AP242) → FreeCAD → OBJ, Blender unnecessary for the core pipeline.
        
    - Updated inventory and dashboards:
        
        - Added missing Valve Index entry and updated headset structure for bookings/dashboards.
            
        - Created asset note `HMD-VALVE-INDEX-01`; flagged missing serial numbers and base station details.
            
        - Updated Vive mocap kit definition to reflect SteamVR headset availability.
            
    - Standardised documentation structure: reformatted **`work-log.md`** into a consistent daily-note format.
        
    - Rewrote `sop/SOP-VIVE-3D-DESIGN-01` to capture end-to-end workflow, including mesh cleaning guidance, tessellation settings, Unity scale correction, and material import/remapping notes.
        
- **Open issues:**
    
    - Standardise and document HTC Vive (SteamVR) setup, including SteamVR initialisation and wireless/IoT connection procedure.
        
    - Unreal Engine installation pending for colleague.
        
    - Meta Quest 2 setup incomplete, no validated workflow.
        

## 2025-12-09

- **Hours:** 6
    
- **Delivered:**
    
    - Programmed and applied a Unity canonical material palette (metals, plastics, rubber, wood, safety) to the Vive demo assembly; demo scene polished for showcasing.
        
    - Updated 3D design SOP, including:
        
        - Corrected origin/orientation guidance.
            
        - Documented Unity material palette usage.
            
        - Added Section 0 covering Vive Focus Vision connection to VIS workstation via RC545 IoT 5 GHz wifi.
            
    - Documented handling of Vive “centre of play” changes and realignment approach without altering Unity’s fixed MR origin.
        
    - Organised secure VR locker and replaced storage note with a clear access/return.
    

## 2026-01-09

- **Hours:** 8
    
- **Activity:** PC upgrade for higher-intensity work. Witnessed Unreal simulations unable to run previously.
    
- **Delivered:**
    
    - Completed a reset on a more powerful PC; reinstalled and reconfigured all required software for intensive future workloads.
        
    - Installed Valve Index brackets into RC545 (room 2).
        
    - Began configuration for a Unreal SOP for human machine interaction.

## 2026-01-14

- **Hours:** 9
    
- **Activity:** Unreal Engine 5.4 MR integration on HTC Vive Focus Vision, including passthrough validation, cross-project asset transfer, material reconstruction, lighting stabilisation, and a first-pass “extreme weather over mesh only” Niagara prototype.
    
- **Delivered:**
    
    - **Passthrough in Unreal (UE 5.4, Focus Vision):**
        
        - Established that passthrough was not available via the existing farm project configuration alone.
            
        - Located and ran the HTC passthrough sample content (the “passthrough sample map” was not present in the initial project; passthrough validated using the dedicated sample project/map).
            
        - Confirmed passthrough functioning in-editor and in-headset for Focus Vision under UE 5.4.
            
        - Assessed platform constraints and alternatives (Focus Vision vs Quest 2 vs Valve Index).
            
    - **Environment asset integration (farm model) into the passthrough sample map:**
        
        - Attempted Unreal “Migrate” from the farm project; migration produced folder structure but failed to bring the mesh correctly.
            
        - Established that passthrough is only enabled in UE5.4, **engine version incompatibility** (farm authored in UE 5.5, now in UE 5.4 for passthrough) due to plugin support.
            
        - Implemented a fallback transfer route by exporting the **static mesh and textures** from the source project and re-importing into the UE 5.4 passthrough project (no raw FBX available).
            
    - **Material and texture reconstruction in UE 5.4:**
        
        - Rebuilt materials from the available texture set (single texture source provided), applied to the imported mesh, and corrected rendering issues via material parameter adjustment.
            
        - Diagnosed and corrected “washed out / overly light” appearance as a lighting build state issue rather than purely material scaling.
            
- **Outcomes:** A working UE 5.4 passthrough baseline on Focus Vision with a transferred, re-imported environment mesh inside the passthrough sample map, lighting stabilised for large scale assets, and an initial pattern for bounds-constrained Niagara weather over the mesh only.
    
- **Blockers:**
    
    - **UE 5.5 → UE 5.4 incompatibility** prevented direct migration and constrained reuse of the original project content.
        
    - Lack of raw **FBX** and a complete PBR texture set limited material reconstruction and increased manual setup overhead.
        
    - Large world scale required explicit **Lightmass Importance Volume** management to avoid poor synthesis and long builds.

## 2026-01-23

- **Hours:** 8
    
- **Activity:** Unreal Engine 5.4 (Vive OpenXR) passthrough and interaction workflow integration for VIS; simplified sample Blueprints into a usable baseline.
    
- **Delivered:**
    
    - **Passthrough (UE 5.4)**
        
        - Confirmed passthrough works in the Vive sample maps but can fail after copying into VIS due to project defaults (GameMode/Pawn/Input) not matching the sample project.
            
        - Adapted the passthrough Blueprint so planar passthrough is always-on, and removed projected passthrough logic and its tick-based meshing.
            
    - **Object manipulation
        
        - Made a generic function that outputs transform any chosen mesh/component via the joysticks.
            
        - Debugged why a manipulated mesh may not appear: wrong actor, mesh not assigned, hidden-in-game, or tick pushing it out of view.
            
    - **Build/runtime issue**
        
        - Fixed StaticMesh→ProceduralMesh conversion warning by enabling **Allow CPU Access** on meshes that need `GetSectionFromStaticMesh` in packaged builds.
            
    - **Rendering issue**
        
        - Fixed shadow only in one eye as a VR stereo/render-path issue and identified settings to disable first.

---

## Totals

- **Total time logged:** **59 / 70 hours**
    
- **Remaining allocation:** **11 hours**
