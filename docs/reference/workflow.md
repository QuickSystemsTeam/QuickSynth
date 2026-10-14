# 1. Workflow


1. Drag a **QuickSynth Manager** (the dataset: image count, outputs, which camera and volumes) and a **QuickSynth
   Capture Camera** into the level (Place Actors → QuickSynth).
2. Create an **Object Library** (Content Browser → right-click → QuickSynth → Object Library) with your objects:
   label, meshes, materials and default counts.
3. Drag one or more **QuickSynth Spawn Volumes** in, scale them over the area where objects should appear, and drop
   the Object Library into them (or add Spawn Entries right in the volume).
4. Optionally add a **QuickSynth Label** component to placed actors you also want labeled.
5. Start capturing, either way:
   - **QuickSynth toolbar button → Generate Dataset:** plays the level, captures, and stops playing when done.
   - **Play:** with *Capture On Play* on (the default), capture starts immediately. With it off, Play just runs the
     level; pick **QuickSynth → Start Capture** when you're ready.
   An editor notification shows progress, images per second and time left, with a **Stop** button; an on-screen
   line shows the same in the viewport. Stop or Esc ends the run early; files written so far are kept and the run
   is finalized.
6. Open the run folder: **QuickSynth → Open Output Folder**, or the link in the "done" notification (default
   `<Project>/Saved/QuickSynth/<DatasetName>_<timestamp>/`).

Capture always runs in a play session, never in the edited level, so spawned objects never touch the saved map.

### 1.1 QuickSynth toolbar button

On the level editor toolbar (next to the other plugin buttons). Its menu:

| Entry | When | What it does |
|---|---|---|
| Generate Dataset (*Generate* + Manager name when there are several) | not playing | Plays the level in the editor, runs that Manager (whatever its *Capture On Play* setting; other Managers stay idle), and stops playing when the run ends. |
| Start Capture (+ Manager name when there are several) | playing, not capturing | Starts a run in the running level. Play keeps running afterwards. |
| Stop Capture | capturing | Ends the run; captured images, labels and the report are kept. |
| Validate Scene | always | Checks the level's setup and lists problems in the Message Log (below). |
| Open Output Folder | always | The last run's folder, or the first Manager's output folder. |

### 1.2 Validate Scene

**QuickSynth → Validate Scene** checks the level you're editing and lists problems in the **Message Log**
(QuickSynth page); click an actor's name there to select it. *Generate Dataset* runs the same check first: errors
stop it (the Message Log opens), warnings don't.

| Check | Severity |
|---|---|
| No Manager, or no Capture Camera, in the level | error |
| Manager without a Capture Camera picked | error |
| Nothing will be labeled (no labeled library objects, spawn entries or Label components for a Manager) | error |
| A Manager's Spawn Volumes use different Object Libraries (ambiguous class numbers) | error |
| Object Library with two objects of the same name, or an object without a Label or Meshes | error |
| Orbit Target is Actor but Target Actor is empty; or orbiting volumes/spawned objects with no volume | error |
| Spawn entry without Meshes, or (not a distractor) without a Label | error |
| Snap To Ground is on but the volume floats above the ground or has no ground under it | error |
| Fixed camera that sees none of its Manager's Spawn Volumes | warning |
| Manager that lists no Spawn Volumes; a Spawn Volume or Lighting Randomizer that no Manager lists | warning |
| A label in use that isn't in the Object Library (it is numbered after the library's classes) | warning |
| No label format ticked | warning |
| More than one Manager with Capture On Play | warning |
| Volume that spawns nothing; entry with Count Range 0 | warning |
| Material problems (e.g. Pick From List with no materials) | warning |
| Label component without a Label, or on an actor with no visible mesh | warning |
| Lighting Randomizer with Sun or Sky on but no light picked, or with everything off | warning |

### 1.3 Label overlay and Dry Run

While capturing, the viewport shows the **last captured image with its labels**: a box per object in its class
color (the Object Library's color, or an automatic one), the class name and visible %, and filtered-out objects in grey
marked "(filtered out)". Because it shows the saved image rather than the live scene, the boxes always match what
was written, even though the scene has already moved on. With *View Through Camera During Capture* on it fills the
viewport (letterboxed); with it off it is a preview in the bottom-right corner so you can still fly around.

- **L** shows or hides it. **M** switches between the image, the **instance mask** (a color per object) and the
  **semantic mask** (class colors). Keys can be changed under Advanced.
- It is drawn on the viewport only (Unreal's debug canvas), which scene captures never see, so it can't appear in
  saved images.

**Dry Run** (Manager checkbox) runs exactly the same randomize → render → label loop, with the overlay, but writes
nothing: no folders, images, labels or report. Each image stays on screen for *Dry Run Seconds Per Image*. It runs
*Image Count* images (or until you stop it). Use it to check framing, label coverage and visibility filtering before a
long run.

