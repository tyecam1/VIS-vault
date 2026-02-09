# Unity 2022 Configuration

1. The HTC Vive package was installed incorrectly. Fixing requires far longer than setting it up from the beginning, correctly.
2. XR conflicts with a lot of build in rendering in unity. Built in RP (not URP) is a good baseline.
3. There are two HTC passthrough paths:
	1. Legacy (deprecated) – `VIVE XR Composition Layer (Passthrough)` + `CompositionLayerPassthroughAPI`. Still works and is a valid fallback.
	2. New – `VIVE XR Passthrough` + `PassthroughAPI`. Preferred path, but only works if the runtime exposes `XR_HTC_passthrough` + `XR_HTC_passthrough_configuration`.  
    Old guides are only usable if you check which API familythey’re using.
4. Headset, firmware, viveHub, openXR runtime all needed a specific configuration.
	1. For VIVE XR Passthrough to work, the required OpenXR extension must be configured:
		1. Headset ROM must be on a release that exposes `XR_HTC_passthrough` and `XR_HTC_passthrough_configuration` (e.g. Focus Vision ≥ 7.0.999.228).
		2. PC streaming (VIVE Hub / SteamVR) must be configured and on a version that forwards those extensions.
5. SteamVR must be set as the **OpenXR runtime**, with MR/OpenXR layers enabled.  
    If Unity logs “`XR_HTC_passthrough_configuration is NOT enabled`”, the problem is here, **not** in your C#.
6. For underlay passthrough ( legacy and new APIs), the XR camera background must be transparent, otherwise you just see solid colour:
	1. Built-in RP: Clear Flags = Solid Color, background alpha = 0.
	2. URP: Base camera, Environment → Solid Color, alpha = 0.  
	    If passthrough still shows black with a transparent background, it’s a runtime/extension issue, not a camera setting.
7. Due to the varying hardware and software configurations, there are many useless settings, or settings that configuring will break the XR environment.
8. We explored PC tethered and standalone android methods of viewing 3D models. Due to the simulating capacity of a desktop, we ignored android standalone (did not process on the headset). There are further settings in Unity that are only for android, and these add confusion.
9. Ensure basic VR is working, i.e. one camera with a tracked pose driver and a simple object to view.
10. There has been a recent deprecation of tracked pose drivers. The legacy variant (com.unity.xr.legacyinputhelpers) is the pragmatic version. Device: Generic XR, Pose: Centre Eye.
11. Unity 6 + Magic Leap package + URP (standard rendering) = compile errors. Magic leap is difficult to remove, even if you delete it from the manifest.json list of plugins, it lingers and blocks play mode.
12. When VR was working, the cube and environment was black. Even with working XR, you will see nothing if:
	1. The object is behind the camera,
	2. The camera’s culling mask doesn’t include that layer,
	3. Another camera is actually rendering the VR view.
13. We tested wired connection, but the headset would not enumerate as a USB (all wiring correct). We proceeded with wireless. We assumed it due to the university managed machine and security (you may have a temporary admin password, but you likely never run a session as administrator). This is suitable for modern MR. It was not caused by:
	1. Drivers in Unity
	2. XR Interaction Toolkit setup
	3. OpenXR runtime selection
	4. SteamVR configuration
	5. GPU capability
	6. Wrong cable
	7. Wrong DP port
	8. Wrong USB spec
14. Unity platform choice:
	1. Windows OpenXR is correct for SteamVR
	2. Android OpenXR is not required
	3. Passthrough works on Windows PC Streaming, despite some documentation (outdated?) claiming it only works on android standalone
15. Found Vive OpenXR plugin 2.5.1 to be the only usable version at the time.
16. Vive input utility is obsolete and should not be used
17. Passthrough is driven by OpenXR features, not legacy Wave or SteamVR components.
18. Working configuration:
	1. OpenXR enabled
	2. ViveXR Support enabled
	3. ViveXR Passthrough Enabled
	4. ViveXR Composition Layer (Passthrough) disabled
	5. All unrelated OpenXR features disabled
19. In 2.5.1, passthrough is not exposed as a simple add component item. It is enabled through HTC sample scene scripts, or direct API calls via VivePassthrough (OpenXRFeature)
20. Sample scenes:
	1. passthrough_configuration
	2. passthrough_planar
	3. passthrough_projection
		1. Verify it works in sample.
		2. Copy the game object into your scene
21. Camera configuration. Skybox completely hides passthrough, HDR breaks HTC passthrough layers, both blocks rendering.
	1. Clear flags: Solid Colour
	2. Background: Black
	3. HDR: Off
22. Render pipeline
	1. URP not required for passthrough
	2. Built-in pipeline fully supported and simpler
	3. No MR-specific renderer
	4. URP only effects geometry
	5. Get it working, then add URP if needed for lighting or performance
23. Working configuration:
	1. 2022.3 LTS
	2. XR Backend: OpenXR (Windows)
	3. Plugin: Vive OpenXR 2.5.1
	4. Passthrough method: Vive XR Passthrough (not composition layer)
	5. Camera:
		1. Solid Colour
		2. Black background
		3. HDR off
	6. passthrough_planer sample copied into new scene
24. Main blockers:
	1. Lack of documentation for latest versions
	2. Deprecated feature naming
	3. Default settings that block passthrough
	4. Copy sample logic wherever possible, inspector components will not help.
25. XR projects are highly sensitive to over-configuration.  
	1. Stability is achieved by disabling unrelated features, validating one capability end-to-end, and only then layering complexity.

Template

26. An example robotic workspace demo was selected as the most strategically effective MR demo
	1. It aligns with DMEM and RC545a research (Human Robot Collaboration)
	2. Demonstrates scale, reach, ergonomics, interaction
	3. Maps directly to the conceptualisation workflow in the VIS brief
	4. Provides immediate academic relevance to staff and students
27. CAD -> Unity target format established (via FBX)
	1. SOLIDWORKS -> STEP -> Blender -> FBX -> Unity
	2. Engine agnostic, no licensing required, suitable for documentation
	3. However, assembly levels (individual parts) lose granularity with this setup. Not beneficial for physics based simulation
28. Unity 3D Design SOP created
	1. Full SOP from SOLIDWORKS assemblies to Unity
		1. Solidworks prep
		2. Step export settings
		3. Blender import, orientation, scale and hierarchy
		4. Fbx export settings for unity
		5. Unity import settings
	2. Targeting staff/ classes and handover
29. Unity template strategy established (scene set up without CAD model)
	1. The unity scene changes with the VR tracking origin.
30. If the plugin logs “`XR_HTC_passthrough_configuration is NOT enabled`”, it means the OpenXR runtime (ROM + streaming stack) doesn’t expose the extension the feature needs. 
31. When debugging XR features (passthrough, hand tracking, etc.), always:
    1. Open the sample scene for that feature.
    2. Run it with your current ROM/Hub/runtime.  
        If the sample doesn’t work, the environment is wrong. Only copy logic once the sample scene is working.
32. Don’t toggle OpenXR features in Play Mode.  
    Changing OpenXR / VIVE XR feature checkboxes while the editor is running does nothing. Also, in the editor there is a lot of memory leakage. Restarting is appropriate every hour or so.
33. Choose one VIVE passthrough API family per project.
    Mixing namespaces and handle types (CompositionLayer vs Passthrough) creates ambiguous types and nonsense errors.
    - New path: `VIVE XR Passthrough` + `VIVE.OpenXR.Passthrough.PassthroughAPI`.
    - Legacy path: `VIVE XR Composition Layer (Passthrough)` + `VIVE.OpenXR.CompositionLayer.Passthrough.CompositionLayerPassthroughAPI`.
34. Hierarchy position of passthrough scripts is irrelevant; GameObject is not  
    It doesn’t matter where `PassthroughManager` sits in the hierarchy. It does matter that the object and all parents are active when Play starts otherwise no layer is created.
35. Solidworks to Unity pipeline does not require blender. For retaining subassembly information, convert using freeCAD. Solidworks -> STEP -> FreeCAD -> OBJ -> Unity.
	1. Suppress internal and small components at the CAD stage
	2. Preserve assembly hierarchy for per-part manipulation in Unity
	3. Keep triangle counts reasonable
36. CAD is exported in mm -> Unity import scale factor 0.001.
	1. Unity is Y-up, Z-forward
	2. Template scene will move if the Vive centre of play is changed.
	3. HTC vive headset offers scene understanding, the functionality to recognise a room is possible (i.e. it doesn't matter if the centre of play moves), but it will require (difficult) setup. It also doesn't help that one of the walls in the VIS is glass.
37. .mtl material files exported from freecad are unusable placeholders.
	1. The default template has default materials that I created, i.e. wood, metal, plastic, etc.
	2. Probably easiest to remap or ignore custom materials in unity when importing.
38. Shaders are down to personal preference, as long as they render.

# Unreal 5.4 Configuration

1. Unreal was prioritised to collaborate with the farm project in DMEM, and for comparison with Unity usefulness
    2. work resulted in a reusable lab template and SOP     
2. Breadth across workflows > depth in one engine
    1. Unity + Unreal together strengthen VIS by offering multiple MR adaptations
3. Uses the same solidworks assets and conversions
4. Clarified Unreal Engine as a priority engine for the VIS conceptualising workflow.
5. Defined Unreal’s role as a reusable VR design review template
6. Unreal has a free app called "reality scan" where you can scan an object using your mobile phone and it generates a 3D model. It's a bit fiddly, but with careful setup, it works well. Good for remote visualisation. It has two modes:
	1. A static object that has photos taken from a multitude of angles
		1. This is the mode I tested. It becomes tricky due to changing backgrounds of each image, and it hates glass/ reflection.
	2. The object rotates with a static background (turntable best). This is the most accurate method for capture as you can keep a constant, plain background, though we don't have a turntable.
	3. IOS phones export OBJ, and android phones export GLB formats. Both can be ingested by unreal, though GLB is preferable due to material linkage.
	4. The meshes are complex and heavy, so they need optimisation. Nanite settings exist for this in Unreal.
	5. Verify the scan looks reasonable on the app before importing.
7. Assets require a lot more configuration in Unreal than in Unity:
	1. set a good pivot, typically in the middle of the object.
	2. Collision must be configured/ simplified for interaction and performance. 'collisions = simple' is the preference.
	3. Materials in Unreal also need to be created, unless you have a texture map (if not from solidworks, fbx files will contain this)
	4. A lot of this can be standardised via blueprint, but it is easy to become confused if you change a config and forget.
8. lighting and texture settings should be reviewed if performance is poor.
9. UE is still growing accustomed to MR, although it is the stable benchmark engine version. Many settings will just stop it from working. 5.4 is the only version that currently supports passthrough
10. SteamVR does not supply the passthrough in Unreal like it did for Unity. It is required for the runtime configuration, but the passthrough is not supplied.
11. It is very hard to migrate assets (meshes etc.) between Unreal Engine versions. It is faster as a fresh import each time.
12. Textures require a lot more configuration than in Unity. 
	1. sRGB off/ on depending on texture format. If the texture is 'packed' (ORM), sRGB must be off. 
13. Lighting and reflection must be tuned on the mesh itself.
	1. Use command DumpUnbuiltLightInteractions to see if there are any unbuilt lighting interactions causing visual issues.
	2. You must add a tightly tuned/ bounded lightmass importance volume (on the light source) otherwise Unreal will make a poor one and lighting/ build time and performance drops.
14. A lot more is achievable, but it comes at massive configuration time costs. I experimented with the Niagaras and simulating particles, but after an hour or two knew it would not be worth continuation within allocated hours.
15. Blueprints require very large learning overhead.
	1. The event graph contains the 'blueprint', which is a visual overview of how cpp header functions interact with one another. Custom functions can be created, but they likely already exist.
	2. Reference viewer and ctrl+F can be used in the event graph to trace dependencies and functions, variables etc.
16. Passthrough:
	1. Handled by a manager/ blueprint, which can be placed in any scene.
	2. Use an always-on planer underlay and not projected passthroughs (projected is like a texture you can apply to meshes, but the configuration is very confusing and requires updating every tick).
17. Developer sample scenes will not work out of the box.
	1. Each MR feature requires correct configuration in Unreal, and often your project's configuration will not suit this. The sample maps have working blueprints, but settings and extra configuration is required to use them.
	2. Project defaults and config do not migrate with the scene.
	3. The most notable cases being related to project default gameMode, Pawn autopossession, and input ownership.
18. Possession and map
	1. You can only possess one pawn at a time or risk breakage and disconnected camera-headset, features not working and lag.
	2. If you possess a pawn and also have that pawn's input set to yourself (i.e. player 0), the pawn will break and input will not be recieved.
	3. In the VIS template, the default gameMode is configured and in use.
	4. If migrating blueprints, assets or scenes from other projects keep this in mind.
19. One major cause of issues was the VR stereo rendering. For some reason, the editor only rendered lighting in one eye and caused intense visual glitching. Ensure that instanced stereo rendering is disabled in plugin settings.
20. Mesh not visible debug:
	1. Mesh not assigned
	2. Hidden in game
	3. wrong actor instance
	4. Collision/ visibility
	5. Tick moving the mesh out of view (if there is a transform blueprint)
21. I refactored a joysticks transform blueprint + existing functions and applied it to the sample mesh in the template. It required a lot of initial understanding, but is simple once learned.
22. I then custom made scaling and interaction blueprints, based on the default grabbing functions.
	1. Hard to explain
	2. A lot of debugging must be done while in game, i.e. there are default functions for displaying visual artifacts when a interaction or input is triggered etc.
	3. There are controller inputs and hand inputs. The hand inputs are inconsistent and I recommend only using controllers unless required.
		1. any motion controller or input that has 'interaction' in the title is strictly HAND ONLY.
		2. All other motion controllers are for the controllers (Left, Right, LeftAim, RightAim, LeftGrip, RightGrip primarily). It can be easily confusing, for example, the pinch input will not trigger via the controller trigger. 
		3. Debug inputs using debugspheres function within a blueprint. I.e. when you grab successfully the sphere will go green
		4. Vive trackers are also easily supported.
	4. Handover will require an induction with me (Tye)
23. Fixed Foveated rendering enabled improves performance
24. Vive Sharpening is post-processing and is ultimately user preference. It has settings in SteamVR, ViveHub, and in engine, I do not know how they interact if all enabled.
25. Grabbing
	1. Collisions need to be configured per-actor. Complicated meshes need their collision bounding boxes to be simplified.
	2. Components are within actors, but they are treated as separate entities. Be sure you are correctly using either one as appropriate within functions within blueprints.
	3. Trace and attach logic must be set up with the grabbing interface, component/actor correctly if 'hits' are not happening.
	4. A common issue I faced was sticky grabbing/ mesh teleportation upon release and grip
		1. This is due to state/ input sequencing and a tick triggering before the engine has realised grab/release.
		2. This signalled that relying on boolean logic is not alone suitable for interaction states, and I implemented a fix through implementing delays.
26. Two Hand Scaling Blueprint
	1. The objects pivot root MUST be in the centre. This can be done before importing the mesh, or you can make the mesh belong to a pivot within the component hierarchy, and place the mesh's centre on the pivot.
	2. Required unsuspected configuration for when two hands are attempting to grab the same object. The sample interface did not support grabbing with two hands, i.e. one at a time, so the blueprint transitions into a new state when two hands are used.

# HTC Vive

1. Wireless connection procedure
	1. Put on headset, connect to IoT 5Ghz
	2. Connect to PC on same network from within the headset UI
	3. Launch steam from within the headset UI
2. Simulations are bottlenecked by wifi connectivity and consistency
	1. Modem plugged into wall for a more stable electrical/ wifi supply
	2. People in the lab connect their phones to IoT. For important demos, it is reasonable to ask them to disconnect.
3. It is automatic from here, although the HTC Vive requires custom plugins to use in simulator environments.
4. You may need to configure vive hub streaming settings depending on current WiFi, GPU load, bitrate, in that order.
	1. Assume wifi problems first as the PC is very simulation-capable.
5. This headset has the greatest functionality for complicated scenarios
	1. You don't necessarily need controllers, and can setup hand tracking instead.
	2. Scene recognition and plane detection can localise the room within sessions, and ensure simulations are accurately placed to the room that you are standing in. Good if you need calibrated workstations.
	3. The extended configuration may be useful for specific use-cases.

# Meta Quest

1. Meta account linkage not required (can be a different meta account on PC and headset)
	1. It can be treated as a generic PC-VR in the lab
	2. It does effect store features (access to oculus libraries)
	3. Developer or experimental features may be locked to an account
	4. For research, visualisation, SteamVR and Unity, irrelevant
2. A lot more compatible with workflows than the Vive Focus Vision (Disable Vive plugin and it will work with less configuration).
# Valve Index

1. Again, this headset works a lot better out-of-the-box, similar to the Meta Quest. 
2. There is no conversion between APIs/ runtimes. It operates foundationally in steamVR, which boosts compatibility greatly.
# Asset Management

1. Made a start in the Github/ using obsidian. Github is useful for moderation and version control, but not for booking systems etc. Currently Tye is the moderator, but automation would be preferred for future use. Assets all made for existing equipment.
2. Physical Storage
	1. All vive equipment on the top shelf.
	2. Valve equipment on the second shelf down.
	3. Miscellanious interactive equipment on the third shelf down.
	4. Quests were removed
3. Github assets can contain fault information. But bookings should be integrated with outlook calendars


