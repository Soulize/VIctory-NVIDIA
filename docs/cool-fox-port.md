# cool_fox graphics/performance integration

Source: [DoubleGhosty/cool_fox](https://github.com/DoubleGhosty/cool_fox/tree/f4b48ad766ddbec1a1867022cd42f7645b0dd55e), fixed snapshot `f4b48ad766ddbec1a1867022cd42f7645b0dd55e`. The same revision was found in neevets/cool_fox and oceansde012/cool_fox. The original codec-d repository was unavailable. Shared 7900 XTX baseline: `0a43566532c57b34a5397d6fdc91e419de32e8e9`. NVIDIA baseline: `033be27f336e395745667df5fe20160e375209ca`.

## Integrated changes
- `e05d4633a073`: new plane compositor, table planning, fallback descriptor heaps, resource residency, fence wakes and DirectStorage.
- `1ad6e67aa28f`: safe reads of the retail 72-byte plane layout.
- `8cc31ab37afc`: Wave32 lowering, movie upload and XMA fixes, residency budgets.
- `18bdf7932b79`: heap-scoped refresh and bounded speculative staging.
- `ab75ac3e02ba`: pool allocation backoff and upload deferral under low system commit.
- `f4b48ad766dd`: descriptor metadata correctness and regression tests; Pareto settings adapted to the NVIDIA launcher.

The NVIDIA adapter selection, target-format PSO variants, constant markers, unsigned 16-bit views, NaN scrubbing, pipeline wait behavior, dynamic descriptor sizing, and Smooth Motion launch path remain. Fresh table planning includes half-view descriptors and prevents reuse of unconverted descriptors for those slots. Straight-alpha composition remains the default; premultiplied composition is opt-in. RT resolve and ray activation skipping require explicit opt-in. The private-release launcher and unrelated scripted-input change are not copied.

## Testing
Use the existing Windows CI (Clippy, all features, tests), then test locally with a real NVIDIA adapter and your own title files:
```powershell
cargo build --release --locked -p victory
.\scripts\play-nvidia.ps1 -Exe 'C:\path\to\your\executable.exe' -Adapter 'RTX' -Build release -NoSmoothMotion
.\scripts\play-nvidia.ps1 -Exe 'C:\path\to\your\executable.exe' -Adapter 'RTX' -Build release -NoSmoothMotion -ParetoResidency
```

Compare identical scene, resolution, cache state, driver, and settings; record frame-time median, 1% low, GPU memory, system commit, dropped draws, and device removal. Verify plane blending, faces/skin, text, menus, first-scene lighting, texture streaming, and movies. No NVIDIA runtime or frame-rate measurements have been made for this integration. Do not merge until CI and real hardware checks are satisfactory.
