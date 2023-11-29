All vision is based on light being absorbed and being reflected.

In a game, light is generally simple enough to be simulated with RGB values and some funky maths, for example by multiplying colors with some light color value.
From our viewport we can think of light being cast "backwards" from our viewport.

## Ambient light
The general low-level light that we can assume is all around is the _ambient_ light. This is pretty easy to emulate:
```glsl
float ambientStrength = 0.2f;
vec3 ambient = ambientStrength * lightColor;

vec4 color = vec4(ambient * objectColor, 4.0f);
```
Ambient light is pretty boring, but very simple and easily adjustable.

## Point/diffuse light
A point light is like ambient lighting, but with fading over distance. If the light is strong enough it also causes _diffusion_, which depends on the angle of a face relative to the light's direction:
```glsl
vec3 norm = normalize(normal);
vec3 lightDir = normalize(lightPos - fragPos);  
float diff = max(dot(norm, lightDir), 0.0f);
vec3 diffuse = diff * lightColor;

vec4 color = vec4(diffuse * objectColor, 4.0f);
```
So the shallower a surface, the less light it receives. If it is turned away from the light source it receives no light.
Softer diffuse lighting can be created using _attenuation_.

## Spotlights
Spotlights are like point lights but with direction. Go figure.

## Phong shading
Phong shading combines ambient, diffuse and specular lighting to emulate reasonable but simple lighting. It depends on:
- the object's color;
- the light's color;
- the light's intensity;
- the distance to the light;
- the amount of light;
- the direction of the light;
- the normal of the surface

A lot of this data is generally "imported" as textures:
```glsl
uniform sampler2D texture;
uniform sampler2D normalsTexture;
uniform sampler2D specularTexture;
uniform sampler2D emissionTexture;
```

## Shadows
When treating the distance between a light and a surface as a height (after twisting reality with a matrix) we can use a heightmap for determining shadows (like an umbrella with rain). This data can be gathered using a shader into a texture: a shadow map.
OpenGL can do this pretty well, as can OpenGLES3, but its still hard.[^shadowmap]

[^shadowmap]: Post on shadow mapping on [Brian's forum](http://scratchpadgames.net/forums/showthread.php?tid=61)