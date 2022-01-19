---
layout: post
title: "Math programming - Matrices and layouts"
author: Razakhel
categories: math
tags: math matrices graphics
---

While doing or using a linear algebra library, you may have heard about row-major or column-major matrices, perhaps without knowing what this truly designates. Those layouts, however, can be an endless source of confusion.

## What is "majorness"?

The majorness, being specifically either row-major or column-major, designates the layout of the matrix. I must point out right now that this is only a convention: there is no such thing as a good or bad way to do it; either majorness is perfectly fine to use.

A matrix MxN is defined as M vectors of size N. But those vectors can be either rows or columns:

```
[ x1 y1 z1 ]   [ x1 x2 x3 ]
[ x2 y2 z2 ]   [ y1 y2 y3 ]
[ x3 y3 z3 ]   [ z1 z2 z3 ]
```

$$
M_{row} =
  \left[ {
    \begin{array}{ccc}
      x_{1} & y_{1} & z_{1} \\
      x_{2} & y_{2} & z_{2} \\
      x_{3} & y_{3} & z_{3} \\
    \end{array}
  } \right]

M_{column} =
  \left[ {
    \begin{array}{ccc}
      x_{1} & x_{2} & x_{3} \\
      y_{1} & y_{2} & y_{3} \\
      z_{1} & z_{2} & z_{3} \\
    \end{array}
  } \right]
$$

On the left is a row-major matrix, on the right a column-major one; they are simply the transpose of each other.

TODO

The usual mathematical convention is thus column-major. If you go row-major, you will simply have to swap the operands of all multiplications. For example, the Model-View-Projection (MVP) matrix is usually computed as $P \times V \times M$. In row-major, you would do the opposite: $M \times V \times P$.

## What is the layout of a matrix?

The layout is how a matrix is stored in memory. With a "true" majorness, a row-major matrix is stored row by row, while a column-major is stored column by column. The indices are then as follows, on the left in row-major, on the right in column-major:

```
[ 0 1 2 ]   [ 0 3 6 ]
[ 3 4 5 ]   [ 1 4 7 ]
[ 6 7 8 ]   [ 2 5 8 ]
```

$$
M_{row} =
  \left[ {
    \begin{array}{ccc}
      0 & 1 & 2 \\
      3 & 4 & 5 \\
      6 & 7 & 8 \\
    \end{array}
  } \right]
$$

$$
  M_{column} =
    \left[ {
      \begin{array}{ccc}
        0 & 3 & 6 \\
        1 & 4 & 7 \\
        2 & 5 & 8 \\
      \end{array}
    } \right]
$$

## Using graphics APIs

Contrary to what some parts of the documentation imply[^gl-doc-majorness], OpenGL actually **does not care about the majorness**. It does not even care about the memory layout: the user is the sole responsible for how the data should be used. However, to use matrices the usual way, it does expect that each vector composing the matrix are one after the other. Or in other words, for a 4x4 matrix, that the translation is located at the 13th, 14th & 15th values. This can be the case with either row-major or column-major:

$$
M_{row} =
  \left[ {
    \begin{array}{cccc}
      M_{11} & M_{12} & M_{13} & 0 \\
      M_{21} & M_{22} & M_{23} & 0 \\
      M_{31} & M_{32} & M_{33} & 0 \\
      \color{red}{T_{x}} & \color{green}{T_{y}} & \color{blue}{T_{z}} & 1 \\
    \end{array}
  } \right]
$$

$$
M_{row} =
  \left[ {
    \begin{array}{cccc}
      \color{red}{M_{x_{1}}} & \color{green}{M_{y_{1}}} & \color{blue}{M_{z_{1}}} & 0 \\
      \color{red}{M_{x_{2}}} & \color{green}{M_{y_{2}}} & \color{blue}{M_{z_{2}}} & 0 \\
      \color{red}{M_{x_{3}}} & \color{green}{M_{y_{3}}} & \color{blue}{M_{z_{3}}} & 0 \\
      \color{red}{T_{x}} & \color{green}{T_{y}} & \color{blue}{T_{z}} & 1 \\
    \end{array}
  } \right]
$$

$$
M_{column} = 
  \left[ {
    \begin{array}{cccc}
      M_{11} & M_{12} & M_{13} & \color{red}{T_{x}} \\
      M_{21} & M_{22} & M_{23} & \color{green}{T_{y}} \\
      M_{31} & M_{32} & M_{33} & \color{blue}{T_{z}} \\
      0 & 0 & 0 & 1 \\
    \end{array}
  } \right]
$$

$$
M_{column} =
  \left[ {
    \begin{array}{cccc}
      \color{red}{M_{x_{1}}} & \color{red}{M_{x_{2}}} & \color{red}{M_{x_{3}}} & \color{red}{T_{x}} \\
      \color{green}{M_{y_{1}}} & \color{green}{M_{y_{2}}} & \color{green}{M_{y_{3}}} & \color{green}{T_{y}} \\
      \color{blue}{M_{z_{1}}} & \color{blue}{M_{z_{2}}} & \color{blue}{M_{z_{3}}} & \color{blue}{T_{z}} \\
      0 & 0 & 0 & 1 \\
    \end{array}
  } \right]
$$

Looking back at the previous section on matrix layout, we can clearly see that the vectors' data is organized vector by vector in either case:

$$
[ \color{red}{x_{1}}, \color{green}{y_{1}}, \color{blue}{z_{1}}, \color{red}{x_{2}}, \color{green}{y_{2}}, \color{blue}{z_{2}}, \color{red}{x_{3}}, \color{green}{y_{3}}, \color{blue}{z_{3}} ]
$$

One can attest that this has _nothing_ to do with majorness: the contiguous data is **exactly the same**, whether in row- or column-major. What OpenGL does next is create a matrix column by column from these values, which results in the exact same matrix regardless of the majorness.

The only situation where it _does_ matter, however, is when you interleave the data, like having a "row-organized" matrix with a column memory layout, or the opposite. The data is then organized as follows:

$$
[ \color{red}{x_{1}}, \color{red}{x_{2}}, \color{red}{x_{3}}, \color{green}{y_{1}}, \color{green}{y_{2}}, \color{green}{y_{3}}, \color{blue}{z_{1}}, \color{blue}{z_{2}}, \color{blue}{z_{3}} ]
$$

In this case, you can either:
- Reverse all multiplications. For example, to compute the vertex position, $P \times V \times M \times vertexPos$ would become $vertexPos \times M \times V \times P$;
- Transpose the matrix yourself on CPU, then send its data;
- Pass `GL_TRUE` as the [transpose argument](https://www.khronos.org/registry/OpenGL-Refpages/gl4/html/glUniform.xhtml#parameters) of `glUniformNfv()`;
- Indicate `layout(row_major)`[^glsl-matrices-layout-majorness] to the matrices or blocks you want to deinterleave.

### 



## What to do then?

Until now, I personally have always used a row-major layout; not only because it made much more sense to me than a column one, but also because it is much easier to think about in common languages. However, it is unconventional compared to usual mathematical notations.

For that reason alone, I would recommend going for a column-major layout. It will be much, _much_ simpler following articles, tutorials or general explanations, if you do not have to think about reversing calculations or transposing matrices every single time[^matrices-reverse-quaternions].

That being said, I must once again emphasize that this is simply a matter of convention. There is no inherent problem going for one layout or another, as long as you understand what to do, which I hope you do now!

---

[^gl-doc-majorness]: [`If transpose is GL_FALSE, each matrix is assumed to be supplied in column major order. If transpose is GL_TRUE, each matrix is assumed to be supplied in row major order.`](https://www.khronos.org/registry/OpenGL-Refpages/gl4/html/glUniform.xhtml#description)

[^glsl-matrices-layout-majorness]: [https://www.khronos.org/opengl/wiki/Interface_Block_(GLSL)#Matrix_storage_order](https://www.khronos.org/opengl/wiki/Interface_Block_(GLSL)#Matrix_storage_order)

[^matrices-reverse-quaternions]: Not only that, quaternion operations probably need to be reversed as well. Else `mat * vec` would give a vector opposite to `quat * vec` would.
