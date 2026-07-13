# simple-pbd

![version](https://img.shields.io/badge/version-v1.0.0-blue)

A simple **Position-Based Dynamics (PBD)** elastic-cloth simulation that runs
entirely on the GPU in the browser.

**▶ Live demo:** https://vadik260.github.io/simple-pbd/

## What it does

A grid of point masses connected by distance constraints is simulated with the
PBD method: each step predicts new positions, then iteratively projects the
constraints to keep edge lengths near their rest length. Prediction, constraint
projection (graph-colored for parallelism), and integration all run in WebGPU
compute shaders; the mesh is drawn with a WebGPU render pipeline. You can adjust
size, elasticity, gravity, and a driven oscillating center point, and orbit /
zoom the camera with the mouse.

## Requirements — WebGPU

The app uses [WebGPU](https://developer.mozilla.org/docs/Web/API/WebGPU_API),
the modern browser GPU API. It needs **Chrome/Edge 113+** or **Safari 18+**; on
unsupported browsers it shows a message instead of rendering. If it doesn't
work, check whether WebGPU is available at https://webgpureport.org. Note that
WebGPU also requires a secure context (HTTPS or `localhost`).

