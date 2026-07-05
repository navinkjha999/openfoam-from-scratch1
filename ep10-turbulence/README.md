# Episode 10 — Turbulence Modeling

A complete, runnable **turbulent** case for **OpenFOAM From Scratch, Episode 10**.
It solves steady turbulent flow over the classic *pitzDaily* backward-facing
step using the **k-omega SST** RANS model — the separation-and-reattachment
case discussed in the video.

▶ Watch the episode: (https://youtu.be/StDNrkSBC1U)
📺 Full series:  (https://www.youtube.com/watch?v=JQZ6zmsFcmM&list=PLaCbL-x3lKB4&pp=sAgC)

---

## Important: get the mesh first

To keep this case small and 100% reliable, we **reuse the official pitzDaily
mesh** that ships with every OpenFOAM install, then layer our own turbulence
setup on top of it. So there are two quick steps.

### Step 1 — copy the official pitzDaily mesh

```bash
# copy the tutorial's blockMeshDict into this case
cp $FOAM_TUTORIALS/incompressible/simpleFoam/pitzDaily/system/blockMeshDict \
   system/blockMeshDict

blockMesh          # build the mesh
```

(The patch names in that mesh — `inlet`, `outlet`, `upperWall`, `lowerWall`,
`frontAndBack` — are exactly the ones our `0/` files below use.)

### Step 2 — run the turbulent solve

```bash
checkMesh          # should say "Mesh OK."
simpleFoam         # steady turbulent solver
paraFoam           # view: colour by U, add Stream Tracer to see the recirculation
```

---

## What's here (the Episode 10 teaching layer)

```
ep10-turbulence/
├── 0/
│   ├── U        p                 # velocity & pressure (inlet/outlet pairing)
│   ├── k                          # turbulent kinetic energy
│   ├── omega                      # specific dissipation rate
│   └── nut                        # turbulent (eddy) viscosity
├── constant/
│   └── turbulenceProperties       # ← selects RAS + kOmegaSST
└── system/
    ├── controlDict  fvSchemes  fvSolution
    └── (blockMeshDict — you copy this in from the tutorial, see Step 1)
```

The two files that make this a *turbulent* case, and that the video is really
about, are:

- **`constant/turbulenceProperties`** — sets `simulationType RAS` and
  `RASModel kOmegaSST`. Change `kOmegaSST` to `kEpsilon` to feel the difference.
- **`0/k`, `0/omega`, `0/nut`** — the new turbulence fields, each with
  wall-function boundary conditions (`kqRWallFunction`, `omegaWallFunction`,
  `nutkWallFunction`) that pair with a **y+ of roughly 30–300**, the
  wall-function strategy from the video.

---

## Try it yourself

1. **See the separation.** In ParaView, add a *Stream Tracer* — the flow
   detaches at the step and reattaches downstream. That recirculation bubble is
   the whole point of the case.

2. **Swap the model.** Change `RASModel` to `kEpsilon` in
   `turbulenceProperties` (you'll also want `epsilon` instead of `omega` in
   `0/` — copy it from the tutorial). Compare the reattachment length.

3. **Check y+.** After the run:
   ```bash
   simpleFoam -postProcess -func yPlus -latestTime
   ```
   Confirm y+ sits in the wall-function range. This is the habit from the video.

---

## Notes

- **Tested against:** OpenFOAM v2312 (openfoam.com / ESI). Field and
  wall-function names are stable across recent versions; on very old builds a
  wall-function name may differ — check the tutorial's own `0/` files.
- Why copy the mesh instead of shipping it? The pitzDaily `blockMeshDict` is
  intricate; reusing the official, version-matched one guarantees it meshes
  cleanly on your system. The turbulence setup — the actual Episode 10 content —
  is what we provide here.

---

OpenFOAM® is a registered trademark of OpenCFD Ltd. This series is not
affiliated with, endorsed by, or sponsored by OpenCFD Ltd or the OpenFOAM
Foundation.
