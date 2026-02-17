# CAD to MR Files and Material Conversions

SolidWorks mostly stores:
- Appearances (color, simple textures, mapping rules)
- Not robust PBR 

---

# GLB vs STEP vs OBJ vs FBX

## STEP (CAD master)

- What it is: Exact CAD B-Rep geometry and assembly semantics.  
	
- What it’s good for: Accuracy, parametric accuracy, long-term source of record.  
	
- MR downside: Engines cannot render it directly. You must tessellate to triangles using a CAD converter.

Materials: Colours are often stripped when exporting/ importing.
Use STEP when: You need accuracy + repeatable regeneration from CAD, and materials/ colours are of little consequence.

---

## OBJ (dumb mesh)

- What it is: Old triangle mesh format with a basic material sidecar.  
	
- What it’s good for: Simple, universally supported geometry export.  
	
- MR downside: Weak scene structure, weak materials, brittle texture linking, no modern PBR workflow.
	
- Materials: Often stripped through conversion. (Come with .MTL files, but they are often blank. Can be useful for importing naming conventions)  
	
- Use OBJ when: You want the simplest mesh transfer.

---

## FBX (DCC pipeline glue)

- What it is: Common exchange format for Maya/Max/Blender style pipelines.  
	
- What it’s good for: Hierarchy, animation, rigs, cameras/lights, complex scene interchange.  
	
- MR downside: Import results vary by exporter/importer; material fidelity is inconsistent; you can get scale and hierarchy weirdness. Optimal for Unreal.
	
- Materials: Works if previously configured in blender, may need cleanup. 

- Use FBX when: You have animation/rigging or a DCC-first workflow already.

---

## glTF / GLB (realtime delivery standard)

- What it is: glTF is an open standard for realtime assets. GLB is glTF packaged as a single binary file (geometry + materials + textures together).  
	
- What it’s good for: Predictable import, XR-friendly, fast loading, native PBR material model.
	
- Materials: Proper PBR channels (base color, metalness, roughness, normal, emissive).  
	
- Use GLB when: You want repeatable prototyping and the GlTF/ GLB export configuration becomes worthwhile
	
- GLB is usually the lowest-hassle path for MR imports. It won’t preserve CAD detail, but it preserves what engines use.

---

# Decision Rule: What to Use When

## Pick GLB if you want:
- Fast MR review
- Low friction repeatable imports, i.e. frequent prototyping
- A single file that doesn’t break texture links
- And you don't mind finding/ configuring an exporter or converter from SOLIDWORKS

## Pick STEP + converter (Datasmith/PiXYZ/Blender/FreeCAD etc.) if you need:
- High accuracy from CAD every iteration
- Stable assembly structure and naming preserved
- Frequent re-export from CAD without hand-fixing meshes

## Avoid OBJ unless:
- You are okay redoing materials every time
- It's just a one-time conversion

## Use FBX if:
- You rely on Blender/Maya pipeline work (animation, rigs, scene staging)
