# 6. Setting up a scene


### 6.1 Manager

One Manager is one dataset. It decides:

- **Capture:** which *Capture Camera* takes the pictures (required), *Image Count*, *Resolution*,
  *Capture On Play*, *Dry Run*, and what you see while it runs (*View Through Camera During Capture*, *Show Label
  Overlay*).
- **Randomization:** which *Spawn Volumes* and *Lighting Randomizers* take part, and the *Seed*. **Only what you list
  is used:** with no Lighting Randomizers listed, the lighting stays exactly as placed.
- **Labels:** the *Visibility Filter* (which partly hidden or cut-off objects still get a label).
- **Output:** *Dataset Name*, *Output Directory*, one checkbox per output (*YOLO*, *COCO*, *QuickSynth JSON*,
  *Instance Masks*, *Semantic Masks*, *Depth*, *Label Previews*), image format and the validation share. See
  [§8](using-the-dataset.md#8-using-the-dataset) for what each output contains.

**Several datasets in one level.** Place one Manager per dataset, each with its own camera, volumes and outputs.
Use *Generate …* to pick which one runs. Want two views of the same scene? Use two Managers with two cameras.

### 6.2 Capture Camera

**Fixed or Orbit?**

- **Fixed** shoots every image from where you placed the camera. Good for a fixed security or factory camera, or a
  robot's mounted camera.
- **Orbit** moves the camera around a target for every image, always looking at it. The target can be the spawned
  objects (the default: their center, so the camera follows them), a Spawn Volume, or any actor. Set the
  **Distance**, **Elevation** (height angle) and **Azimuth** (angle around) ranges.

**Orbit Target: Spawned Objects, Spawn Volume or Actor?**

- **Spawned Objects** (default) aims at the center of the labeled objects spawned for this image, recomputed every
  image. Wherever the objects land, they stay in frame. Best for most datasets.
- **Spawn Volume** aims at the fixed center of a volume (*Target Volume*; empty = the first one). The framing stays
  steady from image to image while objects move around inside it, so objects that land near the volume's edges can be
  partly or fully out of frame. Use it when you want consistent framing of an area, like a shelf or a bin.
- **Actor** aims at any actor you pick, for example a fixed product on a table.

**Orbit Motion: Random or Sweep?**

- **Random** (default) picks a new random viewpoint within the ranges for every image. This gives the most varied
  data and is the best choice for training.
- **Sweep** moves around the target in even steps like a turntable: *Images Per Revolution* sets the step (36 = every
  10°) and *Clockwise* the direction. Over the run, elevation and distance move from the low end of their ranges to
  the high end, so the camera spirals; set *Elevation Range* to the same value at both ends for a flat circle. Use it
  for full, even coverage of an object or for image sequences.

With *Reject Blocked Viewpoints* (Advanced, on by default), viewpoints inside walls or with the target hidden behind
something are skipped automatically.

**Field of view.** Tick *Field Of View Range* to vary the zoom per image.

**The look of the images.** Images use your level's lights and Post Process Volumes, exactly as you set them up. To
give one camera its own look (a stylized grade, film grain, a toon post-process material), tick **Use Camera Post
Process** on the camera: a **Post Process** section appears right below it with Unreal's usual post-process
settings. Tick the ones you want, as in a Post Process Volume. Labels, masks and depth are never affected by any of
this.

### 6.3 Object Library

The Object Library is the list of everything that goes into your dataset, and you can reuse it in any level. Create
one with Content Browser → right-click → QuickSynth → **Object Library**. For each object:

- **Label:** its class name (for example `apple`). Every object needs its own name.
- **Is Distractor:** spawned but never labeled.
- **Color:** its box color in the label overlay and previews.
- **Meshes:** one or more. With several (five different apples, a bunch and a single banana), each spawned copy is
  one of them at random, all labeled as the same class.
- **Count Range:** how many to place by default (each Spawn Volume can change it).
- **Scale Range**, **Rotation** (yaw by default; tick pitch or roll for objects that can tumble or lie on their side),
  **Materials** ([§6.5](#65-materials)).
- **Visibility Filter** (optional): its own rules for when it's labeled, instead of the Manager's.

**Class numbers follow the list.** The first labeled object is class 0, the next class 1 (distractors don't get a
number). Models learn these numbers, so **add new objects at the end**; moving objects around renumbers them.

### 6.4 Spawn Volume

The volume spawns and shuffles **its own objects only**; it never moves or changes anything already in your level.

- **Object Library:** drop one in and the volume spawns all of its objects. A row per object then lets this level
  differ: untick **Spawn** to leave an object out, or tick **Count Range** to place a different number here. Rows
  follow the library automatically when you add or remove objects.
- **Spawn Entries:** objects set up right in the volume, with the same settings as library objects. Handy for scenery
  that isn't part of the dataset (a background plane, a tablecloth) or a quick test. To reuse them elsewhere, click
  **Move Spawn Entries to Object Library**: they move into the volume's library, or into a new one you save. The
  volume spawns exactly as before, and Ctrl+Z undoes it.
- **Snap To Ground** (on) drops each object onto the surface below it. The surface must be inside the volume or flush
  with its bottom face. Turn it off for objects floating anywhere in the box (drones, falling items).
- **Align To Ground Normal** tilts objects to match sloped ground.
- **Avoid Overlaps** (on) keeps objects from intersecting; *Min Spacing* adds a gap.
- **Rerandomize Every N Captures** changes how often objects move. Each object's *Lifetime* can override it, for
  example background clutter that changes every 10 images while targets change every image.

Objects are placed by the center of their bounds, so meshes with an off-center pivot still land in the volume.

### 6.5 Materials

Each object has its own **Materials** setting:

- **Keep Mesh Materials** (default): objects look as authored.
- **Pick From List:** each object gets a random material from your list (or from a *Material Set* asset).
- **Randomize Color & Surface:** each object gets a random color, roughness and metallic value, and optionally a
  random texture with random tiling and rotation.

Material changes never move objects. To reuse a list of materials, create a **Material Set** (Content Browser →
right-click → QuickSynth → Material Set).

### 6.6 Labeling objects already in the level

To label something you placed yourself (a parked car, a machine), select it and click **Add → QuickSynth Label** in
the Details panel, then type its class name. Use the same name as in your Object Library so it gets that class's
number. The whole actor is labeled as one object.

### 6.7 Lighting Randomizer

Drag in a **QuickSynth Lighting Randomizer**, pick your Directional Light in *Sun › Sun Light* and your Sky Light in
*Sky › Sky Light*, and add the Lighting Randomizer to the Manager's *Lighting Randomizers*. It changes, for every
image:

- **Sun:** height, direction, brightness and color temperature,
- **Sky:** sky light brightness, and optionally an HDRI from your list (lights the scene; the visible sky stays the
  level's own),
- **Extra Lights** (off by default): point or spot lights at random spots inside the actor's box (scale the actor to
  resize it).

Your lights are put back exactly as placed when play stops. Lighting never changes where objects are or what's
labeled.

