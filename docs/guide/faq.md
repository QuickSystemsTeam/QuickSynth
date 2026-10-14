# 12. FAQ


**Do I need Python or any other software to generate data?** No. Python is only needed if you want to train a model
afterwards, using whatever tools you already use.

**Does QuickSynth change my level?** No. Everything happens in the play session and is undone when play stops.

**Does installing QuickSynth change my project settings?** No. It ships no project settings. While a run is going it
temporarily turns on custom depth with stencil (needed for its label pass) and restores your setting afterwards.

**Can I use my own meshes and materials?** Yes: any static mesh and any material. *Randomize Color & Surface* can
also drive your own material if it has parameters named `BaseColor`, `Roughness`, `Metallic`, `Tiling`,
`TextureRotation` or `BaseTexture`.

**Can I label translucent objects such as glass?** Not yet: translucent materials don't appear in the ID pass.

**Can I spawn characters or skeletal meshes?** Not in v1: Spawn Volumes spawn static meshes.

**Can I run it from the command line?** Yes. Open the level in a standalone game (`-game`) with a Manager that has
*Capture On Play* ticked; capture starts automatically.

**Will the same settings give the same dataset?** The same seed, scene and settings give identical labels. Image
pixels can differ very slightly between GPUs and drivers.

**Where do I get help?** Email quicksystemssupport@gmail.com with your Unreal version, a description of the problem
and, if possible, the run's `quicksynth_report.txt` and `quicksynth_run.json`.
