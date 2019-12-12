<style>
.katex { font-size: 1.5em !important; }
</style>

# Introduction to 3D vision

## Applications

### 3D reconstruction

It's by far the most common application of 3D vision. It's goal is to recreate 3D model based on a set of photos.

The high-level pipeline of 3D reconstruction looks like this:

1) Image acquisition
2) Camera calibration
3) Actual 3D reconstruction
4) Texture Mapping

Sometimes we can skip certain steps, for instance cameras may be already calibrated when shooting happend in a 3D vision laboratory, or we might not need textures on our model so we can just discard point 4.

> **Camera calibration** is the process of estimating **intrinsic** and/or **extrinsic** parameters. Intrinsic parameters deal with the camera's internal characteristics, such as, its focal length, skew, distortion, and image center. Extrinsic parameters describe its position and orientation in the world

The pre-calibrated case is also referred to as *Multi-view stereo* or *Shape from X*.

In the non-calibrated case we have two standard problems:
  - [Structure from motion](https://www.youtube.com/watch?v=i7ierVkXYa8) - reconstructing an object from photos
  - [Simultaneous Localization and Mapping](https://www.youtube.com/watch?v=oJt3Ln8H03s) - constructing a map of an unknown environment while simultaneously keeping track of camera location within it

We (humaity) have a crazy amount of cameras and the images are all connected in the Internet, so cool things are now possible. For instance:

[Building Rome in a Day](https://grail.cs.washington.edu/rome/) project - a reconstruction of Rome based on publicaly available pictures made by turists.

The project makes a **sparse** reconstruction - it outputs a set of points. But upon this we can perform a **dense reconstruction** that uses meshes not points.

### Others

* Motion capture:
  - medical, sports, gaming, movies
  - we dont have all the points
  - use of many (32) cameras

* Facial expressions

* Bullet time effect:
  - introduced by movie industry
  - change a point of view while some (fast) events happen (shooting bullet)

* Augmented reality
  - introduce virtual object to real-time footage
  - no 3d reconstruction
  - have to place it right and rotate while the camera is moving


* Match moving:
  - placing virtual 3d object on a 2d scene
  - for instance a 3d spaceship model on a highway scene of some movie

## 3D model representation

We use various rapresentations:

* Point Cloud - a set of 3D points
* (Optionally colored) mesh - a set of (textured) meshes
* Voxels - 3D pixels
* Depth map - grayscale image where brighter is closer

## Math

3D vision largely benefits from:

* Linear algebra
* Projective geometry
* Optimization
* Deep learning

# Projective geometry

## 2D case

Pictures are 2D and they usually represent a 3D scene. That's a projection of a 3D space to a 2D plane.

![Projection schema with World, Image and Camera coordinate systems](assets/2dprojection.png)

The switching between world cooridinate system to image system is also called **projective transformation**.

During the transformation:
- **Angles** are not preserved, and thus are shapes
- **Lengths** are not preserved (no distance ratio)
- **Colinearity** is preserved (lines in 3D are still lines on projection)
- **Cross ratio** is preserved.

Given four points $A,B,C,D$ on placed in this order on a  line their cross ratio is defined as $\varkappa(A,B,C,D) = \frac{AC * BD}{AD * BC} = \frac{AC/CB}{BD/AD}$

This enables us to compute real world distance:

![Computations using cross ratio](assets/cratiouse.png)

In (1) we use the information that $A'B' = 7m$, $D'C' = 6m$, in (2) we use one of these and the vanishing point 
<!-- TODO why can we write (2) equation? What does vanishing point change? -->

**The vanishing point** is the perspective center, the projection of infinity point on lines.

<!--
TODO explain:

Projective geometry
> It allows to remove the projective distortion of flat objects and build image mosaics
-->

**Conic sections** are curves formed by the intersection of a cone with planes at different angles. There are four types of conics:

![Conic sections](assets/conicsections.png)

### The basics

<!-- TODO what is projective space? -->

$P^2$ : projective space of dimension 2 (the projective plane)


We will see how these:
* point
* line
* conic

are represented in the projective space.

# Notes in progress - warning

> Beyond this point I haven't yet restructured the notes, they may contain errors and they are just a set of loose sentences written during the lecture.

#### Point representation

A point in the image is a ray in the projective space.
$(x,y,1)$ image plae
$(x,y)$ is mapped to a **ray** $(sx, sy, s)$ (k=aka. visual ray, visual direction or view direction).



All points on the ray are equivalent 


given $x, x' \in P^2$ we define the equivalence relationship if $x ~ x'$ (they are proportional).

That means we represent a point of the plane $R^2$ by a vector in $R^3 \setminus{(0,0,0)}$

How de we transform from cartesian to homogenous coord. ($R^2 \rightarrow P^2$)

$v = (x,y)^T \rightarrow p(v) = (x,y,1)^T$
We can now multiply $p(v)$ by a scalar $s \neq 0 \in \reals$


$P^2 \rightarrow R^2$:

$p(v) = (sx, sy, s)^T \rightarrow v = (x,y)^T$

it doesnt work for $s=0$. That's because infintty is nor representable in $R^2$

#### Line repr

TODO: Point on the planes with scalar product

$l=(a,b,c)^T \in \reals^3 \setminus \{(0,0,0)\}$

Equivalent reprs:

$l ~ l'$ if $\exist_{k \neq 0}: l' = kl$

Given $l=(a,b,c)^T$ the point in infinity is $p(v)=(-b,a,0)^T$
We can parametrize the points on the line: line is always 1D: $ax + by + c = 0 \rightarrow x = \frac{-by}{a} - \frac{c}a$

and so a line is $\{(-bt/a -c/a,t,1)\}_{t\in \reals}$

which is equivalent to $\{(-b/a -c/at,1,1/t)\}_{t\in \reals}$
and as $t$ goes to inifnity that becomes:

$(-b/a), 1, 0) \in P^2$. This is a point at infty of line $l$ (ideal point).

To compute the intersection of two lines $l, l'$ we compute their **cross product**.

*The duality of points and lines*:

$\vec{l} = \vec{x} \times \vec{x'}$ line joining two points

$\vec{x} = \vec{l} \times \vec{l'}$ the intersection of two lines


The line at infinity $l_{\infty} = (0,0,1)^T$: used for image rectification (removal of projective distortion)

### Conics

They are usefull in calibration

equation:
$ax^2 + bxy + cy^2 + dx + ey + f = 0$

C = (a,b/2,d/2), (b/2,c,e/2), (d/2,e/2,f)

$<Cx,x> = 0 \iff x^tCx = 0$ means $x$ belongs to $C$

equivalence 

$C ~ C'$ if $\exist_{k \neq 0}: C' = kC$

### Dual conics

$C*$ defines the conic envelope - equation satisfied by all lines that are TODO styczne with the ellipse

Applications
- rectification
- Measuring real angles
- camera calibration
- auto calibration

TODO absolute conic


## 3D case

> It allows for 3D reconstruction, the calibration and 

