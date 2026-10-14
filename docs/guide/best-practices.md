# 10. Best practices


**Make the data varied (domain randomization).** Models trained on synthetic images work best on real photos when
the synthetic images vary a lot: random viewpoints, lighting, colors, textures and backgrounds. Use Orbit with
Random motion, a Lighting Randomizer, material randomization, and distractors.

**Use distractors.** Add unlabeled clutter so the model learns to tell your targets apart from other objects.

**Keep classes stable.** Put your objects in an Object Library once you have more than one run or level, and add
new objects at the end.

**Check before long runs.** Validate Scene → Dry Run (or a 20-image run with Label Previews) → full run.

**Speed.**

- Capture speed depends mostly on resolution, *Warm Up Frames* and your scene's rendering cost. Fewer warm-up frames
  are faster, but too few cause ghosting and wrong lighting; keep the default (12) unless your scene settles faster.
- In the editor, capture keeps full speed when you switch to another program (Manager setting *Full Speed In
  Background*, on by default). Turn it off if you'd rather leave CPU for other programs; capture then slows down
  whenever the editor isn't in front. Other heavy programs running at the same time slow capture either way.
- The first run after adding new materials can pause briefly while their shaders compile. QuickSynth waits for them,
  so no image is ever saved with Unreal's grey placeholder material.

**Brightness.** Auto exposure adapts to brighter and darker lighting like a real camera. Tick *Locked Exposure EV100*
(Advanced) to keep brightness differences in the images instead.

**Metal looks black?** Metallic surfaces mostly show reflections. Without a Sky Light or Reflection Capture in the
level they look nearly black. Add one, or keep *Metallic Range* low.

**Disk space.** Each label format folder holds its own copy of the images. Pick only the formats you need for big
runs, or use JPG.

**Reproducibility.** Keep the seed fixed (default 1) while you tune a scene; change it to get a fresh dataset from the
same setup.

