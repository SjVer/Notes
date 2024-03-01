## Anti-aliasing
Sending multiple rays through each pixel, averaging their results makes edges look smoother.

Problem:
- Where do we send those rays?
- What if their colors are all the same?

A simple solution is dividing the pixel in equal sub-pixels, one for each ray. This "roster" can, however, interfere with any existing rosters like textures or 3D voxel grids. The result will be a weird kind of interference noise that's annoying to look at.

To avoid noise like this, the rays can be send in random directions through the pixel. A drawback here is that the image will look more noisy.

Combining the subdivision and randomization fixes both drawbacks: **stratification**. It guarantees relatively uniform distribution of the pixels whilst avoiding interference.

Anti-aliasing acknowledges that pixels are tiny squares, not points. The color of pixels should represent everything we can see through that square, thus sending multiple samples per pixel makes sense. Of course we cannot sample every existing point inside the pixel, but the randomization at least gives every location a chance.

## Distributed Rendering
Soft shadows emerge when using area lighting; lights aren't really a single point in space. The same counts for reflections, which, in general, aren't perfectly sharp either.

In the single-point light model, we send just a single shadow ray to check for occlusion, which results in hard shadows. Similarly, when sending just one perfectly reflected ray off surfaces, we get perfect reflections.

The solution to both is **distributed raytracing**. Instead of *point sampling* distributed raytracing means *area sampling*; we send multiple rays to get a better result.

### Area lights
When treating lights as a single point we get hard shadows, but area lights emit light from a larger area onto the same surface, resulting in soft shadows; umbra's and penumbra's. The soft part of the shadow is the result of the area light being only partly occluded, and only partly visible.

This means we need to figure out how much of that area is visible by applying a visibility function $V$ for every direction $\omega$ to area $A$ from a position $x$:
$$V_A = \int_A V(x, \omega)d \omega$$
Or analytically:
$$A_{visible} = A_{light} - A_{(light \space \cap \space sphere)}$$
Or approximately using the *Riemann sum*:
$$V_A = \int_A^B V(x)dx \approx \sum_{i=1}^N V(t_i) \Delta_i \text{ where } \sum_{i=1}^N \Delta_i = B - A$$Note that the intervals do not need to be uniform. as long as sample along the full interval. If the intervals are uniform, then we can compute this as follows:
```cpp
float sum = 0.0f;
for (ti = A; ti < B; ti += (B - A) / N)
	sum += V(ti);
float average = sum / N;
return (B - A) * average;
```
To estimate using *Monte Carlo Integration* of Area Light Visibility we calculate the visibility of $N$ random points $P$ inside the area:
$$V_A \approx \frac 1 N \sum_{i = 1}^N V_A(P)$$
Or, implemented programmatically:
```cpp
float sum = 0.0f;
for (int i = 0; i < N; i++)
{
	float3 point = random_point_in_area(A);
	sum += is_visible(point, x); // 0.0f or 1.0f
}
return 1.0f / N * sum;
```

### Accumulating
By itself, the anti-aliasing as described above will result in noisy and jittery images. We can counteract this by simply taking more samples, but this increases render times too.

The solution is to accumulate the samples of previous frames. This increases the sample count for every consecutive frame whilst preserving performance.

An implementation can be very simple:
```cpp
color Accumulate(color sample, int u, int v)
{
	accumulator[u + v * RESOLUTION_WIDTH] += sample;
	return accumulator[u + v * RESOLUTION_WIDTH] / frameCount;
}
```