Out of all the phases of the graphics pipeline, we can control the vertex, geometry, and fragment shaders:
![graphics pipeline](assets/graphics%20pipeline.png)

The _vertex shader_ calculates where the vertices need to be (relevant for e.g. waves); The geometry shader_ modifies the geometry, adding extra vertices and whatever, and the _fragment shader_ calculates the final color of each pixel.

## Compiling shaders
To compile a shader, we first need to create a shader object before compiling it and checking for errors[^compile]:

```cpp
const char* vertexShaderSource = "...";
unsigned int vertexShader = glCreateShader(GL_VERTEX_SHADER);

glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);

int success; 
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
if (!success) 
{ 
	char infoLog[512];
	glGetShaderInfoLog(vertexShader, 512, NULL, infoLog); 
	std::cerr << infoLog << std::endl; 
}
```

## Shader program
In order to actually use the shaders we need to put them in a shader program, which represents the completed graphics pipeline[^program]:

```cpp
unsigned int shaderProgram = glCreateProgram();

glAttachShader(shaderProgram, vertexShader); 
glAttachShader(shaderProgram, fragmentShader); 
glLinkProgram(shaderProgram);

int success;
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success); 
if (!success) 
{ 
	char infoLog[512];
	glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog); 
	... 
}
```

The shaders themselves can then be deleted using `glDeleteShader`, and the shader program can be used using `glUseProgram`.
## Example shaders
Vertex shader:
```glsl
#version 330 core 
layout (location = 0) in vec3 aPos; 

void main() 
{ 
	gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0); 
}
```
Note here that the location of `aPos` is specified and [used](Vertices.md#Vertex%20Attributes) when sending that data to the shader.

Fragment shader:
```glsl
#version 330 core 
out vec4 FragColor; 

void main() 
{ 
	FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f); 
}
```

[^compile]: Learn OpenGL section on [Compiling a shader](https://learnopengl.com/Getting-started/Hello-Triangle#:~:text=OpenGL%27s%20visible%20region.-,Compiling%20a%20shader,-We%20take%20the)
[^program]: Learn OpenGL section on [Shader programs](https://learnopengl.com/Getting-started/Hello-Triangle#:~:text=here%20as%20well!-,Shader%20program,-A%20shader%20program)