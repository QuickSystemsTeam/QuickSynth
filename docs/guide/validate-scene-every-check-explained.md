# 5. Validate Scene: every check explained


**What it does.** Validate Scene looks at the level you're editing (nothing needs to be playing) and finds setup
mistakes *before* you spend time generating. It checks every Manager, Capture Camera, Spawn Volume, Object Library,
spawn entry, Label component and Lighting Randomizer.

**Where the results go.** If anything is found, the **Message Log** opens on its **QuickSynth** page (you can also
open it from *Window → Message Log*). Each line names the actor it's about; **click the name to select that actor**.
If nothing is found, a notification says *"no problems found. Ready to generate."*

**Errors and warnings.**

- An **error** means the run can't produce a useful dataset. **Generate Dataset won't start until errors are fixed.**
- A **warning** means something is probably a mistake, but you might have done it on purpose. Generate still runs.

**When it passes.** "No problems found" means the setup is complete and consistent: there's a Manager and a camera,
something to label, valid spawn entries, ground under your volumes, and so on. It can't judge whether your images *look* right.
Use the label overlay or a Dry Run for that ([§7](checking-your-labels.md#7-checking-your-labels)).

### Errors

| Message | What it means | How to fix it |
|---|---|---|
| *The level has no QuickSynth Manager.* | Nothing defines or runs a dataset. | Drag a Manager in from *Place Actors → QuickSynth*. |
| *The level has no QuickSynth Capture Camera.* | Nothing can take pictures. | Drag a Capture Camera in from *Place Actors → QuickSynth*. |
| *No Capture Camera is picked* | The Manager uses only what you pick, so nothing would be captured. | Pick the camera in the Manager's *Capture Camera* (the eyedropper lets you click it in the viewport). |
| *Nothing will be labeled* | The Manager's run has no labeled objects: no labeled objects in its volumes (only distractors, or nothing), and no Label components on placed actors. Every label file would be empty. | Drop an Object Library with labeled objects into a Spawn Volume (or add a Spawn Entry with a Label), or add a QuickSynth Label component to a placed actor. If the Manager lists specific *Spawn Volumes*, make sure the right ones are listed. |
| *Its Spawn Volumes use different Object Libraries* | Two libraries would give conflicting class numbers. | Use one Object Library per dataset (put all its objects in it), or leave one volume out of the Manager's *Spawn Volumes*. |
| *Object Library x has several objects named "y"* | Every object needs its own name. | Rename one, or merge them into one object with several *Meshes* (the usual way to have variants of one class). |
| *Object Library x: object N has no Meshes* (or *no Label*) | A library object is incomplete. | Add a mesh (or type the name), or remove the object. |
| *Orbit Target is Actor, but Target Actor is empty.* | The orbiting camera has nothing to circle. | Pick the actor in *Target Actor* (the eyedropper lets you click it in the viewport), or choose another *Orbit Target*. |
| *The camera orbits Spawn Volumes or spawned objects, but … has no Spawn Volume.* | The orbit target doesn't exist. | Add a Spawn Volume, or set *Orbit Target* to *Actor*. |
| *Spawn entry N has no Meshes.* | An entry in *Spawn Entries* is empty. | Add a static mesh to it, or remove the entry. |
| *Spawn entry N has no Label.* | A labeled entry has no class name. | Type the class name in *Label*, or tick *Is Distractor* if it shouldn't be labeled. |
| *Snap To Ground is on, but the bottom of the volume is N cm above the ground* | Objects drop onto the surface below them, but the floor is outside the volume, so nothing can be placed. | Move the volume down until its bottom face touches the floor (or sits slightly below it), or untick *Snap To Ground* to place objects anywhere in the box. |
| *Snap To Ground is on, but there is no ground under the volume* | There's no surface under the volume at all. | Put the volume over a floor or table (with collision), or untick *Snap To Ground*. |

### Warnings

| Message | What it means | What to do |
|---|---|---|
| *No Spawn Volumes are listed* | The Manager spawns nothing, so images show the level as placed (fine if you only label placed actors). | Add your volumes to the Manager's *Spawn Volumes*. |
| *No Manager lists it in Spawn Volumes / Lighting Randomizers* | That actor does nothing in any run. | Add it to a Manager's list, or delete it. |
| *The camera is Fixed and none of its Manager's Spawn Volumes are in view* | The camera is pointing away from where objects spawn, so images will have nothing to label. | Aim the camera at a volume (right-click → *Pilot*), or use *Camera Mode: Orbit*. |
| *Label "x" isn't in Object Library y* | A Spawn Entry or Label component uses a class the library doesn't have. It is still labeled, but numbered after the library's classes, so its number can differ between levels. | Add the object to the library (the Spawn Volume's *Move Spawn Entries to Object Library* button does it in one click), or fix the spelling. |
| *No label format is ticked* | Only images (and masks or depth, if ticked) will be written, no label files. | Tick *YOLO*, *COCO* and/or *QuickSynth JSON* in the Manager's *Output*. |
| *N Managers have Capture On Play on* | When you press Play, only one of them captures. | Untick *Capture On Play* on the others, or use *Generate …* to pick the Manager. |
| *Has no Object Library and no Spawn Entries* (or *every object is unticked*) | A volume that spawns nothing. | Drop in an Object Library or add entries, or delete the volume if you don't need it. |
| *Spawn entry N has a Count Range of 0* | That entry never spawns anything. | Raise *Count Range* (for example 1–3). |
| *… is set to Pick From List but has no materials* | Material randomization has nothing to pick from, so the object keeps its own materials. | Add materials or assign a Material Set, or set *Mode* to *Keep Mesh Materials*. |
| *Has a QuickSynth Label component without a Label* | That placed actor won't be labeled. | Type the class name in the component's *Label*. |
| *Has a QuickSynth Label component but no visible mesh* | The labeled actor has nothing that renders, so it never appears in labels. | Put the component on an actor with a visible mesh. |
| *Sun (or Sky) is on but no Sun Light (Sky Light) is picked* | That part of the Lighting Randomizer has nothing to change. | Pick the light in *Sun Light* / *Sky Light*, or turn off *Sun* / *Sky*. |
| *Sun, Sky and Extra Lights are all off* | The Lighting Randomizer changes nothing. | Turn a part on, or delete the actor. |

