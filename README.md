# Spatial Primitive Language SPL-3D

_SPL-3D is a minimal, deterministic language that describes 3-D geometry as structured text.  
One can “recite lines” and raise space itself._

Author : **Koichi Nishizuka**  
License : **MIT**  
Repository : <https://github.com/NishizukaKoichi/spl3d-core>

---

## What is SPL-3D?

SPL-3D expresses finite-precision Euclidean structures with the smallest possible vocabulary.  
Every valid document is deterministic (A1), contains nothing superfluous (A2), and can represent any lattice-aligned polyhedron set in _O(n log n)_ length (A3).  
Because it is plain text, a shape that weighs megabytes in GLB can shrink to a few kilobytes — or even hundreds of bytes — when rewritten in SPL-3D.

---

## Repository layout

```

spec/          ── formal specification (Markdown)
examples/      ── working samples (.spl3)
LICENSE        ── MIT
README.md      ── this file

````

---

## Quick run

Open **examples/cube.spl3** and read it side by side with **spec/SPL-3D.md**; the example is self-explanatory.  
If you want instant visuals, clone this repo and point any future viewer or parser you write to that file; a reference implementation will be added later.

```spl3
geometry:
  X(0,0,0) 1 ×1
  Y(0,0,0) 1 ×1
  X(0,1,0) 1 ×1
  Y(1,0,0) 1 ×1
  Z(0,0,0) 1 ×1
  Z(1,0,0) 1 ×1
  Z(0,1,0) 1 ×1
  Z(1,1,0) 1 ×1
autoFace mode=tri
````

Running a viewer will show eight vertices and twelve triangular faces: the canonical closed cube.

---

## Why use SPL-3D?

* **Human-readable** — write or review a structure in any text editor.
* **Deterministic** — same document, same geometry; no hidden state.
* **Compact** — orders of magnitude smaller than mesh or GLB when structure is regular.
* **Unencumbered** — MIT-licensed; fork, embed, and extend freely.

---

## Citation

If SPL-3D helps your research or product, cite it so others can discover the language.

```bibtex
@manual{spl3d2025,
  title   = {Spatial Primitive Language (SPL-3D): Core Specification},
  author  = {Koichi Nishizuka},
  year    = {2025},
  note    = {Available at https://github.com/NishizukaKoichi/spl3d-core}
}
```

---

SPL-3D is offered as a tool, not a walled garden: take it, bend it, and let space rise at your command.
