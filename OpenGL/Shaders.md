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