Using stochastic/distributed rendering and an accumulator, an image can _converge_ to correctness over time. As soon as the camera moves, though, we have to start all over again.

We know that as long as the camera doesn't move too much, there is still useful information in the previous frame. The question is: _how do we find this information efficiently?_ We have to keep both translation and rotation in mind, as well as occlusion and disocclusion between frames.

If we can figure out what information we can reuse, we can converge much more quickly, like in this 
[Path traced GI shader on ShaderToy](https://www.shadertoy.com/view/Wt3XRX)

## Temporal Reprojection
The first step is to figure out where each pixel was in the previous frame. 

One thing we can do is store the world-space positions of the primary ray intersections in a separate buffer. In the new frame, we can then look this pixel up and calculate the motion vector. Looking the pixel up can be approached in a few different ways.
### Diamond Search
Starting at the 2D screen position P in the new frame, we look in a diamond pattern around P for the pixel Q in the previous frame for which we recorded the best matching world space coordinate. For every iteration we shrink the area in which we're looking and recenter it at the new best pixel. We repeat until we're content, or the search are is too small.
![|300](attachments/diamond%20search.jpg)

The final difference between P and Q is the 2D motion vector we're looking for. If we didn't find a good match, we just don't have a good sample.

An implementation of this can be found on [ShaderToy](https://www.shadertoy.com/view/ldtGWl).

### Pyramid Reprojection
The field of view of the camera has a pyramid shape, bounded by four planes. Given a 3D coordinate $P$, we can calculate its 2D screen space coordinate as follows:
$$x = \frac{d_1}{d_1 + d_2}$$
Where $d_1$ is the distance of $P$ to the left plane, and distance $d_2$ is the distance to the right plane:
![|300](attachments/pyramid%20reprojection.png)
Note that $x$ is in the range of $[0, 1]$ (if on-screen); multiply by the screen with and round to the nearest integer to the actual pixel position.

We need to know the distance between a point $P$ and the point $X$ on the plane with normal $N$:
$$dist = (N \cdot P) - (N \cdot X)$$
Pro tip: Store $N$ together with $-d$ in a single `float4` and turn $P$ into a `float4` with $w_P = 1$. Now the distance calculation is as simple as $dist = N \cdot P$.

We can now calculate the position in both the current, and the previous frame.

Note that this method does not work with mirrors, panini projection or a non-pinhole camera.

## Temporal Accumulation
When reprojection, we assume older data is more likely to be accurate. Hence, we can ditch the accumulator entirely, and use a _running average_:
$$V_{avg} = \alpha \space V_{prev} + (1 - \alpha) V_{new}$$

A smaller $\alpha$ supresses "history", for example, when we know something has changed. A bigger $\alpha$ effectively lets us blend more frames, reducing noise. An $\alpha$ of $0.9$ is typical.

Sometimes we cannot reproject, for example, when something has moved. When this happens, we lower $\alpha$ to $0$. We can also do this if the previous sample was obviously wrong (very different).

The final algorithm would look something like this: 
```python
Vnew = get_sample(uv)

old_uv = reproject(uv, camera_pos, old_camera_pos)
if old_uv in screen:
	old_intersection = prev_frame_intersection_buff[old_uv]
	delta = old_intersection - curr_intersection

	if delta < REPROJ_DELTA:
		# we have a valid previous sample
		Vprev = old_frame_sample_buff[old_uv]
		return ALPHA * Vprev + (1 - ALPHA) * Vnew
	
# we did not find a valid previous sample
return Vnew
```
