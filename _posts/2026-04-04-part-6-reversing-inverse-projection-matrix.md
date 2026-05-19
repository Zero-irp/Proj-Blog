---
layout: post
title: "Part 6: Reversing Construction of the Inverse Projection Matrix"
date: 2026-04-04 00:00:00 +0530
permalink: /part-6-reversing-inverse-projection-matrix/
---

<style>
.ida-code{
  background:#1e1e1e;
  color:#dcdcdc;
  padding:12px;
  border-radius:8px;
  font-family: Consolas, "Liberation Mono", Menlo, monospace;
  font-size:14px;
  line-height:1.4;
  overflow-x:auto;
  white-space: pre; /* preserve spaces and linebreaks */
}

/* token classes you can use inside the div */
.ida-code .kw    { color:#569cd6; } /* keywords */
.ida-code .type  { color:#4ec9b0; } /* types */
.ida-code .fn    { color:#dcdcaa; } /* functions / intrinsics */
.ida-code .num   { color:#b5cea8; } /* numbers / hex */
.ida-code .var   { color:#9cdcfe; } /* variables */
.ida-code .const { color:#ce9178; } /* globals / constants */
.ida-code .comment{ color:#6a9955; font-style:italic; }
</style>

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

Now for the final matrix getting constructed by the else block!

I have isolated all lines relating to the construction of the Inverse Projection Matrix:

![ESP-Image1](/Proj-Blog/assets/images/part-6/ida-inv-proj.png)


Let's start with row 0.

### **Row 0:** ###

<div class="ida-code">*(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x130</span>) = <span class="fn">_mm_unpacklo_ps</span>(<span class="fn">_mm_unpacklo_ps</span>(<span class="var">fovX_val</span>, (<span class="type">__m128</span>)<span class="num">0LL</span>), (<span class="type">__m128</span>)<span class="num">0LL</span>);
</div>

Where the last value for fovX_val was:

<div class="ida-code"><span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// fovX_val = FovX / 2</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">19</span>    <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">0.5</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Arg: fovX_val. Result: fovX_val = tan(FovX / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">20</span>    <span class="fn">ucrtBase_Tanf</span>(); 
</div>

so Row 0 = [tan(fovX/2), 0, 0, 0]

### **Row 1:** ###

<div class="ida-code">*(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x140</span>) = <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="fn">_mm_unpacklo_ps</span>(<span class="var">fovY_val</span>, (<span class="type">__m128</span>)<span class="num">0LL</span>));
</div>

Where the last value for fovY_val was:

<div class="ida-code"><span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// fovY_val = FovY / 2</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">22</span>    <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">0.5</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Arg: fovY_val. Result: fovY_val = tan(FovY / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">25</span>    <span class="fn">ucrtBase_Tanf</span>(); 
</div>

so Row 1 = [0, tan(fovY/2), 0, 0]

### **Row 2:** ###

<div class="ida-code">*(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x150</span>) = <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="var">v45</span>));
</div>

"v45" comes from:

<div class="ida-code"><span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  <span class="comment">// Calculate zNear - zFar</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">33</span>  <span class="var">v45</span> = <span class="var">zNear</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">34</span>  <span class="var">v45</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] - <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  <span class="comment">// Modifying zFar in place: zFar = zFar * zNear</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">42</span>  <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  <span class="comment">// Used for inverse Projection</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">45</span>  <span class="var">v45</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">v45</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] / <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>]; 
</div>

I don't feel like this needs explaining, all very self explanatory:

$$
\large v_{45} = \frac{z_{near} - z_{far}}{z_{far} \cdot z_{near}}
$$

Final Unpack: [0, 0, 0, (near - far) / (far * near)]

### **Row 3:** ###

<div class="ida-code">*(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x160</span>) = <span class="fn">_mm_unpacklo_ps</span>(
                            <span class="fn">_mm_unpacklo_ps</span>(<span class="var">tan_fovX_half_save</span>, (<span class="type">__m128</span>)<span class="num">0xBF800000</span>),
                            <span class="fn">_mm_unpacklo_ps</span>(<span class="var">tan_fovY_half_save</span>, <span class="var">v50</span>));
</div>

We already know "tan_fovX_half_save" is tan(fovX/2) which just before get's multiplied with "0" while "tan_fovY_half_save" which previously had the value of "tan(fovY/2)" is multiplied 
with our anomaly "0.1299999952".  

These calculations are done in these lines:

<div class="ida-code"><span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  <span class="comment">// Apply asymmetric offset (0.13) to tan(FovY/2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">49</span>  <span class="var">tan_fovY_half_save</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * *(<span class="type">float</span> *)(<span class="var">a1</span> + <span class="num">0x42C</span>); <span class="comment">// 0.1299999952</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  <span class="comment">// Apply X-offset (0.0) to tan(FovX/2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">50</span>  <span class="var">tan_fovX_half_save</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * *(<span class="type">float</span> *)(<span class="var">a1</span> + <span class="num">0x428</span>); <span class="comment">// 0.0</span>
</div>

Next, "v50" is simply:

<div class="ida-code"><span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>  <span class="comment">// Calculate 1.0 / zNear</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">51</span>  <span class="var">v50</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="num">1.0</span> / <span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
</div>

and "0xBF800000" is "-1"

Final Unpack: [0, tan(fovY/2)*0.13, -1, 1/near]

### **Final Layout:** ###

$$
\begin{bmatrix}
\tan(fovX/2) & 0 & 0 & 0 \\
0 & \tan(fovY/2) & 0 & 0 \\
0 & 0 & 0 & \frac{near - far}{far \cdot near} \\
0 & \tan(fovY/2) \cdot 0.13 & -1 & \frac{1}{near}
\end{bmatrix}
$$

This matches what we have seen previously in cheat engine's memory viewer!

![ESP-Image1](/Proj-Blog/assets/images/part-4/CE-proj.png)

### **Reversing Insight** ###

The engine is doing something incredibly smart here. Instead of relying on a generic 4x4 matrix inversion algorithm (like Cramer's rule) which requires a heavy, separate function call 
and eats up valuable CPU cycles. It performs a heavily optimized algebraic "fast inverse" inline. Because a projection matrix has so many known zeroes, the developers hardcoded the exact 
algebraic inverse right into the function.

>Bit of a side track - Initially, when staring at this block, I didn't even realize I was looking at an Inverse Projection Matrix. In hindsight it's very obviously an inverse projection. 
>We unconsciously look for standard math library calls (like a `MatrixInverse`function), so when it's just raw, inline floating-point math, it's easy to miss.
>
>So how do you prove a hunch when the code is ambiguous?
>
>The Scientific Method of Hypothesis ➔ Observation ➔ Conclusion!
>
>*   **Hypothesis:** This weird block of inline math is manually constructing the Inverse Projection Matrix.
>*   **Observation:** I dumped the originally constructed Projection Matrix directly from memory and ran it through a standard matrix inversion script on my own. I then compared my calculated output against the values the engine was generating in this second matrix.
>*   **Conclusion:** The floats lined up exactly. Hypothesis confirmed! we have found the Inverse Projection.

### **The Full Picture:** ###

<div class="ida-code"><span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 1</span>  <span class="kw">else</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 2</span>  {
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 1. Initial Setup &amp; FovX ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Load FovX (radians) from a1 + 0x234</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 3</span>    <span class="var">fovX_calc</span> = (<span class="type">__m128</span>)*(<span class="type">unsigned int</span> *)(<span class="var">a1</span> + <span class="num">0x234</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 4</span>    <span class="var">fovX_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">0.5</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Arg: fovX_calc. Result: fovX_calc = tan(FovX / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 5</span>    <span class="fn">ucrtBase_Tanf</span>(); 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 6</span>    <span class="var">fovY_calc</span> = <span class="var">fovX_calc</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 7</span>    <span class="var">tanFovXby2_dup</span> = <span class="var">fovX_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Divide by Aspect Ratio at a1 + 0x18 (hardcoded at 1.777)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// fovY_calc = tan(FovX / 2) / AspectRatio</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 8</span>    <span class="var">fovY_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] / *(<span class="type">float</span> *)(<span class="var">a1</span> + <span class="num">0x18</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 2. The Redundant Call ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Arg: fovX_calc. Result: atan(tan(FovX / 2)). Reverts back to FovX / 2</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;"> 9</span>    <span class="fn">ucrtBase_aTanf</span>(); 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 3. FovY Calculation ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Check if FovY is pre-calculated (usually 0)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">10</span>    <span class="var">fovY_val</span> = (<span class="type">__m128</span>)*(<span class="type">unsigned int</span> *)(<span class="var">a1</span> + <span class="num">0x430</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">11</span>    <span class="var">fovX_val</span> = <span class="var">fovX_calc</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// fovX_val = (FovX / 2) * 2.0 -&gt; FovX</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">12</span>    <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">2.0</span>; 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">13</span>    <span class="kw">if</span> ( <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] == <span class="num">0.0</span> )
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">14</span>    {
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>      <span class="comment">// Arg: fovY_calc. Result: atan(tan(FovX / 2) / AspectRatio)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">15</span>      <span class="fn">ucrtBase_aTanf</span>(); 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">16</span>      <span class="var">fovY_val</span> = <span class="var">fovY_calc</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>      
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>      <span class="comment">// fovY_val = 2 * atan(tan(FovX / 2) / AspectRatio)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">17</span>      <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovY_calc</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">2.0</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">18</span>    }
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// fovX_val = FovX / 2</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">19</span>    <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">0.5</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Arg: fovX_val. Result: fovX_val = tan(FovX / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">20</span>    <span class="fn">ucrtBase_Tanf</span>(); 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">21</span>    <span class="var">xScale</span> = (<span class="type">__m128</span>)<span class="num">0x3F800000u</span>; <span class="comment">// 1.0f</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// fovY_val = FovY / 2</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">22</span>    <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="num">0.5</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Save tan(FovX / 2) for later calculations</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">23</span>    <span class="var">tan_fovX_half_saved</span> = <span class="var">fovX_val</span>; 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 4. Final xScale Calculation ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// xScale = 1.0 / tan(FovX / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">24</span>    <span class="var">xScale</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="num">1.0</span> / <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Arg: fovY_val. Result: fovY_val = tan(FovY / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">25</span>    <span class="fn">ucrtBase_Tanf</span>(); 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">26</span>    <span class="var">v39</span> = (<span class="type">__m128</span>)*(<span class="type">unsigned int</span> *)(<span class="var">a1</span> + <span class="num">0x428</span>); <span class="comment">// Load 0.0f</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">27</span>    <span class="var">v40</span> = (<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0xF0</span>); <span class="comment">// Pointer to Row 0</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">28</span>    <span class="var">v41</span> = <span class="var">a1</span> + <span class="num">0x130</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Save tan(FovY / 2) for later calculations</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">29</span>    <span class="var">tan_fovY_half_save</span> = <span class="var">fovY_val</span>; 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">30</span>    <span class="var">v43</span> = (<span class="type">__m128</span>)<span class="num">0x3F800000u</span>; <span class="comment">// 1.0f</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">31</span>    <span class="var">yScale</span> = (<span class="type">__m128</span>)<span class="num">0x3F800000u</span>; <span class="comment">// 1.0f</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 5. Final yScale Calculation ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// yScale = 1.0 / tan(FovY / 2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">32</span>    <span class="var">yScale</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="num">1.0</span> / <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 6. Depth Mapping Construction (Projection Matrix) ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Calculate zNear - zFar</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">33</span>    <span class="var">v45</span> = <span class="var">zNear</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">34</span>    <span class="var">v45</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] - <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Load the asymmetric frustum offset (0.13)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">35</span>    <span class="var">anomaly_offset</span> = (<span class="type">__m128</span>)*(<span class="type">unsigned int</span> *)(<span class="var">a1</span> + <span class="num">0x42C</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Pack and store Row 1: [0, yScale, 0, 0]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">36</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x100</span>) = <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="fn">_mm_unpacklo_ps</span>(<span class="var">yScale</span>, (<span class="type">__m128</span>)<span class="num">0LL</span>));
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// v43 = 1.0 / (zNear - zFar)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">37</span>    <span class="var">v43</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="num">1.0</span> / (<span class="type">float</span>)(<span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] - <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>]);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Pack and store Row 0: [xScale, 0, 0, 0]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">38</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0xF0</span>) = <span class="fn">_mm_unpacklo_ps</span>(<span class="fn">_mm_unpacklo_ps</span>(<span class="var">xScale</span>, (<span class="type">__m128</span>)<span class="num">0LL</span>), (<span class="type">__m128</span>)<span class="num">0LL</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">39</span>    <span class="var">zFarByzNearNegzFar</span> = <span class="var">v43</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// zFarByzNearNegzFar = (1.0 / (zNear - zFar) * zFar)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">40</span>    <span class="var">zFarByzNearNegzFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">v43</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Pack and store Row 2: [0, 0.13, zFar / (zNear - zFar), -1.0]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Note: 0xBF800000 is -1.0f, v39 is always 0</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">41</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x110</span>) = <span class="fn">_mm_unpacklo_ps</span>(<span class="fn">_mm_unpacklo_ps</span>(<span class="var">v39</span>, <span class="var">zFarByzNearNegzFar</span>), <span class="fn">_mm_unpacklo_ps</span>(<span class="var">anomaly_offset</span>, (<span class="type">__m128</span>)<span class="num">0xBF800000</span>));
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Modifying zFar in place: zFar = zFar * zNear</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">42</span>    <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">43</span>    <span class="var">v48</span> = <span class="var">zFar</span>; 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// v48 = (zFar * zNear) * (1.0 / (zNear - zFar))</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">44</span>    <span class="var">v48</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * <span class="var">v43</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Used later for inverse Projection calculation</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">45</span>    <span class="var">v45</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">v45</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] / <span class="var">zFar</span>.<span class="var">m128_f32</span>[<span class="num">0</span>]; 
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Pack Row 3: [0, 0, (zFar * zNear) / (zNear - zFar), 0]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">46</span>    <span class="var">row3_packed</span> = <span class="fn">_mm_unpacklo_ps</span>(<span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="var">v48</span>), (<span class="type">__m128</span>)<span class="num">0LL</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">47</span>    <span class="var">v50</span> = (<span class="type">__m128</span>)<span class="num">0x3F800000u</span>; <span class="comment">// 1.0f </span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Store Row 3</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">48</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x120</span>) = <span class="var">row3_packed</span>;
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// --- 7. Inverse Projection Matrix Construction ---</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Apply asymmetric offset (0.13) to tan(FovY/2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">49</span>    <span class="var">tan_fovY_half_save</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovY_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * *(<span class="type">float</span> *)(<span class="var">a1</span> + <span class="num">0x42C</span>); <span class="comment">// 0.1299999952</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Apply X-offset (0.0) to tan(FovX/2)</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">50</span>    <span class="var">tan_fovX_half_save</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="var">fovX_val</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] * *(<span class="type">float</span> *)(<span class="var">a1</span> + <span class="num">0x428</span>); <span class="comment">// 0.0</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Calculate 1.0 / zNear</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">51</span>    <span class="var">v50</span>.<span class="var">m128_f32</span>[<span class="num">0</span>] = <span class="num">1.0</span> / <span class="var">zNear</span>.<span class="var">m128_f32</span>[<span class="num">0</span>];
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Store Inverse Matrix Row 0: [tan(FovX/2), 0, 0, 0]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">52</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x130</span>) = <span class="fn">_mm_unpacklo_ps</span>(<span class="fn">_mm_unpacklo_ps</span>(<span class="var">fovX_val</span>, (<span class="type">__m128</span>)<span class="num">0LL</span>), (<span class="type">__m128</span>)<span class="num">0LL</span>);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Store Inverse Matrix Row 1: [0, tan(FovY/2), 0, 0]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">53</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x140</span>) = <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="fn">_mm_unpacklo_ps</span>(<span class="var">fovY_val</span>, (<span class="type">__m128</span>)<span class="num">0LL</span>));
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Store Inverse Matrix Row 2: [0, 0, 0, near-far/far*near]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">54</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x150</span>) = <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="fn">_mm_unpacklo_ps</span>((<span class="type">__m128</span>)<span class="num">0LL</span>, <span class="var">v45</span>));
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    <span class="comment">// Store Inverse Matrix Row 3: [0, tan(fovY/2)*0.13, -1, 1/near]</span>
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">55</span>    *(<span class="type">__m128</span> *)(<span class="var">a1</span> + <span class="num">0x160</span>) = <span class="fn">_mm_unpacklo_ps</span>(
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>                                <span class="fn">_mm_unpacklo_ps</span>(<span class="var">tan_fovX_half_save</span>, (<span class="type">__m128</span>)<span class="num">0xBF800000</span>),
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>                                <span class="fn">_mm_unpacklo_ps</span>(<span class="var">tan_fovY_half_save</span>, <span class="var">v50</span>));
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">  </span>    
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">56</span>    *(<span class="type">float</span> *)(<span class="var">a1</span> + <span class="num">0x1C</span>) = <span class="num">1.0</span> / <span class="fn">fminf</span>(<span class="var">tanFovXby2_dup</span>, <span class="var">v34</span>.<span class="var">m128_f32</span>[<span class="num">0</span>]);
<span style="color:#555;user-select:none;display:inline-block;width:24px;text-align:right;margin-right:12px;border-right:1px solid #444;padding-right:8px;">57</span>  }
</div>

### **Conclusion: Owning the Pipeline** ###

And there we have it. We have successfully reverse-engineered the complete perspective and inverse projection matrix construction inside the Dunia Engine. 

We are now free to Intercept, Modify and Read the matrices, We now control what the game can see. 

A Trampoline hook here and we can control how the game engine goes from View -> Clip space.

Hopefully, this series has demystified the process and given you the tools to tackle your own reverse engineering targets. The math might look intimidating at first, but at the CPU 
level, it all breaks down to logic, patterns, and proving your hypotheses.

Happy reversing!

<div class="post-nav">
  <a href="{{ site.baseurl }}/part-5.2-reversing-projection-matrix/">&laquo; Part 5.2: Reversing Construction (Depth)</a>
</div>