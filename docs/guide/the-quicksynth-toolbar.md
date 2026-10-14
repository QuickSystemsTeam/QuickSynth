# 4. The QuickSynth toolbar


The **QuickSynth** button sits on the level editor toolbar. Click it for this menu:

| Entry | What it does |
|---|---|
| **Generate Dataset** | Validates the scene, plays the level, captures the dataset, and stops playing when it's done. Works whether or not the Manager's *Capture On Play* is ticked. With several Managers, there's one *Generate …* entry per Manager, and only that one runs. |
| **Start Capture** | Only while the level is playing. Starts capturing in the running level, for when *Capture On Play* is off and you want to look around first. Play keeps running after the run. |
| **Stop Capture** | Only while capturing. Ends the run early. Everything captured so far is kept, with its labels and report. |
| **Validate Scene** | Checks your setup and lists problems in the Message Log ([§5](validate-scene-every-check-explained.md#5-validate-scene-every-check-explained)). |
| **Open Output Folder** | Opens the last run's folder, or the folder runs are written to. |

**Other ways to start.** Pressing the normal **Play** button also captures when the Manager's *Capture On Play* is
ticked (the default). Pressing **Esc** ends play and the run; captured files are kept.

**While a run is going**, a notification in the bottom-right corner shows images done, images per second and time
left, with a **Stop** button. When the run ends, a notification says how it went, with a link to the output folder.

