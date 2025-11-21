---
sop_id: SOP-QUEST-REMOTE-COLLAB-01
asset_group: rc545a-vis
type: vis_sop
name: Quest remote collaboration workflow
relates_to_kit: "[[KIT-QUEST-REMOTE-01]]"
purpose: |
  Run remote MR or VR collaboration sessions in RC545a using the Quest headsets and RC545a VIS workstation or laptops, with observers able to watch on the LG display.
intended_users:
  - Staff running MR design reviews
  - Researchers conducting MR user studies
  - Students delivering XR presentations
status: draft
last_tested:
owner: ""
---
# Quest remote collaboration workflow

## 1. Preconditions

- Room: RC545a VIS is booked in Microsoft 365.
- Kit: [[KIT-QUEST-REMOTE-01]] is available and not in maintenance.
- Accounts:
  - University Microsoft 365 account for Teams or equivalent.
  - Meta account for each Quest headset being used.
- PC:
    - Meta Quest PC app installed (if using Link)
    - Unity / Unreal project installed if needed

## 2. Room and hardware setup

1. Unfold the main table and place it in the center of RC545a.
2. Position 2 to 4 chairs around the table or push them back for standing sessions.
3. Switch on the LG TV (VIS-DSP-LG65-01) and set the correct HDMI input.
4. From Shelf A, take the Quest kit:
   - Headset [[HMD-META-KW49-01]]
   - Headset [[HMD-META-KW49-02]]
   - Controllers and chargers for each
5. Visually inspect headsets and controllers:
   - Check for obvious damage
   - Check lenses are clean
6. Power on each headset and check the battery level.
   - If below approximately 40 percent, connect to chargers at the charging station until adequate.

## 3. Network and account setup

1. Confirm the GL iNet router or university WiFi network to be used:
   - For controlled experiments, prefer a dedicated SSID if available.
2. On each Quest headset:
   - Open Settings - WiFi - connect to the chosen network.
   - Verify internet access by briefly opening the browser or store.
3. Sign in to the correct Meta account on each device:
   - Ensure study or teaching account is used, not personal accounts where possible.
4. On the VIS workstation:
   - Sign in to the correct Microsoft 365 account.
   - Sign in to Teams or the relevant collaboration app.

## 4. Choose workflow variant

Pick one of these patterns and follow the relevant section.

- Variant A - Standalone MR with remote audience on Teams  
- Variant B - PC VR via Link with Unity or Unreal content  

---

## 4A. Variant A - Standalone MR with Teams observer view

1. On the VIS workstation, start a Teams meeting:
   - Invite remote participants.
   - Share the LG TV view if needed for people in the room.
2. On each Quest, launch the collaboration or MR app you plan to use:
   - Example: remote collaboration tool, MR viewer etc.
3. If the Quest app supports casting to a browser:
   - On the PC, open the casting URL or Meta casting page.
   - Mirror that browser window into the Teams call.
4. Check:
   - Remote participants can see at least one headset view.
   - Audio routing is clear and there is minimal echo in the room.
5. Begin the session and monitor:
   - Comfort
   - Tracking performance
   - Any network lag

---

## 4B. Variant B - PC VR with Unity or Unreal

1. On the VIS workstation:
   - Launch the Meta Quest PC app.
   - Confirm it can see the Quest device when connected.
2. Connect the Quest to the PC with a suitable USB C link cable.
3. Inside the headset:
   - Accept the Link prompt to enter PC VR mode.
4. Start Unity or Unreal on the PC and open the required project or build.
5. Enter Play mode or launch the built application:
   - Confirm the headset display shows the scene.
   - Confirm controller inputs work as expected.
6. Use the LG TV to mirror the game view:
   - In Unity, use the Game view.
   - In Unreal, use the Play output window.
7. If remote participants are present:
   - Start a Teams meeting on the PC.
   - Share the application window or the entire display with them.

---

## 5. Safety and comfort

- Ensure free floor area around the user is clear of chairs, bags and cables.
- Confirm guardian or boundary setup in the Quest if the user will move around.
- Remind participants:
  - To mention immediately if they feel discomfort, dizziness or eye strain.
  - To remove the headset slowly and sit down if needed.
- If using a cable:
  - Route it over the back of the user and keep it off the floor where possible.
  - Avoid tripping hazards in the main walkways.

---

## 6. Ending the session

1. In software:
   - Exit the Unity or Unreal application or MR app.
   - Leave or end the Teams meeting.
2. On the Quests:
   - Return to the main menu.
   - Power off fully or place into sleep mode.
3. On the PC:
   - Stop any recordings.
   - Save session logs or notes if this is part of a study.
4. Hardware:
   - Wipe headset face interfaces and controllers with approved wipes if applicable.
   - Coil cables neatly.
   - Return headsets and controllers to their bins on Shelf A.
5. Charging:
   - Put headsets and controllers on charge if they are below about 70 percent.
   - Ensure chargers are connected to the correct devices.

---

## 7. Troubleshooting checklist

- Headset not found by PC:
  - Check cable is data capable, not charge only.
  - Try a different USB port on the workstation.
  - Confirm Quest Link is enabled in headset settings.
- Poor tracking or drift:
  - Clean lenses and cameras if smudged.
  - Check lighting in the room - avoid extreme brightness or darkness.
  - Restart headset if tracking remains unstable.
- Remote participants cannot see view:
  - Check you are sharing the correct window in Teams.
  - Confirm casting or PC mirror target is not hidden behind other windows.
- Severe or repeat issues:
  - Log a fault note for the affected asset in the vault.
  - Set the asset status to maintenance if it should not be used until fixed.

---

## 8. Version and review

- This SOP should be updated when:
  - New collaboration apps are adopted.
  - The workstation hardware changes.
  - Feedback from studies suggests improvements.
- When the workflow has been tested end to end, set `status: tested` and record `last_tested` at the top of this note.
