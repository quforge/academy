# OpenGL Cheat Sheet

## Shaders 
OpenGL Shading Language (GLSL) is used to write programs called shaders that run on GPU's. 
Each shader has a list of inputs and outputs as well as *uniforms* (global variables). 
The shader does something on the inputs and produces the outputs. 
In addition to normal types such as `int`, `uint`, `float`, `double`, and `bool`, shaders support vector and matrix types such as `vec3` and `mat4`. 
Different components of a `vector` can be accessed as `vector.x`, `vector.y`, `vector.z`, and `vector.w`. 

### Swizzling 
```c++
vec2 vector = vec2(1.0, -1.0); // (1.0, -1.0)
vec4 copyVector = vector.xyxy; // (1.0, -1.0, 1.0, -1.0)
```

### Initializing 
```c++
vec2 unitVector = vec2(1.0, 0.0);
vec4 biggerUnitVector = vec4(unitVector, 0.0, 0.0); 
```

### Example code
Here is an example for the vertex shader which takes the coordinates of the vertices as the input and outputs the `vertexColor` which is then used by the fragment shader. 
```c++
#version 330 core
layout (location = 0) in vec3 pos;
 
out vec4 vertexColor;

void main()
{
    gl_Position = vec4(pos, 1.0); 
    vertexColor = vec4(0.5, 0.5, 0.5, 0.0);
}
```
- **Input and Output**: Inputs are denoted by `in` and outputs by `out`. 
- **Cascading**: Shaders can be cascaded where the output of one shader is the input of another (they should have the same type and name). 
- **Layout**: `layout (location = 0) in vec3 pos` means that the `pos` vector is located in the first slot (index 0) in the data sent to the GPU. 

## GLM (Open GL Mathematics Library)
This is a header-only math library. 
These 3 header files are used commonly. 
```c++
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>
```

## Vectors 
The vectors are 4 dimensional. 
The fourth component $w$ denotes whether we are working with a position vector (homogeneous coordinate) in which case $w = 1$ or we are working with a direction vector $w = 0$. 
$$
\begin{bmatrix}
    x \\ y \\ z \\ w
\end{bmatrix}
$$
Vectors and matrices can be defined using GLM
```c++
glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);
glm::mat4 identity = glm::mat4(1.0f);
```

## Transformations 

Transformations are 4 by 4 matrices. 

### Scaling 
$$
\begin{bmatrix}
    S_1 & 0 & 0 & 0 \\
    0 & S_2 & 0 & 0 \\
    0 & 0 & S_3 & 0 \\
    0 & 0 & 0 & 1
\end{bmatrix}
$$
If $S_1 = S_2 = S_3$, it's called uniform scaling. Otherwise it's non-uniform scaling. 
In GLM a scaling matrix can be initialized this way 
```c++
glm::mat4 scaling = glm::scale(identity, glm::vec3(1.5, 1.5, 1.5));
```
The scaled vector is obtained by
```c++
vec = scaling * vec;
```

### Translation 
$$
\begin{bmatrix}
    1 & 0 & 0 & T_x \\
    0 & 1 & 0 & T_y \\
    0 & 0 & 1 & T_z \\
    0 & 0 & 0 & 1
\end{bmatrix}
$$
In GLM we can initialize a matrix that translates by (2, 3, 0)
```c++
glm::mat4 translation = glm::translate(identity, glm::vec3(2.0f, 3.0f, 0.0f));
```
The translated vector is obtained by
```c++
vec = translation * vec;
```

### Rotation
$$
R_x(\theta) = 
\begin{bmatrix}
    1 & 0 & 0 & 0 \\
    0 & \cos(\theta) & -\sin(\theta) & 0 \\
    0 & \sin(\theta) & \cos(\theta) & 0 \\
    0 & 0 & 0 & 1
\end{bmatrix}
$$
$$
R_y(\theta) = 
\begin{bmatrix}
    \cos(\theta) & 0 & \sin(\theta) & 0 \\
    0 & 1 & 0 & 0 \\
    -\sin(\theta) & 0 & \cos(\theta) & 0 \\
    0 & 0 & 0 & 1
\end{bmatrix}
$$
$$
R_z(\theta) = 
\begin{bmatrix}
    \cos(\theta) & -\sin(\theta) & 0 & 0 \\
    \sin(\theta) & \cos(\theta) & 0 & 0 \\
    0 & 0 & 1 & 0 \\
    0 & 0 & 0 & 1
\end{bmatrix}
$$
Check out Gimbal lock, rotation around arbitrary axis, and quaternions. 
In GLM we can initialize a rotation matrix say about the z axis 
```c++
glm::mat4 rotation = glm::rotate(identity, glm::radians(90.0f), glm::vec3(0.0, 0.0, 1.0));
```
The rotated vector is obtained by
```c++
vec = rotation * vec;
```

## Combining Transformations 
The convention is to first scale, then rotate, and finally translate. 
In terms of matrix multiplication 
$$
\mathrm{Translate}\cdot\mathrm{Rotate}\cdot\mathrm{Scale}
$$
In GLM we can combine matrices sequentially starting from the identity matrix, scaling all components by 1.5, rotating about the z axis by 90 degrees and finally translating by the (2, 3, 0) vector. 
```c++
glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);
glm::mat4 transformation = glm::mat4(1.0f);
transformation = glm::scale(transformation, glm::vec3(1.5, 1.5, 1.5));
transformation = glm::rotate(transformation, glm::radians(90.0f), glm::vec3(0.0, 0.0, 1.0));
transformation = glm::translate(transformation, glm::vec3(2.0f, 3.0f, 0.0f));
vec = transformation * vec;
```


## Resources 
- Learn Open GL ([learnopengl.com](https://learnopengl.com))
- Tutorials for Open GL 3.3 ([opengl-tutorial.org](http://www.opengl-tutorial.org))