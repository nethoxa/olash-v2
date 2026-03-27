Building on [olash-v1](https://github.com/nethoxa/olash-v1), this version adds a per-vertex lighting model computed in the vertex shader alongside the wave displacement. The surface normal at each displaced vertex is constructed analytically from the wave direction:

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

### Demo

https://github.com/user-attachments/assets/de2153ea-f411-43ed-9c10-a0c2873d1670
