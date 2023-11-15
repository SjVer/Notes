**Length:**
$$||\vec{v}|| = \sqrt{x^2 + y^2}$$

**Unit vector:**
$$\hat{n} = \frac{\vec{v}}{||\vec{v}||}$$
**Dot product:**
$$\begin{pmatrix} x_1 \\ y_1 \end{pmatrix} \cdot \begin{pmatrix} x_2 \\ y_2 \end{pmatrix} = x_1 \cdot x_2 + y_1 \cdot y_2$$

**Cross product:**
$$\begin{pmatrix} a_x \\ a_y \\ a_z \end{pmatrix} \times \begin{pmatrix} b_x \\ b_y \\ b_z \end{pmatrix} = \begin{pmatrix} a_y \cdot b_z - a_z \cdot b_y \\ a_z \cdot b_x - a_x \cdot b_z \\ a_x \cdot b_y - a_y \cdot b_x \end{pmatrix}$$

**Matrix multiplication:**
For matrix multiplication, the number of columns in the first matrix must be equal to the number of rows in the second matrix. The resulting matrix has the number of rows of the first and the number of columns of the second matrix.[^wiki] Also note: $A \cdot B \neq B \cdot A$.
Treating the rows of the first matrix and the columns of the second matrix as vectors, the resulting matrix is the **dot product** of the corresponding vectors:
![|300](assets/matrix%20multiplication.png)
## Transformations
For an actual explanation see the videos on Khan Academy.[^khan]

**Scaling:**
Keep in mind that a vector is basically a matrix with just one column, so this is just matrix multiplication. 
(The extra $w$ component is there for vector translation.)
$$\begin{bmatrix} s_x & 0 & 0 & 0 \\ 0 & s_y & 0 & 0 \\ 0 & 0 & s_z & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix} = \begin{pmatrix} s_x \cdot x \\ s_y \cdot y \\ s_z \cdot z \\ 1 \end{pmatrix}$$
(E.g. the first row is $s_x \cdot x + 0 \cdot y + 0 \cdot z + 0 \cdot 1 = s_x \cdot x$.)

**Translation:**
Again, this is just matrix multiplication. 
(The extra $w$ component exists and is $1$ so that the $t$s are multiplied by $1$ and not $0$.)
$$\begin{bmatrix} 1 & 0 & 0 & t_x \\ 0 & 1 & 0 & t_y \\ 0 & 0 & 1 & t_z \\ 0 & 0 & 0 & 1 \end{bmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix} = \begin{pmatrix} x + t_x \\ y + t_y \\ z + t_z \\ 1 \end{pmatrix}$$
(E.g. the first row is $1 \cdot x + 0 \cdot y + 0 \cdot z + t_x \cdot 1 = x + t_x$.)

NOTE: The $w$ component is $1$ if the vector represents a point, and it is $0$ if it represents a direction. This way, translation only affects points.

**Rotation**:
Rotations in 3D are specified with an angle **and** a rotation axis.[^rotations]
The rotation matrix for rotating a 2D vector is simple:[^2d]
$$\begin{bmatrix} cos \space \theta & -sin \space \theta \\ sin \space \theta & cos \space \theta \end{bmatrix} \cdot \begin{pmatrix} x \\ y \end{pmatrix} = \begin{pmatrix} x' \\ y' \end{pmatrix}$$
Expanding this to 3 dimensions and around the 3 axes we get the following matrices:
Around the X-axis:
$$\begin{bmatrix} 1 & 0 & 0 & 0 \\ 0 & {\cos \theta} & - {\sin \theta} & 0 \\ 0 & {\sin \theta} & {\cos \theta} & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix} = \begin{pmatrix} x \\ {\cos \theta} \cdot y - {\sin \theta} \cdot z \\ {\sin \theta} \cdot y + {\cos \theta} \cdot z \\ 1 \end{pmatrix}$$
Around the Y-axis:
$$\begin{bmatrix} {\cos \theta} & 0 & {\sin \theta} & 0 \\ 0 & 1 & 0 & 0 \\ - {\sin \theta} & 0 & {\cos \theta} & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix} = \begin{pmatrix} {\cos \theta} \cdot x + {\sin \theta} \cdot z \\ y \\ - {\sin \theta} \cdot x + {\cos \theta} \cdot z \\ 1 \end{pmatrix}$$
Around the Z-axis:
$$\begin{bmatrix} {\cos \theta} & - {\sin \theta} & 0 & 0 \\ {\sin \theta} & {\cos \theta} & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix} \cdot \begin{pmatrix} x \\ y \\ z \\ 1 \end{pmatrix} = \begin{pmatrix} {\cos \theta} \cdot x - {\sin \theta} \cdot y  \\ {\sin \theta} \cdot x + {\cos \theta} \cdot y \\ z \\ 1 \end{pmatrix}$$
Rotating around those axes one by one is possible, but not a good idea. 
Instead, there is one big generalized rotation matrix around the rotation axis $\vec{r}$:
$$\begin{bmatrix} \cos \theta + {r_x}^2(1 - \cos \theta) & {r_x}{r_y}(1 - \cos \theta) - {r_z} \sin \theta & {r_x}{r_z}(1 - \cos \theta) + {r_y} \sin \theta & 0 \\ {r_y}{r_x} (1 - \cos \theta) + {r_z} \sin \theta & \cos \theta + {r_y}^2(1 - \cos \theta) & {r_y}{r_z}(1 - \cos \theta) - {r_x} \sin \theta & 0 \\ {r_z}{r_x}(1 - \cos \theta) - {r_y} \sin \theta & {r_z}{r_y}(1 - \cos \theta) + {r_x} \sin \theta & \cos \theta + {r_z}^2(1 - \cos \theta) & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix}$$
Even better would be to use quaternions, but that's a whole other topic.[^quat]

### Combination
Combining transformations is as simple as multiplying them **in the correct order**. It is advised to first do scaling operations, then rotations and lastly translations. When using row vectors instead of column vectors this order is flipped. This final matrix is often called the _TRS_ (since row vectors are more common).

[^wiki]: Wikipedia article on [matrix multiplication](https://en.wikipedia.org/wiki/Matrix_multiplication)
[^khan]: Khan Academy on [matrix transformations](https://www.khanacademy.org/math/linear-algebra/matrix-transformations)
[^rotations]: Learn OpenGL section on [rotation](https://learnopengl.com/Getting-started/Transformations#:~:text=our%20transformation%20toolkit.-,Rotation,-The%20last%20few)
[^2d]: Video on [deriving a rotation matrix](https://www.youtube.com/watch?v=EZufiIwwqFA)
[^quat]: Website on maths such as [quaternions](https://euclideanspace.com)