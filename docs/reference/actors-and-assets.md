# 2. Actors and assets


The QuickSynth category is always shown first in the Details panel (right below Transform). Settings are listed
in the order you set them up; technical settings are in the **Advanced** group at the bottom of the QuickSynth
section.

### 2.1 Manager

Defines one dataset and runs it. Place one per dataset. Each Manager uses one camera; for two views of the same
scene, add two Managers.

**Capture**

| Setting | Default | What it does |
|---|---|---|
| Capture Camera | empty | The camera that captures this dataset. Required: with none picked, nothing is captured (an on-screen message says so, and Validate reports an error). |
| Image Count | 100 | Images to capture. |
| Resolution | 1280×720 | Image size in pixels (16–8192). Every image in a dataset has the same size. |
| Capture On Play | on | Start capturing when Play is pressed. Off: Play only runs the level; start with QuickSynth → Start Capture, or use Generate Dataset. |
| Dry Run | off | Run the whole loop with the label overlay but write no files (§1.3). |
| Dry Run Seconds Per Image | 1 s | How long each Dry Run image stays on screen (shown only with Dry Run on). |
| View Through Camera During Capture | on | Show the capture camera's view in the viewport. Off keeps your normal Play view. Images are the same either way. |
| Show Label Overlay | on | Draw the last captured image with its labels over the viewport during play (§1.3). Never part of saved images. |

**Randomization**

| Setting | Default | What it does |
|---|---|---|
| Spawn Volumes | empty | Volumes that fill this dataset. Only listed volumes spawn anything (empty = none, with a warning). The picker lists only Spawn Volumes. |
| Lighting Randomizers | empty | Lighting Randomizers that change the lighting. Only listed ones do anything (empty = the lighting stays as placed). |
| Seed | 1 | Seed for all randomization. 0 = a new random seed each run (logged and recorded in `quicksynth_run.json`). |

**Labels**

| Setting | Default | What it does |
|---|---|---|
| Visibility Filter | see below | Which visible objects get a label. Objects in an Object Library can override it. |

Visibility Filter:

| Field | Default | Meaning |
|---|---|---|
| Min Visible Pixels | 100 | Fewer visible pixels → never labeled. |
| Min Visible Fraction | 0.2 | Visible pixels ÷ the object's full silhouette in the image. Below this → *occluded*. |
| Max Truncation | 0.75 | Share of the object's projected 3D bounds outside the image. Above this → *truncated*. |
| Action | Drop | *Drop* occluded/truncated objects, or *Keep With Flags* (flags written to COCO and QuickSynth JSON; YOLO has no flags, so they are still left out of YOLO). |

**Output**

| Setting | Default | What it does |
|---|---|---|
| Dataset Name | Dataset | Used in the run folder name. |
| Output Directory | empty | Empty = `<Project>/Saved/QuickSynth`. Each run creates `<Dataset Name>_<yyyyMMdd_HHmmss>` (plus `_2`, `_3`… if that exists). |
| YOLO / COCO / QuickSynth JSON | on / off / off | Label formats, one checkbox each. Each ticked format gets its own folder (see §3). |
| Write Instance Masks / Semantic Masks / Depth / Label Previews | off | Extra outputs, one checkbox each (§3.4–3.6, §3.11). Depth and the masks come from separate clean passes, so post-processing never changes them. |
| Depth Format | 16-bit PNG (millimeters) | Shown when Write Depth is on. *16-bit PNG (millimeters)* or *32-bit EXR (centimeters)* (§3.6). |
| Image Format | PNG | PNG (lossless) or JPG (with JPG Quality, default 90). |
| Validation Percent | 20% | Share of images in the validation split, chosen from the seed. 0 = no validation split and no val folders. |

**Advanced**

| Setting | Default | What it does |
|---|---|---|
| Hide Player Pawns | on | Hides player pawns during capture so they don't appear in images. |
| Full Speed In Background | on | Editor only. Keeps capture at full speed when the editor isn't the focused window by turning off *Use Less CPU when in Background* for the run (restored afterwards, never saved). Off = keep your editor preference: more CPU for other programs, but capture slows down whenever the editor is in the background. |
| Overlay Toggle Key / Overlay View Key | L / M | Keys that show/hide the label overlay and switch its view. |

### 2.2 Capture Camera

Where the images are taken from and what they look at. Picked by a Manager.

| Setting | Default | What it does |
|---|---|---|
| Camera Mode | Fixed | *Fixed*: every image from the placed transform. *Orbit*: a new viewpoint around a target for each image, always looking at it. |
| Orbit Target | Spawned Objects | *Spawned Objects*: center of the labeled spawned objects, recomputed for every image so the camera follows them wherever they land. *Spawn Volume*: the volume's fixed center (steady framing; objects near the volume's edges can leave the frame). *Actor*: any actor in the level. |
| Target Volume / Target Actor | – | Shown for the matching Orbit Target. Empty volume = the first volume in the run. |
| Orbit Motion | Random | *Random*: a new random viewpoint within the ranges for every image (best variety for training). *Sweep*: steps evenly around the target like a turntable (below). |
| Images Per Revolution | 36 | Sweep: images per trip around the target (36 = every 10°), or per pass across a partial Azimuth Range. |
| Clockwise | on | Sweep: direction as seen from above (Top viewport). |
| Distance Range | 300–800 cm | Distance from the target. Sweep moves from Min to Max over the run. |
| Elevation Range | 15–60° | Angle above the horizon (0 = level, 90 = straight down). Sweep moves from Min to Max over the run (a spiral); equal values give a flat circle. |
| Azimuth Range | 0–360° | Angle around the target. Sweep starts at Min (counter-clockwise: Max); a partial range is swept back and forth. |
| Look At Jitter | 0 cm | Random offset of the look-at point so targets aren't always centered. |
| Roll Range | 0–0° | Camera roll. |
| Field Of View Range | off, 60–90° | Tick to randomize the horizontal FOV per image; off = the Camera component's FOV. |
| Use Camera Post Process | off | Give this camera's images their own look, on top of the level's Post Process Volumes. Off = the level's Post Process Volumes only. Labels, masks and depth are never post-processed. |
| Post Process | none ticked | Shown when Use Camera Post Process is on. Unreal's full post-process settings (color grading, film grain, bloom, post-process materials, …); tick a setting to use it. A GI or Reflection method set here replaces the project's for these images. Also applied to the viewport while viewing through the camera during capture. |

**Advanced**

| Setting | Default | What it does |
|---|---|---|
| Warm Up Frames | 12 | Frames rendered after each randomization before saving, so TAA/TSR, Lumen and auto exposure settle. Too few causes ghosting. The first image of a run gets 4× as many, because its lighting caches, shadow maps and anti-aliasing history start empty. |
| Locked Exposure EV100 | off, 10 | Tick to use a fixed exposure for consistent brightness. |
| Draw Small Object Shadows | on | Unreal skips the shadows of objects that look small on screen to save time (how small depends on the project's shadow quality; at the editor's High preset, fruit-sized objects lose theirs). On: that culling is off during the run (`r.Shadow.RadiusThreshold` 0, `r.Shadow.Virtual.NonNanite.UseRadiusThreshold` 0) and restored afterwards, so every object casts its shadow. |
| Reject Blocked Viewpoints | on | Orbit viewpoints inside geometry or with the target blocked are rejected. |
| Max Viewpoint Attempts | 30 | Viewpoints tried before an image is skipped (skips are counted in the report). |

### 2.3 Spawn Volume

A box you scale in the viewport. It only spawns and moves **its own objects**; it never touches actors already
placed in the level. Objects are pooled (created once, then shown/hidden and moved), so long runs don't allocate.

| Setting | Default | What it does |
|---|---|---|
| Object Library | none | The dataset's objects (§2.4). The volume spawns every object in it. |
| Library Objects | one row per library object | Per level: **Spawn** (untick to leave an object out of this volume), **Count Range** (tick to override the library's count) and **Lifetime**. Rows follow the library automatically; objects added to the library later spawn with the library's settings. |
| Spawn Entries | empty | Objects set up right here (below), spawned together with the library's, e.g. a background plane or a quick test. |
| Move Spawn Entries to Object Library (button, bottom of the QuickSynth section) | – | Moves the Spawn Entries into the volume's Object Library, or into a new one (save dialog; defaults to the level's folder, or `/Game` for maps outside the project's content such as the demo), so other levels can reuse them. Entries whose label the library already has add their meshes to that object. The volume spawns the same as before. Undoable. |
| Snap To Ground | on | Drops each object onto the surface below it (the surface must be inside the volume or flush with its bottom). Off = anywhere in the box, including mid-air. |
| Align To Ground Normal | off | Tilt snapped objects to the surface slope. |
| Avoid Overlaps | on | Objects that can't find a free spot are skipped. |
| Min Spacing | 0 cm | Minimum gap between objects' bounding boxes. |
| Rerandomize Every N Captures | 1 | How often the volume's objects move. |
| Max Placement Attempts (Advanced) | 30 | Spots tried per object before it is skipped. |

Spawn entry:

| Field | Default | What it does |
|---|---|---|
| Label | object | Class name written to the dataset. |
| Is Distractor | off | Spawned but never labeled (clutter). |
| Meshes | empty | Static meshes; with several, each object is one of them at random (variants of the same class). |
| Count Range | 1–3 | How many to place each randomization. |
| Scale Range | 1–1 | Uniform scale. |
| Rotation | yaw 0–360 | Per-axis toggles and ranges (pitch/roll off by default so objects stay upright). |
| Materials | Keep | Material randomization (below). |
| Lifetime (captures) | 0 | How many images these objects stay put before moving. 0 = the volume's setting. |

Objects are placed by the **center of their bounds**, not their pivot, so meshes with an off-center pivot (common
in downloaded assets) still land inside the volume. With Snap To Ground the bottom of the bounds rests on the
surface.

If Snap To Ground is on and there is nothing to land on, the volume reports it on screen and in the log.

**Materials** (per object). Materials change whenever the object's copies are re-randomized, and use a random
stream of their own, so turning them on never changes where objects are placed.

| Field | Default | What it does |
|---|---|---|
| Mode | Keep Mesh Materials | *Keep Mesh Materials*, *Pick From List* (each object gets a random material from the list), or *Randomize Color & Surface* (each object gets QuickSynth's material with random values). |
| Materials | empty | *Pick From List*: the materials to choose from. |
| Material Set | none | Optional Material Set asset; adds its materials (Pick From List) or textures (Randomize Color & Surface). |
| Hue / Saturation / Brightness Range | 0–360°, 0–1, 0.2–1 | Random color, as an sRGB color picker shows it. |
| Roughness Range | 0.2–0.9 | 0 = mirror-like, 1 = matte. |
| Metallic Range | 0–0 | 0 = plastic/paint/wood, 1 = bare metal. Metal mostly shows reflections, so it looks almost black unless the level has a Sky Light or Reflection Capture. |
| Textures | empty | Optional; each object gets one, tinted by its color. Saturation 0–0 and Brightness 1–1 = untinted. |
| Tiling Range / Texture Rotation Range | 1–1, 0–0° | Texture repeats and rotation. |
| Slots (Advanced) | empty | Material slots to change (0 = first). Empty = all. |
| Custom Material (Advanced) | none | Randomize your own material instead. QuickSynth sets these parameters if it has them: `BaseColor` (vector), `Roughness`, `Metallic`, `Tiling`, `TextureRotation` (scalars), `BaseTexture` (texture). |

QuickSynth's material is `/QuickSynth/Materials/M_QuickSynth_Randomizable` (opaque, lit; base color = texture ×
color). Problems such as Pick From List with an empty list are reported on screen and the object keeps its materials.

### 2.4 Object Library asset

Content Browser → right-click → QuickSynth → **Object Library**. Everything that goes into a dataset, reusable across
levels: drop it into a Spawn Volume. Each object has the spawn entry's fields (Label, Is Distractor, Meshes, Count
Range as the default count, Scale Range, Rotation, Materials) plus:

| Field | Default | What it does |
|---|---|---|
| Color | blue | Box color in the label overlay and label previews (not used in mask files). |
| Visibility Filter | off | Tick to give this class its own visibility rules instead of the Manager's. |

**Class numbers.** Labeled objects are numbered in list order (distractors are skipped): the first is class 0.
Models learn these numbers, so add new objects at the end; reordering renumbers the classes. Every object needs its
own Label. Labels used in the level that the library doesn't contain (a volume's own Spawn Entries, Label components)
get the next numbers, sorted alphabetically, with a warning. A Manager whose volumes use different libraries is a
Validate error.

### 2.5 QuickSynth Label component

Add to any placed actor (Details → Add → QuickSynth Label) and set **Label**. All of the actor's visible primitive
components are labeled as one object.

### 2.6 Material Set asset

Content Browser → right-click → QuickSynth → **Material Set**. A reusable list of **Materials** and **Textures** for
material randomization, assigned in an object's Materials.

### 2.7 Lighting Randomizer

Drag *QuickSynth Lighting Randomizer* into the level. It changes the lighting for every capture (or every N): the
sun, the sky light and optional extra lights. It works with the level's own lights: pick the Directional Light in
*Sun Light* and the Sky Light in *Sky Light* (nothing is picked automatically; with Sun or Sky on and no light picked,
that part does nothing and a warning says so). Changes only exist during the play session; when the run ends the lights
are put back. Static or Stationary lights are made Movable for the play session (the log says so), because
Unreal can't move them otherwise. Only Lighting Randomizers listed in the Manager's *Lighting Randomizers* take part,
and lighting uses its own random stream, so it never changes object placement or labels.

| Setting | Default | What it does |
|---|---|---|
| **Sun** › Enabled | on | Randomize the sun. |
| Sun › Sun Light | empty | The Directional Light to randomize (the picker lists only Directional Lights). Required for the sun to change. |
| Sun › Elevation Range | 15–75° | Height above the horizon (90 = overhead). |
| Sun › Azimuth Range | 0–360° | Compass direction the light comes from. |
| Sun › Intensity Scale Range | 0.5–1.5 | Multiplies the light's own Intensity. |
| Sun › Temperature Range | on, 4500–7500 K | Color temperature; untick to keep the light's color. |
| **Sky** › Enabled | on | Randomize the sky light. |
| Sky › Sky Light | empty | The Sky Light to randomize (the picker lists only Sky Lights). Required for the sky light to change. |
| Sky › Intensity Scale Range | 0.7–1.3 | Multiplies the sky light's own Intensity. |
| Sky › HDRIs | empty | Cube textures; each capture lights the scene with one (through the sky light, which is switched to *Specified Cubemap* and, if it was real-time, to captured for the run). Empty = the level's own sky. |
| Sky › HDRI Rotation Range | 0–360° | Rotation of the HDRI. |
| **Extra Lights** › Enabled | off | Add lights at random spots inside the actor's box (scale the actor to resize it; default 6 × 6 × 3 m). |
| Extra Lights › Count Range | 1–3 | Lights per capture (at most 16). |
| Extra Lights › Light Type | Point | Point, Spot (aimed at the box center) or Mixed. |
| Extra Lights › Intensity Range | 10–100 cd | Brightness in candela. |
| Extra Lights › Attenuation Radius Range | 300–1000 cm | How far each light reaches. |
| Extra Lights › Temperature Range | 2700–6500 K | Color temperature. |
| Extra Lights › Hue / Saturation Range | 0–360°, 0–0 | Optional colored tint; saturation 0 = white. |
| Extra Lights › Cone Angle Range | 25–45° | Spot lights only. |
| Extra Lights › Cast Shadows | on | |
| Rerandomize Every N Captures | 1 | How often the lighting changes. |

With auto exposure (the default), the camera adapts to brighter or darker lighting like a real camera. Turn on
*Lock Exposure* on the Capture Camera to keep brightness differences in the images.

### 2.8 Demo map

`/QuickSynth/Maps/LV_Demo_Tabletop` (Content Browser → Plugins → QuickSynth Content → Maps; enable
*Show Plugin Content* in the Content Browser settings). A wooden table with four fruit classes (apple, banana,
lemon, lime) and onions and sweet potatoes as distractors, all in the Object Library `DA_ObjectLibrary_Fruit` (bananas
are one class with two meshes: a bunch and a single banana), and a cloth with a random surface (brick, clay tiles,
corrugated iron, velvet) from the Material Set `DA_MaterialSet_Surfaces`, set up as a Spawn Entry in its own volume. Lighting is Unreal's Sun & Sky setup (Directional Light, Sky
Atmosphere, real-time Sky Light), and a Lighting Randomizer changes the sun's direction, brightness and color every
image; the sky and sky light follow it automatically.

- **QuickSynth Manager:** 200 images, YOLO + COCO, instance and semantic masks and label previews; lists the
  Lighting Randomizer explicitly so it's clear the lighting changes. Press Play or use
  Generate Dataset.
- **Capture Camera:** random orbits around the fruit at 55–160 cm, random field of view.

All demo assets are CC0 (credits in `DemoAssetCredits.md`).

**Plugin content layout.** The plugin's content folders are named by asset type, as Fab requires: `Maps`, `Data`
(Object Library and Material Set), `Materials`, `Meshes`, `Textures`. Everything is demo content except the two
materials QuickSynth itself uses, `Materials/M_QuickSynth_InstanceID` and `Materials/M_QuickSynth_Randomizable`.
Names use Unreal's prefixes: `LV_` level, `DA_` data asset, `M_` material, `MI_` material instance, `SM_` static
mesh, `T_` texture.

