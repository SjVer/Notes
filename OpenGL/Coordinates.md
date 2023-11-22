There are a total of 5 different coordinate systems that are of importance to us[^systems]:
- Local space (or Object space)
- World space
- View space (or Eye space)
- Clip space
- Screen space

To go from local space all the way to the screen a few transformations need to be applied:
![|800](assets/coordinate%20systems.png)
1. Starting with the coordinates of the object and its vertices relative to itself.
2. The next step is to transform the local coordinates to world-space coordinates.
3. Next we transform the world coordinates to view-space coordinates according to the camera's perspective.
4. After that the coordinates are clipped. Clip coordinates are processed to the `-1.0` and `1.0` range and determine which vertices will end up on the screen. Projection to clip-space coordinates can add perspective if using perspective projection.
5. And lastly the viewport transform is applied to map the coordinates from `-1.0` and `1.0` to the coordinate range defined by `glViewport`. The rest is up to OpenGL.

## The matrices
The view matrix basically applies the inverse of the [transformation](Transformations.md#Transformations) of the camera to every object. It is usually calculated using a "LookAt" matrix. This matrix takes into account the position, $\vec p$ and the right, up and direction vectors $\vec r$, $\vec u$ and $\vec d$:[^matmul]
$$LookAt = \begin{bmatrix} r_x & r_y & r_z & 0 \\ u_x & u_y & u_z & 0 \\ d_x & d_y & d_z & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix} \cdot \begin{bmatrix} 1 & 0 & 0 & -p_x \\ 0 & 1 & 0 & -p_y \\ 0 & 0 & 1 & -p_z \\ 0 & 0 & 0 & 1 \end{bmatrix}$$
Functions such as _glm_'s `glm::lookAt(vec3 pos, vec3 center, vec3 up)` calculate and apply this matrix themselves to calculate a view matrix.

The project matrix depends on the kind of projection; orthographic or perspective. It maps the view volume (a weird pyramid in the case of perspective projection) to a unit cube (with all vertices at `1`s or `-1`s). After that it's really easy to clip stuff out.

The perspective projection matrix is as follows, with $n$ as near, $f$ as far and $t$ as the top (along the $z$ axis, and assumed to be symmetrical along the $y$ axis).[^projmat]
$$\begin{bmatrix} \frac n r & 0 & 0 & 0 \\ 0 & \frac n t & 0 & 0 \\ 0 & 0 & \frac{f+n}{f-n} & \frac{2fn}{f-n} \\ & 0 & -1 & 0 \end{bmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ w \end{pmatrix} = \begin{pmatrix} \frac n r x \\ \frac n t y \\ \frac{f+n}{f-n}z + \frac{2fn}{f-n}w \\ -z \end{pmatrix}$$
The depth is conveniently stored as the new $w$ component. The negation there is because of the right-hand coordinate system.[^hand]

As with [combining transformation matrices](../Transformations.md#Combination), the transformations between coordinate systems can be combined by multiplying them in the correct order: projection; view; model (for column vectors). This final matrix is often called the _MVP_ as row vectors (row-major matrices) are more common. 

[^systems]: Learn OpenGL chapter on [coordinate systems](https://learnopengl.com/Getting-started/Coordinate-Systems#:~:text=About-,Coordinate%20Systems,-In%20the%20last)
[^matmul]: Notes on [matrix multiplication](Transformations.md#Matrix%20multiplication)
[^projmat]: Article on the [projection matrix](http://www.songho.ca/opengl/gl_projectionmatrix.html)
[^hand]: Learn OpenGL on the [right-handed system](https://learnopengl.com/Getting-started/Coordinate-Systems#:~:text=are%20moving%20backwards.-,Right%2Dhanded%20system,-By%20convention%2C%20OpenGL)