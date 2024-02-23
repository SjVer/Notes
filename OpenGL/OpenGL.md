OpenGL is not a typical library in the sense that it's really just a specification[^intro]. It is the graphics driver's task to implement that specification. This means that most OpenGL functions aren't known at compile-time and must be found/loaded before use. OpenGL's declarations are really just prototypes. **GLAD is a library that takes care of all that loading.**[^glad]

OpenGL is basically a state machine. Some functions set the state and some use it. For example, `glBindBuffer` would bind a buffer to OpenGL's _context_, and e.g. `glBufferData` would then do something to the currently bound buffer. **This mindset is pretty important**.

GLFW (and GLUT and the like) is a helper library that makes some of the more tedious parts of OpenGL, such as opening a window easier.

The graphics pipeline looks as follows, where the blue phases are [[Shaders|shaders]] we can control: 
![graphics pipeline](attachments/graphics%20pipeline.png)

[^intro]: Learn OpenGL [introduction](https://learnopengl.com/Introduction)
[^glad]: Learn OpenGL section on [GLAD setup](https://learnopengl.com/Getting-started/Creating-a-window#:~:text=to%2Ddate%20library.-,Setting%20up%20GLAD,-GLAD%20is%20an), GLAD must be `#include`'d **before** the rest!