# 3. Output


### 3.1 Folder layout

Each selected label format gets a **self-contained folder** with its own copy of the images, so it can be zipped or
uploaded on its own. Each image is encoded once and the same bytes are saved into every format folder. Val folders
exist only when Validation Percent > 0.

```
<DatasetName>_<timestamp>/
  yolo/                         YOLO
    dataset.yaml
    images/train/000000.png …   images/val/…
    labels/train/000000.txt …   labels/val/…
  coco/                         COCO
    images/train/000000.png …   images/val/…
    annotations/instances_train.json   instances_val.json
  quicksynth/                   QuickSynth JSON
    images/train/000000.png …   images/val/…
    metadata/train/000000.json …   metadata/val/…
  images/train/…                only when no label format is selected
  masks/instance/<split>/000000.png    Instance Mask
  masks/semantic/<split>/000000.png    Semantic Mask
  depth/<split>/000000.png             Depth (.exr with Depth Format EXR)
  previews/<split>/000000.jpg          Label Previews
  classes.txt
  quicksynth_run.json
  quicksynth_report.txt
```

File names are the zero-padded image index (`000000`, `000001`, …) and are the same across every folder, so
`masks/instance/train/000042.png` belongs to `yolo/images/train/000042.png`.

### 3.2 Classes and IDs

- **Class ID** is 0-based: the class's position among the Object Library's labeled objects (labels outside the library
  follow, sorted), or in the alphabetical list of labels in use when there is no library.
- `classes.txt` lists class names, one per line, in class ID order (line 1 = class 0). It sits at the run root
  because it applies to every output: YOLO class numbers, COCO `category_id` − 1, semantic mask values − 1 and the
  JSON `class_id`. Many tools (e.g. labelImg, Darknet-style YOLO, CVAT import) read a plain class list like this; the
  same names are also inside `yolo/dataset.yaml` and the COCO `categories`.
- COCO `category_id` is class ID + 1 (COCO reserves 0).
- Semantic mask pixel value is class ID + 1 (0 = background).

### 3.3 RGB images

8-bit sRGB PNG (or JPG), opaque, at the chosen resolution. Rendered with the scene's normal lighting and post
processing after the warm-up frames, using the project's Global Illumination and Reflection methods (Lumen included;
Unreal would otherwise turn Lumen off for captures) and encoded for a normal display like the viewport, so images
look like what you see through the camera. With auto exposure, brightness can differ slightly from the viewport right
after a camera move, because the viewport adapts gradually; *Locked Exposure EV100* removes that difference.

### 3.4 Instance mask

- **16-bit single-channel (greyscale) PNG**, same size as the image.
- Pixel value = the object's `instance_id`, numbered **1, 2, 3, …** per image in a stable order. **0 = background**.
  Up to 65,535 objects per image.
- Covers **every visible labeled-class object**, including objects the visibility filter left out of YOLO/COCO.
  Distractors and unlabeled scenery are background.
- Pixel-exact: no anti-aliasing, blending or edge colors. Each pixel belongs to exactly one object or to background.
- Because the values are small (1, 2, 3 out of 65,535), the image **looks black in normal viewers**. That is
  expected. Read it as integers, e.g. `cv2.imread(path, cv2.IMREAD_UNCHANGED)` → `uint16` array.
- The matching `instance_id`, name, class and flags for each value are in the QuickSynth JSON for that image.

### 3.5 Semantic mask

- **8-bit single-channel (greyscale) PNG**, same size as the image.
- Pixel value = **class ID + 1**; **0 = background**. Up to 255 classes.
- Covers exactly the same pixels as the instance mask.
- Also looks black in viewers (values 1, 2, 3 …).

### 3.6 Depth

Same size as the image, for every pixel including unlabeled scenery. Value = planar depth: distance along the
camera's forward axis (not along the ray). Two formats (*Depth Format* on the Manager):

- **16-bit PNG, millimeters** (default): single-channel `uint16`, 1 mm steps, up to 65.535 m. **0 = no depth** (sky,
  or farther than 65.535 m). The usual depth-camera convention; read with
  `cv2.imread(path, cv2.IMREAD_UNCHANGED)` and divide by 1000 for meters. Looks almost black in normal viewers.
- **32-bit float EXR, centimeters** (Unreal units): exact, unlimited range; the sky is a very large value.

`quicksynth_run.json` records which one: `depth_format` = `png_uint16_millimeters` or `exr_float32_centimeters`.

### 3.7 YOLO

Ultralytics detection format; loads and trains with no edits (`yolo detect train data=<run>/yolo/dataset.yaml`).

- One `.txt` per image, including **empty files** for images with no labels (Ultralytics treats them as background images).
- One line per object: `class_id cx cy w h`, normalized to 0–1, 6 decimals.
- Only objects that pass the visibility filter **and** are not flagged occluded/truncated (YOLO has no flags).
- `dataset.yaml`: absolute `path` to the `yolo/` folder (edit it if you move the dataset), `train`, `val` (points at
  the train images when there's no validation split, because Ultralytics requires `val`), and `names`.

### 3.8 COCO

Detection + instance segmentation; loads in `pycocotools` with no errors.

- `instances_train.json` and (with a validation split) `instances_val.json`.
- `images`: `id` = image index, `file_name` (relative to `coco/images/<split>/`), `width`, `height`.
- `annotations`: `bbox` `[x, y, w, h]` in pixels (tight around the visible pixels), `area` = visible pixel count,
  `iscrowd` 0, `category_id`, `segmentation` as **compressed RLE** of the visible pixels, and `attributes`
  `{occluded, truncated, visible_fraction}`.
- `categories`: every class with `id` = class ID + 1.

### 3.9 QuickSynth JSON

One file per image with everything we know about it:

- `image` (path relative to `quicksynth/`), `image_index`, `split`, `width`, `height`, `seed`, `frame_index`.
- `camera`: `location`, `rotation_pyr`, `horizontal_fov_degrees`, `intrinsics` `{fx, fy, cx, cy}` in pixels.
- `coordinate_system`: Unreal, centimeters, X forward, Y right, Z up, degrees.
- `objects`: every visible labeled-class object with `instance_id` (its value in the instance mask),
  `instance_name` (readable, numbered per class within the image: `cube_001`, `cube_002`, …), `class_id`,
  `class_name`, `labeled` (passed the filter), `bbox_xywh`, `visible_pixels`, `silhouette_pixels`,
  `visible_fraction`, `truncation`, `occluded`, `truncated`, `transform` (world location/rotation/scale),
  `world_bounds_min`/`max`, `source` (the volume/entry or actor it came from), and for spawned objects
  `materials`: per slot, the `material` name and, when QuickSynth randomized it, `base_color_linear`, `roughness`,
  `metallic` and (with a texture) `texture`, `tiling`, `texture_rotation`.

### 3.10 Run file and report

- `quicksynth_run.json`: plugin and engine version, dataset name, seed, start time, duration, completed or stop
  reason, image counts per split, resolution, formats, classes with label counts, stats, and a snapshot of every
  QuickSynth setting on the camera, volumes and Lighting Randomizers. Enough to reproduce the dataset.
- `quicksynth_report.txt`: human-readable summary: labels per class (labeled / filtered out), images with no labels,
  skipped viewpoints, consistency checks (every box inside its image and matching its mask), and warnings
  (classes with no labels, mostly empty images, failed file writes).

### 3.11 Label previews

With **Write Label Previews** ticked on the Manager, each image is also saved as `previews/<split>/000000.jpg` with
its boxes drawn on it: labeled objects in their class color (the Object Library's color, or the same automatic colors
as the overlay), filtered-out objects in grey. Boxes are drawn on their own outermost pixels. Previews come from the same
labels as every other output and don't need any other output to be on. They are for checking labels by eye, not for
training (they are JPG and have boxes burned in).

