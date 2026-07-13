# Changelog

## Safari / iOS WebGPU compatibility

The project rendered in Chrome but failed in Safari (desktop and iOS) — first
a `navigator.gpu` crash, then a blank canvas. Safari enforces parts of the
WebGPU spec that Chrome tolerates, so several latent issues had to be fixed.

### Fixed
- **`navigator.gpu` crash on unsupported browsers.** Feature-detect before use
  and show a friendly message linking https://webgpureport.org instead of
  throwing. (Some machines also have WebGPU suppressed by MDM/enterprise policy,
  which is environmental and not fixable in code.)
- **Blank canvas — uniform `array<u32>` stride.** WGSL requires a 16-byte
  element stride for arrays in the uniform address space; the tightly-packed
  `u32` buffers violated this. Moved `colorGroupSizes` / `colorGroupOffsets` to
  `read-only-storage` buffers (tight 4-byte stride, matching the CPU layout).
- **Blank canvas — raw buffers in bind groups.** Bind group entries passed a raw
  `GPUBuffer` as `resource`; Safari requires a `GPUBufferBinding`. Wrapped every
  entry as `resource: { buffer: ... }`.
- **`Storage buffers count(9) exceeded max count per stage(8)`.** The storage
  migration pushed the compute stage to 9 storage buffers; Safari caps it at 8.
  Moved the single-`u32` `colorIdx` back to a uniform buffer (count → 8). This
  also resolved a downstream `GPUCommandEncoder.finish: encoder state is 'Locked'`
  error caused by the invalid compute pipeline.
- **Stale assets.** Versioned subresource URLs (`model.js?v=N`, `style.css?v=N`)
  so a hard refresh actually reloads the code.
- **`favicon.ico` 404.** Added a real `favicon.svg`.

### Diagnostics added
- `navigator.gpu` / adapter guards with on-page messages.
- `uncapturederror` handler that surfaces GPU validation errors on the page.
- Shader-compilation error reporting and a validation error scope around
  pipeline creation, so failures show as red text instead of a silent blank.

### Other cleanups
- Corrected the projection aspect ratio to match the 600×400 canvas
  (was a stale `800/600`, which stretched the mesh).
- `freeGPU()` now destroys the color-group and color-index buffers that were
  previously leaked on re-initialization.
- Removed dead arguments from a `render()` call, bounded the wireframe color
  loop to the 8-entry palette, and minor tidy-ups.

## Deployment
- Entry point is `index.html`, so the site loads at the repository root URL on
  GitHub Pages.
