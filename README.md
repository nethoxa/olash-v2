# OLASH-V2: Gerstner Waves with Per-Vertex Lighting

Real-time water surface simulation combining GPU-driven Gerstner wave displacement with a three-component per-vertex lighting model, rendered with OpenGL 4.0.

## Gerstner Wave Model

The vertex shader displaces mesh vertices according to the Gerstner (trochoidal) wave model, where water particles trace circular orbits producing sharp crests and broad troughs. For a vertex at position **P** with normalized wave direction **D** projected onto the XZ plane:

```
k       = 2 * pi / lambda                    wave number
omega   = sqrt(g * k)                        angular frequency (dispersion relation)
f       = k * (D . P) - omega * t            phase

P'.x    = P.x + (Q / k) * D.x * cos(f)
P'.y    = P.y + (Q / k) * sin(f)
P'.z    = P.z + (Q / k) * D.z * cos(f)
```

Wave speed follows the deep-water dispersion relation `sqrt(g / k)` with g = 9.8 m/s^2 -- longer waves propagate faster, matching real ocean physics.

## Lighting Model

The surface normal at each displaced vertex is constructed analytically from the wave direction:

```
T  = normalize(1 - D.x^2,  0,  -D.x)        tangent (perpendicular to wave direction in XZ)
B  = cross(up, T)                             binormal
N  = normalize(cross(T, B))                   surface normal
```

Three additive lighting components are evaluated per-vertex:

| Component | Formula | Role |
|-----------|---------|------|
| **Diffuse** | `max(N . L, 0) * C_material` | Lambertian shading from a point light at (0, 0.03, 0) |
| **Ambient** | `k_ambient * C_material` | Constant base illumination |
| **Indirect** | `k_indirect * C_material * max(N . L_indirect, 0)` | Fill light at the origin to soften shadows |

### Parameters

| Parameter | Value |
|-----------|-------|
| Steepness (Q) | 0.4 |
| Wavelength | 0.25 |
| Time step | 0.005 s |
| Ambient intensity | 10.0 |
| Indirect intensity | 2.0 |

## Building

### Prerequisites

| Dependency | Install (Debian/Ubuntu) |
|------------|------------------------|
| CMake 3.25+ | `sudo apt install cmake` |
| C++14 compiler | `sudo apt install g++` |
| OpenGL 4.0 | GPU drivers |
| GLFW 3 | `sudo apt install libglfw3-dev` |
| GLM | `sudo apt install libglm-dev` |

### Compile and Run

```bash
cd src
mkdir build && cd build
cmake ..
make
./olash-v2
```
