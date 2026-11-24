# AMD RDNA4 + Linux notes

VIctory for **AMD RDNA4 (RADV) on Linux under Wine/Proton**. It is the `integration` branch
(VIctory `main` + tested open PRs, credits in `INTEGRATION.md`) plus fixes and measurements made on
this platform. Fixes that also help Windows/NVIDIA go upstream (Marvisak/VIctory) and to
[JMAN730/victory](https://github.com/JMAN730/victory).

## Tested setup

- RX 9070 XT (RDNA4), Mesa RADV, CachyOS (kernel 7.x), 30 GB RAM.
- GE-Proton11-14 wine run directly (not through the Proton script).
- **vkd3d-proton master** (`d3d12.dll`, `d3d12core.dll` next to `victory.exe`). Stock vkd3d-proton 3.1
  crashes at world entry (its dxil-spirv makes invalid SPIR-V that crashes RADV); master or the DLLs from
  GE-Proton11-14 do not.
- `WINEDLLOVERRIDES="d3d12,d3d12core,dxgi,d3d11,d3d10core=n,b"`, `VKD3D_CONFIG=ignore_shared_fence`,
  `VICTORY_NO_WATCHDOG=1`.
- Free memory matters: anything pinning GPU memory (local LLM servers, etc.) pushes the world into swap.

## Build (from Linux)

```
cargo xwin build --release -p victory --target x86_64-pc-windows-msvc
# add --features diagnostics for per-frame copy/watch statistics
```

## Status on this machine

Menus at 30 fps; the world loads and plays audio but renders black (shaders that do not translate
make their draws skip). World frame rate is limited by title-memory copies (0.2-0.8 fps); see
VIctory #783 and #400.
