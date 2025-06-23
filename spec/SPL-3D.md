# Spatial Primitive Language — **SPL‑3D**

*A minimal, deterministic language for finite‑precision Euclidean space*

---

## 0  Overview & Axioms

| Axiom                           | Statement                                                                                                                    |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **A1 Determinism**              | A valid SPL‑3D document always produces exactly one geometric realization.                                                   |
| **A2 Minimal Sufficiency**      | No construct that is unnecessary to recreate that realization appears in the core grammar.                                   |
| **A3 Extensional Completeness** | Every lattice‑aligned polyhedral complex with *n* vertices can be expressed by an SPL‑3D description of length *O(n log n)*. |

---

## 1  Global Conventions

| Item              | Rule                                                         |
| ----------------- | ------------------------------------------------------------ |
| Character set     | UTF‑8                                                        |
| Units             | SI (m, kg, s, rad, N, Nm)                                    |
| Coordinate system | Right‑handed (x forward, y left, z up)                       |
| Comment syntax    | `# …` to end of line                                         |
| File extensions   | **.spl3** (core)   **.spl3x** (extended)                     |
| File size limit   | 1 GiB → error `file_too_large`                               |
| Numeric tokens    | Finite IEEE‑754 double. NaN/Inf → error `invalid_coordinate` |

---

## 2  Core Grammar (`.spl3`)

*(LL(1), deterministic)*

```bnf
<doc>       ::= { <section> }

<section>   ::= "geometry:" <nl> { <g-line> }
             | "physics:"  <nl> { <p-line> }
             | "joint:"    <nl> { <j-line> }
             | "io:"       <nl> { <io-line> }

<g-line>    ::= <edge> | <face> | <repeat> | <autoFace>

<edge>      ::= "edge" <vec3> <vec3>
<face>      ::= "face" "[" <indices> "]"
<repeat>    ::= <axis> "(" <vec3> ")" <num> "×" <int> [<num>]
<autoFace>  ::= "autoFace" { <opt> }

<axis>      ::= "X" | "Y" | "Z"
<vec3>      ::= <num> "," <num> "," <num>
<indices>   ::= <int> { "," <int> }
<opt>       ::= "axis="("xy"|"yz"|"xz"|"all")
              |"mode="("quad"|"tri")
              |"maxArea="<num>
              |"eps="<num>
              |"angEps="<num>
              |"subdiv="<int>
<num>        ::= [+|-]digit*["."digit*]
<int>        ::= digit+
```

---

## 3  Generative Semantics

### 3.1 Vertex Merging

Dynamic tolerance εₚ = 10⁻⁶ × **L<sub>max</sub>** (the longest edge length in the document). `autoFace eps=x` overrides this.
Duplicate edges → keep first, warn `duplicate_edge`.

### 3.2 Edge Emission

* **edge** adds an unordered vertex pair.
* **repeat** `A(p) ℓ × n [δ]`

  * If ℓ < 0 → reverse direction, use |ℓ|.
  * If δ omitted → δ := |ℓ|.
  * `×0` produces nothing; warn `empty_repeat`.

### 3.3 autoFace (automatic surface weaving)

Default options `axis=all`, `mode=quad`, `maxArea=∞`, `eps=εₚ`, `angEps=10⁻⁶ rad`, `subdiv=1`.

1. **Plane clustering** by `axis`.
2. **Orthogonality** |dot| ≤ sin(`angEps`).
3. **Quad‑loop search** (4 edges, closed).

   * Non‑convex loops → reject `non_convex_loop`.
   * If many candidates → choose **minimum area**, then lexicographic vertex order.
4. Area > `maxArea` → reject `area_exceed`.
5. `subdiv=N` → subdivide each quad into N×N grid.
6. `mode=tri` → immediate triangulation.
7. Edge intersections generate no implicit vertices.

### 3.4 Explicit face rules

\* < 3 vertices → `degenerate_face` error.
\* 3 or 4 vertices → accepted.
\* ≥ 5 vertices → implementation MAY Ear‑cut triangulate, else warn `unsupported_face`.

---

## 4  Physics / Joints / I‑O (positional shorthand allowed)

```spl3
physics:
  body base 5 diag(0.01,0.02,0.01)   # mass inertia (kg, kg·m²)

joint:
  hinge knee thigh shin 0,1,0 0,0,-0.3 -1.57 0

io:
  sensor imu base 400 0.005
  motor  knee_act knee torque 120
```

Units: mass kg, inertia kg·m², spring N/m, damping N·s/m.

---

## 5  Extended Layer (`.spl3x`)

* Variables `$r = 0.1`
* Macros `@box()`
* Conditionals `#if` … `#endif`
* Blocks `[optimize]`, `[validate]`
* Macro recursion depth ≤ 256 → error `macro_cycle_detected`

`.spl3x` **must** be expanded to `.spl3` before execution.

---

## 6  Primary Error Codes

`invalid_coordinate`, `file_too_large`, `duplicate_edge`, `empty_repeat`, `non_convex_loop`, `area_exceed`, `degenerate_face`, `unsupported_face`, `macro_cycle_detected`

---

## 7  Complexity & Theoretical Properties

* Parsing LL(1) Time O(T) Space O(V + E)
* Description upper bound *c·n log n*
* Surface weaving BFS + area sort O(E + F)
* Reference TS implementation ≈ 500 LOC

---

## 8  Minimal Cube Example (12 lines)

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
```

*Result → 8 vertices, 12 triangles: a closed 1 m cube.*

---

## 9  Citation (BibTeX)

```bibtex
@manual{spl3d2025,
  title  = {{Spatial Primitive Language (SPL-3D): Core Specification v2.0}},
  author = {Koichi Nishizuka and contributors},
  year   = {2025},
  note   = {URL: https://github.com/spl3d/spec}
}
```

---

**SPL‑3D** provides the “arithmetic of space”: the minimal, fully‑deterministic language for describing and reconstructing 3‑D structure with finite precision.
