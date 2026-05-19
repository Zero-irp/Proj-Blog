---
layout: post
title: "Part 4: Finding and Tracing Projection Construction"
date: 2026-04-04 00:00:00 +0530
permalink: /part-4-finding-projection-matrix/
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


Let's try and find the Projection Matrix in Memory:

In Far cry New dawn the Fov Slider exposes the actual degrees used by the projection Matrix thus making it very easy to find it. 

![ESP-Image1](/Proj-Blog/assets/images/part-4/FOV-slider.png)

Reminder that the xScale is: 

$$
\frac{1}{\tan\left(\frac{FOV_X}{2}\right)}
$$

Now we have access to FovX. I will set the in-game slider to 105 degrees. Next, we convert it to radians and calculate the result:

$$
\frac{1}{\tan{\left(\frac{1.8326}{2}\right)}} = 0.7673
$$

And then we simply search for this value:

![ESP-Image1](/Proj-Blog/assets/images/part-4/CE-scan-105.png)

Now I change the Fov Slider value to "60 degrees", put this through the formula and we get "1.732" and search for it again. Do it a few more times to narrow it down and we get:

![ESP-Image1](/Proj-Blog/assets/images/part-4/CE-scan-60.png)

Now verify all matrices manually till we find a Projection Matrix that is close to the textbook layout. 

This seems to be a Projection Matrix that comes close but still not "Textbook layout":

![ESP-Image1](/Proj-Blog/assets/images/part-4/CE-proj.png)

> As I've said before Game engines need not follow convention

In the first Matrix we can see the expected values in [0][0] (xScale) and [1][1] (yScale) as well as Depth Mapping in [2][2] and [3][2], the unexpected value is 0.13 at [2][1] 
(which is supposed to be 0 in a standard layout). We will figure out what this anomaly is later. The second Matrix is simply the Inverse Projection.

Let's try to find out "what writes to this address" using CE

![ESP-Image1](/Proj-Blog/assets/images/part-4/CE-what-writes.png)

We can see two instructions. Both seem to be from the same function, let's decompile the function in IDA pro:

> Far Cry New Dawn uses Denuvo Anti-Tamper, IDA pro will endlessly loop and will never finish decompiling fully due to the obfuscated nature of the binary. Make sure to turn off 
auto-analysis and do manual analysis!

![ESP-Image1](/Proj-Blog/assets/images/part-4/IDA-decomp.png)

Looks like a mess but to a trained eye we can tell it is doing a Projection Matrix Construction!

### **Why?** ###

Look at these 1/x's and x/2's and various calls to Tanf and aTanf. A clear indicator for Perspective Projection Matrix Construction 

![ESP-Image1](/Proj-Blog/assets/images/part-4/Proj-pin.png)

### **Visual Verification** ###
Before reversing the function, we need to test if this is the real projection matrix used for rendering. I will be changing the FovX value accessed by the function

![ESP-Image1](/Proj-Blog/assets/images/part-4/fcnd-fovX.gif)

Next we will be Reversing this function using IDA pro.

<div class="post-nav">
  <a href="{{ site.baseurl }}/part-3-methods-to-find-proj-mat/">&laquo; Part 3: Methods to find Projection Matrix</a>
  <a href="{{ site.baseurl }}/part-5.1-reversing-projection-matrix/">Part 5.1: Reversing Construction (X & Y) &raquo;</a>
</div>

