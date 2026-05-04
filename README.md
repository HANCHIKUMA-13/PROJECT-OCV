# OCV — OpenFOAM Case Viewer

> Zero-install, drag-and-drop CFD case reviewer. Open it in a browser, drop a zipped OpenFOAM case, and get the geometry, parameters, and validation surface in one screen — without touching the terminal.

**Edition 0.0.2 · 04 MAY 2026**

---

## Live demo

Once deployed: **https://ocv.vercel.app** *(or whatever your Vercel domain ends up as)*.

Click **Load Demo** in the top-right to load the bundled `LES_RL9-10_v100ms.zip` — a Spalart–Allmaras DDES motorbike case at U∞ = 100 m/s, surface refinement levels 9–10.

## What it does

The page parses an uploaded `.zip` of an OpenFOAM case directory and renders five horizontal strips:

| Strip | Content |
|------:|---------|
| 0 | Active case name · brand mark · light/dark toggle |
| 1 | Console log · Upload / Validate / Reset / Demo buttons |
| 2 | 3-pane viewer — geometry tree (per-layer color, visibility, opacity) · Three.js scene · live properties |
| 3 | Dashboards — workflow pipeline, models, governing equations, boundary patches, parameters + CFL, mesh refinement, aerodynamics |
| 4 | Edition / copyright |

Strip 2 columns and Strip 3 height are drag-resizable.

## What it parses (so far)

From the uploaded zip the app extracts:

- `system/controlDict` (and `lesFiles/controlDict`) — application, deltaT, endTime, writeInterval
- `constant/transportProperties` — kinematic viscosity ν
- `constant/momentumTransport` — RAS / LES model, Δ filter, DDES variant
- `system/fvSchemes` — ddt, divergence, Laplacian schemes
- `system/fvSolution` — PISO/SIMPLE corrector counts
- `system/blockMeshDict` — domain vertices, base cell counts, **boundary patch list**
- `system/snappyHexMeshDict` — surface refinement levels, refinementBox, max global cells
- `system/decomposeParDict` — number of subdomains, decomposition method
- `system/forceCoeffs` — lRef, Aref, magUInf, lift/drag/pitch directions
- `0/U` — internalField vector + **boundaryField patch types**
- `model/*.obj.gz` — surface geometry, decompressed via pako and rendered with Three.js as named groups

## Stack

- Vanilla HTML/CSS/JS — single file, no build step
- [three.js r128](https://github.com/mrdoob/three.js/) — 3D viewport
- [JSZip 3.10](https://stuk.github.io/jszip/) — read uploaded archives in-browser
- [pako 2.1](https://github.com/nodeca/pako) — gunzip the `.obj.gz` geometry
- Google Fonts — Saira Condensed / Saira / JetBrains Mono

All three libraries are loaded from cdnjs; nothing is bundled.

## Local use

Open `OCV.html` directly in any modern browser, **or** serve the folder so the demo zip can fetch:

```bash
cd OCV
python3 -m http.server 8000
# open http://localhost:8000/OCV.html
```

Without an HTTP server, the demo button falls back to a metadata-only view; you can still drag any `.zip` onto the window for the full experience.

## File layout

```
.
├── OCV.html                  # the entire app
├── index.html                # tiny redirect → OCV.html
├── LES_RL9-10_v100ms.zip     # bundled demo case
├── vercel.json               # static-site config
├── README.md
├── LICENSE
└── .gitignore
```

## Roadmap (v0.0.3 candidates)

- Function-object outputs — render `forceCoeffs`, `cuttingPlane`, `streamLines` settings + sampled data
- Cd / Cl plotter from `postProcessing/forces/0/coefficient.dat`
- Multi-case diff
- Validation expanded into a structured checklist with severities
- VTK slice viewer for `cuttingPlane` outputs

## License

MIT — see [LICENSE](LICENSE).

## Acknowledgements

Demo case adapted from the OpenFOAM Foundation `motorBike` tutorial. The OCV viewer itself is original.
