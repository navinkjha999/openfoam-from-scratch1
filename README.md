# Episode 3 — Meshing with blockMesh

The complete, runnable case from **OpenFOAM From Scratch, Episode 3**.
This is the lid-driven cavity we build by hand in the video.

▶ Watch the episode: [ADD VIDEO LINK]
📺 Full series: [ADD PLAYLIST LINK]

---

## What's here

```
ep03-blockmesh/
├── 0/
│   ├── U            # velocity field + boundary conditions
│   └── p            # pressure field + boundary conditions
├── constant/
│   └── transportProperties   # kinematic viscosity (nu)
└── system/
    ├── blockMeshDict         # ← the star of the episode
    ├── controlDict
    ├── fvSchemes
    └── fvSolution
```

The heavily-commented `system/blockMeshDict` is the file we walk through
line by line in the video: `convertToMeters`, `vertices`, `blocks`,
`simpleGrading`, and `boundary`.

---

## Run it

From inside the `ep03-blockmesh/` folder, with your OpenFOAM environment
sourced:

```bash
blockMesh      # build the mesh from blockMeshDict
checkMesh      # inspect it — you want to see "Mesh OK."
```

That's all Episode 3 requires. If you want to go further and actually solve
the flow (as we did "blind" back in Episode 1):

```bash
icoFoam        # solve the incompressible flow
paraFoam       # open the result in ParaView
```

---

## Try it yourself

1. **Change the resolution.** In `blockMeshDict`, change `(20 20 1)` to
   `(40 40 1)`. Re-run `blockMesh` — four times as many cells.

2. **Add grading.** Change `simpleGrading (1 1 1)` to `simpleGrading (8 1 1)`.
   Re-run and open in ParaView: the cells now bunch toward the left wall.

3. **Break it (like in the video).** Swap two vertices in the `hex` line —
   e.g. `hex (0 3 2 1 4 5 6 7)`. Run `checkMesh` and watch it report
   **negative volume cells**. This is why vertex order matters.

---

## Notes

- **Tested on:** OpenFOAM v2312 (openfoam.com / ESI). Older or `.org`
  versions are almost identical here; if `noSlip` isn't recognised on a very
  old version, use `fixedValue; value uniform (0 0 0);` in `0/U` instead.
- This is a 2D case: the `frontAndBack` patch is `empty`, and the block is one
  cell thick in z. That's how OpenFOAM does 2D.
- Units are checked by OpenFOAM via the `dimensions` entries — a feature we
  lean on throughout the series.

---

OpenFOAM® is a registered trademark of OpenCFD Ltd. This series is not
affiliated with, endorsed by, or sponsored by OpenCFD Ltd or the OpenFOAM
Foundation.
