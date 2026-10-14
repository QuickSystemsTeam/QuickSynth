# 3. How QuickSynth works


**Four pieces.**

| Piece | What it's for |
|---|---|
| **Object Library** (asset) | *What* the dataset is about: each object's label, meshes, materials and default count. Reusable in any level. |
| **Spawn Volume** (actor) | *Where* objects appear in this level, and how many of each. |
| **Capture Camera** (actor) | *Where the pictures are taken from* and what they look at. |
| **Manager** (actor) | *The dataset itself*: which camera and volumes, how many images, what is written, and starting the run. |

**Capture runs while the level is playing.** QuickSynth never changes the level you're editing: it spawns objects,
moves the camera and changes lights only in the play session, and everything goes back when play stops.

**Every image goes through the same loop:**

1. **Randomize:** Spawn Volumes place their objects (count, position, rotation, scale, materials), Lighting
   Randomizers change the lights, and the camera picks its viewpoint.
2. **Render:** a few *warm-up frames* are rendered so Unreal's lighting, anti-aliasing and exposure settle, then the
   image is captured.
3. **Label:** QuickSynth renders a separate, clean *ID pass* in which every labeled object has its own ID. Boxes and
   masks are measured from those pixels, so a box always fits exactly the part of the object you can see.
4. **Write:** the image and its labels are saved in every format you chose.

**Classes.** Each object's *Label* is a class, and training formats store each class as a number. With an Object
Library, the numbers follow its list: the first labeled object is class 0, the next class 1, and so on, in every level
that uses the library. That's what lets you combine datasets from several runs or levels. Without a library, the
classes are every label in use, sorted alphabetically.

**Distractors.** An object with *Is Distractor* ticked is spawned like any other object but never labeled.
Distractors teach a model that not everything it sees is a target.

**Visibility filter.** An object that is barely visible (a few pixels, mostly hidden, or mostly off the edge of the
image) can be left out of the labels. The Manager's *Visibility Filter* decides this; an Object Library can override it
per object.

**Seed.** All randomness comes from the Manager's *Seed*. The same seed, scene and settings give the same labels
every time, so a dataset can always be regenerated. Seed 0 picks a new seed for every run (it's recorded in the run
file).

**Train/validation split.** *Validation Percent* (20% by default) puts that share of images into a `val` split,
chosen from the seed. Set it to 0 for training images only.

