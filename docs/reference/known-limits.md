# 5. Known limits


- Translucent materials (glass, water) can't be labeled yet: they don't write depth, so the ID pass can't see them.
- Truncation uses the object's projected 3D bounds, which are a little larger than its silhouette, so it can slightly
  overestimate how much is cut off.
- Only static meshes can be spawned (skeletal meshes are v1.1).
- HDRIs in the Lighting Randomizer light the scene (through the sky light) but don't replace the visible background;
  the level's own sky stays visible.
- Win64 only.
- In the editor, capture runs at full speed even when the editor isn't the focused window (Manager setting *Full
  Speed In Background*, on by default). Other heavy programs running at the same time still slow capture down.

