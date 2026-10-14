# 11. Troubleshooting


| Problem | Fix |
|---|---|
| Generate Dataset does nothing and the Message Log opens | Validate Scene found errors. Fix them ([§5](validate-scene-every-check-explained.md#5-validate-scene-every-check-explained)). |
| Images have no objects | Run Validate Scene. Common causes: the camera points away from the volume, or the volume floats above the floor with Snap To Ground on. |
| Objects aren't labeled | They may be filtered out as too small, hidden or cut off (grey in the overlay). Lower the Manager's *Visibility Filter* thresholds, or move the camera closer. |
| Masks look completely black | That's expected; the values are small numbers. Read them as integers ([§8](using-the-dataset.md#8-using-the-dataset)). |
| Ghosting or blurry objects | Raise *Warm Up Frames*. |
| Lighting doesn't change | Run Validate Scene: the level may have no Directional Light or Sky Light. QuickSynth makes Static lights movable for the play session automatically; if your project uses baked lighting, baked shadows won't move. |
| Capture is slow when the editor is in the background | Make sure *Full Speed In Background* is ticked on the Manager. |
| Some images were skipped | Orbit couldn't find a clear viewpoint. Widen the distance or elevation ranges, or move obstacles. Skips are listed in the report. |
| "Waiting for N shader(s) to compile" in the log | Normal on the first run with new materials. |
| `dataset.yaml` points to the wrong place | You moved the dataset. Update its `path:` line. |

