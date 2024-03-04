## Dielectrics
The direction of the transmitted vector depends on the refraction indices $n_1$, $n_2$ of the media separated by the surface. According to Snell's Law:
$$n_1 \cdot sin \space a_2 = n_2 \cdot sin \space a_2$$
or:
$$\frac{n_1}{n_2} sin \space a_1 = sin \space a_2$$
Where the angles $a_1$ and $a_2$ describe how the light ray "bends".
The final refracted vector will then be:
$$\vec T = \frac{n_1}{n_2} \vec D + \vec N \left( \frac{n_1}{n_2} cos \space a_1 - \sqrt k \right)$$
Note that this equation does not always hold true. These cases cause an effect called _Snell's Window_, and refraction cannot take place. This is called _total internal reflection_ (TIR), and results in zero transmittance/refraction.

A typical dielectric also reflects light, or in the case of TIR _only_ reflects light, based on the Fresnel equations:
$$F_r = \frac 1 2 \left( \left( 
	\frac{n_1 \space cos \space a_i - n_2 \space cos \space a_t}
		{n_1 \space cos \space a_i + n_2 \space cos \space a_t} 
\right)^2 + \left(
	\frac{n_1 \space cos \space a_t - n_2 \space cos \space a_i}
		{n_1 \space cos \space a_t + n_2 \space cos \space a_i} 
\right)^2 \right)$$
where:
$$cos \space a_t = \sqrt{1 - \left( \frac{n_1}{n_2} sin \space a_i \right)^2}$$
applied:
$$e_{total} = F_r * e_{refracted} + (1 - F_r) * E_{reflected}$$ 
Note that splitting/doubling the rays can be avoided by treating $F_r$ as a probability, sampling only one of the two rays (and accumulating the results).
### Beer's Law
The amount of light absorbed by a translucent object depends on the density of the object and the distance travelled through that surface, according to _Beer's Law_:
$$I(d) = I(0)e^{-ln(a)d}$$
applied:
$$E_{outgoing} = E_{incoming} * I(d) \text{ where } I(0) = C_{material}$$
or in pseudocode:
```
color light = ...;
vec3 absorption = albedo * exp(-LN_A * d);
color finalColor = light * absorption;
```
## Texture Sampling
To avoid oversampling (sampling the same texel over and over again) we can apply _bilinear sampling_: instead of clamping a UV coordinate to a single pixel we sample the 4 neighbouring pixels, taking a biased average.

This bias is based on the distances between the UV coordinate $P$ to the 4 neighbouring pixels $Q$:
![|400](attachments/bilinear%20sampling.png)
To calculate this, we first sample the top and bottom sets of pixels:
$$R_1 = f(x, y_1) = \frac{x_2-x}{x_2-x_1} f(Q_{11}) + \frac{x-x_1}{x_2-x_1} f(Q_{21})$$
$$R_2 = f(x, y_2) = \frac{x_2-x}{x_2-x_1} f(Q_{12}) + \frac{x-x_1}{x_2-x_1} f(Q_{22})$$
And then we blend those two rows:
$$P = f(x, y) = \frac{y_2-y}{y_2-y_1} f(x, y_1) + \frac{y-y_1}{y_2-y_1} f(x, y_2)$$

## Normal Interpolation
To smoothen surfaces the normals can be interpolated between. 
For this, we need _vertex normals_:
```
for each triangle t:
	for each vertex v in t:
		v.N += t.N
for each vertex v:
	normalize(v)
```
The surface normals are then just the interpolated vertex normals.

**NOTE:** When casting shadow rays, the origin should be on the original surface, not the smoothened one.

Normal interpolation can cause the rays to reflect _into_ the surface. This can be remedied by simply not using the interpolated normal whenever that happens; $dot(\vec T, \vec D) < 0$.