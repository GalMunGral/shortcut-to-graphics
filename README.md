# 3D Hands-On

## Rhetorical Design

### Audience

Those with no prior experience in computer graphics.

### Goal

Illustrate the principles of 3D surface rendering through direct manipulation
of real geospatial data, without prerequisite knowledge of graphics APIs or
geometric modeling.

### Challenge

Most introductions to computer graphics begin with triangular meshes and a
graphics API. The learning curve is steep: even legacy APIs like OpenGL require
understanding buffers, pipelines, and shader programs before a single pixel is
lit. Modern APIs are more complex still.

### Key Observation

Triangular meshes are necessary for geometric modeling, not for rendering. The
Phong lighting model requires only a surface normal — not a mesh. A digital
elevation model (DEM) supplies normals directly, bypassing mesh construction
entirely and reducing the problem to arithmetic on a height map.

### Strategy

We implement Phong shading from scratch in Python over a real LiDAR DEM
(Illinois statewide survey). An interactive widget exposes the shading
parameters — light direction, ambient, diffuse, specular coefficients — so the
reader can feel the effect of each before its formula is introduced.

## Technical Challenges

### Normal estimation

**Problem.** The DEM is a discrete grid; the surface normal at each point must
be approximated.

**Model.** A height map $`f(x, y)`$ defines a surface
$`\vec{r}(x, y) = (x,\, y,\, f(x, y))^\top`$.
The tangent vectors are its partial derivatives:

$$\frac{\partial \vec{r}}{\partial x} = \left(1,\, 0,\, \frac{\partial f}{\partial x}\right)^\top$$

$$\frac{\partial \vec{r}}{\partial y} = \left(0,\, 1,\, \frac{\partial f}{\partial y}\right)^\top$$

The surface normal is their cross product:

$$\vec{n} = \frac{\partial \vec{r}}{\partial x} \times \frac{\partial \vec{r}}{\partial y} = \left(-\frac{\partial f}{\partial x},\, -\frac{\partial f}{\partial y},\, 1\right)^\top.$$

**Algorithm.** Approximating the partial derivatives by central finite differences:

$$\vec{n}[i,j] = \Bigl(f[i,j-1] - f[i,j+1],\; f[i+1,j] - f[i-1,j],\; 2\Bigr)^\top.$$