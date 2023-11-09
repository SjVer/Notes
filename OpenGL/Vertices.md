Vertices are often just 3 (or more) floats packed in a vector. To be able to send those to the GPU a few things need to happen.

## Vertex Buffer
First of all, the vertices need to be put into a vertex buffer. These buffers manage the memory and allow us to send vertices in big batches to the GPU[^vertexbuffer]:

```cpp
// create and bind the buffer
unsigned int vertexBuffer;
glGenBuffers(1, &vertexBuffer);
glBindBuffer(GL_ARRAY_BUFFER, vertexBuffer);

// copy the data into the buffer
glBufferData(GL_ARRAY_BUFFER, size, data, mode);
```

## Vertex Attributes
Secondly, we need to tell OpenGL how to interpret the vertices[^vertexattribs]. The following attributes describe how the data is formatted (in this example):
![vertex attributes](assets/vertex%20attributes.png)
- the *position* is where the buffer is located in memory;
- the *offset* is the point in the buffer from which we start to care about the data (which is often right at the start, so 0);
- the *size* of each element/vertex, or the amount of sub-elements it has;
- and the *stride*, which is the size in bytes of one element of the buffer.

The attributes of the buffer in the example would be set as follows:

```cpp
glVertexAttribPointer(
	0,                    // buffer's index
	3,                    // count of sub-elements
	GL_FLOAT,             // type of sub-element/data
	GL_FALSE,             // not normalized
	3 * sizeof(float),    // stride
	(void*)0              // offset (not really NULL, but just zero)
);
glEnableVertexAttribArray(0 /*buffer's index*/);
```

## Vertex Array
To avoid having to set the vertex attributes every time we swap vertex buffers we can use a vertex array to hold on to both the buffer and the attributes[^vertexarray]. When a vertex array is bound, any attribute calls will be stored in that array:
![vertex array](assets/vertex%20array.png)

Creating, binding and using a vertex array works as follows:

```cpp
// create and bind the vertex array
unsigned int vertexArray;
glGenVertexArrays(1, &vertexArray);
glBindVertexArray(vertexArray);

// do vertex buffer and vertex attribute stuff
...
```

The vertex array can then be passed around without having to drag the buffers and attributes around too.

[^vertexbuffer]: Learn OpenGL section on [Vertex Buffers](https://learnopengl.com/Getting-started/Hello-Triangle#:~:text=about%20graphics%20programming.-,Vertex%20input,-To%20start%20drawing)
[^vertexattribs]: Learn OpenGL section on [Vertex Attributes](https://learnopengl.com/Getting-started/Hello-Triangle#:~:text=Linking%20Vertex%20Attributes)
[^vertexarray]: Learn OpenGL section on [Vertex Arrays](https://learnopengl.com/Getting-started/Hello-Triangle#:~:text=restore%20its%20state%3F-,Vertex%20Array%20Object,-A%20vertex%20array)