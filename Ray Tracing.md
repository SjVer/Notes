## Rays
A ray has an origin and a direction and when given a parameter results in a point along that ray:
$$P(t) = A + B \cdot t$$
where $A$ is the origin, $B$ is the direction and $t$ is the parameter.

A ray tracer "shoots" out a ray from the camera through each pixel in the screen and out into the world, hitting surfaces that will give it its color.
![|500](assets/ray%20tracing.png)
The direction of these rays is generally the UV coordinate of its pixel multiplied by the in-world dimensions of the window/image.

## Spheres
Recall the equation for a sphere:
$$(x - a)^2 + (y - b)^2 + (z - c)^2 = r^2$$
In terms of vectors $P = (x, y, z)$ and $C = (a, b, c)$ this becomes:
$$dot(P - C, P - C) = length(P - C) = r^2$$
Any point $P$ that satisfies this equation thus lies on the surface of the sphere:
$$length(P(t) - C) = r^2$$
$$dot(A + B \cdot t - C, A + B \cdot t - C) = r^2$$
$$t \cdot t \cdot dot(B, B) + 2 \cdot t \cdot dot(A-C, A-C) + dot(C, C) - r^2 = 0$$
$$t \cdot t \cdot length(B) + 2 \cdot t \cdot length(A-C) + length(C) - r^2 = 0$$
Since this is in the form of $ax^2 + bx + c = 0$ we have at least one hit if the discriminant ($b^2 - 4 \cdot a \cdot c$) is zero or positive:
$$length(B) \cdot t^2 + 2 \cdot length(A-C) \cdot t + length(C) - r^2 = 0$$
$$D = (2 \cdot length(A-C))^2 - 4 \cdot length(B) \cdot (length(C) - r^2) \geq 0$$
Putting this in code **and accounting for the offset from the origin** we get:
```cpp
bool hit_sphere(vec3 C, float r, Ray ray) {
	vec3 Crel = ray.origin - C;
	float a = length(ray.direction);
	float b = 2 * length(ray.origin - Crel);
	float c = length(Crel) - r*r;
	return b*b - 4*a*c >= 0;
}
```

To get the normal we solve for and return the smallest $t$:
```cpp
	...
	float d = b*b - 4*a*c > 0;
	if (d >= 0) return (-b - sqrt(d)) / (2 * a);
	// you should account for `-b + ...`
}
```
We can then just get the normal by subtracting the sphere's center from the point at that $t$, and taking the unit vector from that.

For an in-depth tutorial see [The Cherno's video](https://www.youtube.com/watch?v=4NshnkzOdI0&t=1s).
