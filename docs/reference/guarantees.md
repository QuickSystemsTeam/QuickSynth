# 4. Guarantees


- **Tight boxes:** boxes are measured from each object's visible pixels in a clean ID pass, not from 3D bounds.
- **Exact visibility:** "visible fraction" compares visible pixels with the object's full silhouette, rendered
  separately, so it is measured rather than estimated.
- **Reproducible:** the same seed, scene and settings produce identical labels. Images can differ by a few pixel
  values because of GPU nondeterminism.
- **No placeholder materials:** in the editor, an image is never saved while shaders are compiling (Unreal draws a
  grey stand-in until a new material is ready); the run waits for them and renders the warm-up frames again.
- **Self-contained:** no Python or other downloads are needed to generate data.

