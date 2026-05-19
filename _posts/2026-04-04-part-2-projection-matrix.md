---
layout: post
title: "Part 2: What is the Projection Matrix?"
date: 2026-04-04 00:00:00 +0530
permalink: /part-2-projection-matrix/
---

<style>
.post-nav {
  display: flex;
  justify-content: space-between;
  margin-top: 40px;
  padding-top: 20px;
  border-top: 1px solid #444;
  font-family: Consolas, "Liberation Mono", Menlo, monospace;
  font-size: 15px;
}
.post-nav a {
  color: #569cd6;
  text-decoration: none;
  padding: 10px 16px;
  background: #1e1e1e;
  border-radius: 6px;
  transition: background 0.2s ease;
}
.post-nav a:hover {
  background: #2d2d2d;
}
</style>

I previously touched on this here: [Reversing The ViewProjection Matrix (Part 2.2)](https://zero-irp.github.io/ViewProj-Blog/part-2.2-projection-matrix/), but we need to establish a 
baseline before we start tearing apart engine binaries.

I won't be giving a deep-dive computer graphics lecture here (Frustum plane derivations, complex perspective math, etc.). Instead, we will learn how textbooks do the math vs. how real Game Engines do the math.

### **The Bare Minimum: What is it?**

If the **View Matrix** acts as the camera's position and rotation in the world, the **Projection Matrix** is the camera's lens. It compresses the 3D world flat onto your 2D monitor. 

It does this by defining a **View Frustum** (a 3D truncated pyramid of space) using four variables:
*   **Field of View (FOV):** How wide the lens is.
*   **Aspect Ratio:** Your monitor's dimensions (16:9, 21:9).
*   **Near & Far Planes:** The minimum and maximum depth the camera can see.

Anything inside this pyramid gets rendered to your screen. Anything outside gets clipped. That is all the theory you need for now.

![ESP-Image1](/Proj-Blog/assets/images/part-2/frustum.png)

### **The Memory Layout (What We Actually Care About)**

As reverse engineers, understanding the theory is secondary to understanding the *memory layout*. When you are scanning memory in Cheat Engine, a projection matrix just looks like a 
contiguous block of 16 floating-point numbers. 

To spot it in the sea of data, you have to know what those 16 floats represent. Here is the standard perspective projection matrix layout (DirectX):

$$
P =
\begin{bmatrix}
x_{scale} & 0 & 0 & 0 \\
0 & y_{scale} & 0 & 0 \\
0 & 0 & \dfrac{z_{far}}{z_{far} - z_{near}} & 1 \\
0 & 0 & -\dfrac{z_{near} \cdot z_{far}}{z_{far} - z_{near}} & 0
\end{bmatrix}
$$

The formulas used for the `zNear` and `zFar` depth mapping (Rows 2 and 3) are not universal and can differ engine to engine, convention to convention. What actually matters is that 
after projection and perspective divide (in DirectX convention), the near plane maps to 0 and the far plane maps to 1 (or the opposite in Reversed-Z.)

However, the **X and Y scales** (the floats at `[0][0]` and `[1][1]`) are almost always derived directly from the Field of View using tangent math:

$$
x_{scale} = \frac{1}{\tan{\left(\frac{Fov_X}{2}\right)}}
$$

$$
y_{scale} = \frac{1}{\tan{\left(\frac{Fov_Y}{2}\right)}}
$$

**This is our golden ticket.** If we know the game's FOV, we can calculate the exact floating-point value for `xScale`, and use Cheat Engine to hunt down that exact float in memory. 

>A Projection Matrix standing out in a sea of floats.  
>
>![ESP-Image1](/Proj-Blog/assets/images/part-2/proj-find.png)

Let's look at the exact methods we use to hunt this matrix down in Part 3.

<div class="post-nav">
  <a href="{{ site.baseurl }}/part-1-intro/">&laquo; Part 1: Intro</a>
  <a href="{{ site.baseurl }}/part-3-methods-to-find-proj-mat/">Part 3: Methods to find Projection Matrix &raquo;</a>
</div>

