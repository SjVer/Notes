Not all noise is equal; some random number generators/sequences are more uniformly/evenly distributed than others, such as true random vs _Halton sequences_. 
For example, stochastic rendering will look less noisy when using _blue noise_ over truly random noise.

## Fireflies
When rendering stochastically, some rays will happen to bounce straight into lights, and other rays will not. This relatively high variance in incoming light will result in fireflies.

A naïve approach to supress fireflies is by just clamping each pixel's intensity:
```cpp
if (length(E) > MAX_INTENSITY * MAX_INTENSITY)
	E = MAX_INTENSITY * normalize(E);
```

## Filtering
The core idea behind filtering is to exploit the fact that illumination is typically _low-frequent_; nearby pixels tend to receive similar light, so we should be able to use the neighbours of a pixel to improve its color.

For this, we apply a kernel:
```cpp
color filter(x, y, radius)
{
	color sum;
	float summedWeight = 0;
	
	for (int iy = y - radius; iy < y + radius; iy++)
		for (int ix = x - radius; ix < x + radius; ix++)
		{
			float weight = calcWeight(iy, ix);
			sum += weght * samples[ix, iy];
			summedWeight += weight;
		}	
	
	return sum / summedWeight; 
}
```
The kind of blur (linear, Gaussian, ...) depends on the implementation of the `calcWeight` function here.

For a more in-depth explanation on this kernel see [these slides](https://edubuas.sharepoint.com/:b:/s/CMGT9/Ebl08eSeQstNqjz0cR_bk7MBCIR2OgiDFC1SRCAUdgzNmg?e=28ocuw) (starting slide 15).

