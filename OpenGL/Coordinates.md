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

[^systems]: Learn OpenGL chapter on [coordinate systems](https://learnopengl.com/Getting-started/Coordinate-Systems#:~:text=About-,Coordinate%20Systems,-In%20the%20last)