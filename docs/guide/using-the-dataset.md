# 8. Using the dataset


Each run creates a folder named `<Dataset Name>_<date>_<time>` with one **self-contained folder per label format**,
each with its own copy of the images, so you can zip or upload just the one you need:

```
yolo/        dataset.yaml, images/, labels/                (Ultralytics YOLO)
coco/        images/, annotations/instances_train.json …   (COCO detection + segmentation)
quicksynth/  images/, metadata/ (one JSON per image)       (everything about each image)
masks/       instance/ and semantic/ (if chosen)
depth/       (if chosen)
previews/    (if chosen)
classes.txt, quicksynth_run.json, quicksynth_report.txt
```

**Train YOLO (Ultralytics)**, no edits needed:

```
yolo detect train data=<run folder>/yolo/dataset.yaml model=yolov8n.pt epochs=100
```

If you move the dataset, update the `path:` line at the top of `dataset.yaml`.

**COCO:** load `coco/annotations/instances_train.json` with `pycocotools`, Detectron2, MMDetection and other
COCO-compatible tools. Segmentation is stored as RLE masks of the visible pixels.

**Masks** are pixel-exact greyscale PNGs. The instance mask is 16-bit (each object has its own number, 0 =
background) and the semantic mask is 8-bit (class number + 1). They **look black in normal image viewers** because
the values are small; read them as numbers, for example in Python:
`cv2.imread(path, cv2.IMREAD_UNCHANGED)`.

**Depth** images (if ticked) are 16-bit PNGs in millimeters by default: read them as numbers like the masks and
divide by 1000 for meters; 0 means no depth (sky). Choose *32-bit EXR (centimeters)* in *Depth Format* for exact
values with unlimited range.

**`classes.txt`** lists class names in class-number order (line 1 = class 0) and applies to every output.

**`quicksynth_run.json`** records the seed and every setting, so the dataset can be regenerated exactly.

Exact formats for every file are in the Feature Reference, section 3.

