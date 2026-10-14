# 9. Rendering quality


QuickSynth renders images with **your project's own rendering settings**, so image quality is controlled the same way as
everything else in Unreal: in **Project Settings → Engine → Rendering**. Installing QuickSynth doesn't change any of
your project's settings.

**Lighting method.** Images use the project's Global Illumination and Reflection methods. With Lumen (the default for
new UE5 projects), images get sky light and bounce light like the viewport. Unreal normally switches Lumen off for
scene captures; QuickSynth turns it back on for its images. To use a different method for one camera's images (for
example none, for a flat stylized look), tick *Use Camera Post Process* on that camera and set the method in its *Post Process*.

**Stylized datasets.** Everything about the look is yours: your lights and sky, your materials (*Keep Mesh Materials*
keeps unlit or toon materials as authored), your Post Process Volumes, and the camera's own Post Process. The
Lighting Randomizer is optional, and each of its parts can be turned off. Labels come from a separate clean pass, so a
stylized look never changes label quality.

**Hardware ray tracing** (sharper reflections and more accurate indirect lighting, on GPUs that support it):

1. Tick **Support Hardware Ray Tracing** and restart the editor (the first start compiles shaders and takes a while).
2. Under **Lumen**, tick **Use Hardware Ray Tracing when available**.

QuickSynth uses it automatically. Labels, masks and depth are not affected: they come from a separate, clean pass.
Expect capture to be somewhat slower.

**Path tracing** (offline, film-quality rendering) isn't supported yet: QuickSynth's image capture can't path trace,
so turning on the path tracer in your project doesn't change QuickSynth's images. A Path Tracer / Movie Render Queue
mode is planned for a later version.

**Shadows of small objects.** Unreal normally skips the shadows of objects that look small on screen, to save time;
how small depends on your project's shadow quality. QuickSynth turns that off while capturing (*Draw Small Object
Shadows* on the camera, Advanced), so every object casts its shadow whatever your quality settings are. Your setting
is restored when the run ends.

**Other settings that matter:**

- **Resolution** (Manager) and **Warm Up Frames** (camera) trade speed for quality; see [Best practices](best-practices.md#10-best-practices).
- **Exposure:** auto exposure by default; *Locked Exposure EV100* (camera, Advanced) for fixed brightness.
- Images look like what you see through the camera in the viewport. With auto exposure, brightness can differ
  slightly right after the camera moves, because the viewport's exposure adapts gradually; lock the exposure if you
  want the two to match exactly.

