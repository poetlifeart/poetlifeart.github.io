---
title: "Discrete Flow Matching-1"
date: 2025-07-13
categories:
  - flow-matching
permalink: /flow-matching-theorem/part-1/    
tags:
- flow matching 
- mathematics
layout: post
---



<!-- Load MathJax so LaTeX renders in GitHub Pages without touching layouts -->
<script>
  window.MathJax = {
    tex: {
      inlineMath: [['\\(','\\)'], ['\\[','\\]']]
    }
  };
</script>
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>


## Table of Contents
- [Introduction](#introduction)
- [Training Procedure](#training-procedure)
- [Inference Procedure](#inference-procedure)
- [Factorized Source→Sink Velocities](#factorized-source-sink-velocities)




## Introduction {#introduction} 

In this post, we walk through the step-by-step derivations of some of the math in the 
paper [ Flow Matching Guide and Code](https://arxiv.org/pdf/2412.06264), at times provide a deeper discussion and correct minor mistakes and typos in that paper. The best way is to read the paper and use this blog to clarify various topics. Our goal is not to replicate the paper but to supplement it. 

Everything is clear till page 11. We will discuss equation 3.20. For those familiar with typical ODEs, it might appear unusual as the definition of the vector field as derivative contains an inverse function here. This formulation is needed for Flow Matching because the ODEs used in the paper are not Autonomous and depend on time as a variable. We show below that for Autonomous case, using the semi-group property, that this definition reduces to usual simple differentiation without the inverse.



For any smooth flow of diffeomorphisms \\(\psi_t:\mathbb{R}^d\to\mathbb{R}^d\\), define the Eulerian velocity field by

$$
u_t(x)=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr),
$$

where

$$
\dot{\psi}_t(y)=\frac{d}{dt}\,\psi_t(y)
=\lim_{h\to 0}\frac{\psi_{t+h}(y)-\psi_t(y)}{h}.
$$

In Autonomous case, \\(u_t \equiv u\\) because the velocity is constant in time, the flow satisfies the semigroup property:

$$
\psi_{t+h}=\psi_h\circ\psi_t.
$$

Hence

$$
\begin{aligned}
u(x)
&=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr)\\
&=\lim_{h\to0}\frac{\psi_{t+h}(\psi_t^{-1}(x))-\psi_t(\psi_t^{-1}(x))}{h}\\
&=\lim_{h\to0}\frac{(\psi_h\circ\psi_t)(\psi_t^{-1}(x))-x}{h}\\
&=\lim_{h\to0}\frac{\psi_h(x)-x}{h}.
\end{aligned}
$$

Thus in the autonomous case,

$$
u(x)
=\left.\frac{d}{dh}\right|_{h=0}\psi_h(x)
=\lim_{h\to0}\frac{\psi_h(x)-x}{h}.
$$


When \\(u_t\\) varies with time, the one‐parameter map \\(\psi_h\\) alone is not enough. Define the two‐time propagator

$$
\Phi_{t,t+h}:=\psi_{t+h}\circ\psi_t^{-1},
$$
which carries points from time \\(t\\) to \\(t+h\\). Then

$$
u_t(x)
=\lim_{h\to0}\frac{\psi_{t+h}(\psi_t^{-1}(x))-\psi_t(\psi_t^{-1}(x))}{h}
=\lim_{h\to0}\frac{\Phi_{t,t+h}(x)-x}{h}.
$$



In both cases the compact relation

$$
u_t(x)=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr)
$$

holds without changing notation.


We are going to repeat these notations now in the cotext of thinking about ODEs into ways:

### 1. Standard IVP (Trajectory-by-Trajectory)

Given a (possibly time-dependent) vector field  

$$
f\colon [0,1]\times R^d\;\to\; R^d,
$$  

the *initial-value problem* with label \\(x\in R^d\\) is  

$$
\begin{cases}
\dot y(t) = f\bigl(t,y(t)\bigr),\\
y(0) = x.
\end{cases}
$$  

- **Solution curve**: for each fixed \\(x\\), existence–uniqueness (e.g. local Lipschitz in \\(y\\)) yields a unique \\(y_x(t)\\).  
- Notation: we often write \\(y_x(t)\\) or simply \\(y(t)\\) when the initial point is understood.

### 2. Flow-map Formulation

Bundle *all* these curves into one map  

$$
\psi\colon [0,1]\times R^d\;\longrightarrow\; R^d,
\quad
\psi_t(x) = y_x(t).
$$  

Then \\(\psi\\) satisfies the **flow ODE**:  

$$
\frac{\partial}{\partial t}\,\psi_t(x)
= f\bigl(t,\psi_t(x)\bigr),
\qquad
\psi_0(x)=x,
$$  

for *every* \\(x\\).  Under smoothness (e.g. \\(C^1\\) in \\((t,x)\\)) each \\(\psi_t\\) is a diffeomorphism of \\(R^d\\).

- **Why use it?**  
  - Captures the entire dynamical system in one object \\(\psi\\).  
  - Makes clear that at \\(t=0\\) nothing has moved (\\(\psi_0=\mathrm{Id}\\)).  
  - Yields the semi-group property in the autonomous case.

#### Autonomous vs. Non-Autonomous

- **Autonomous** (\\(f(t,y)\equiv u(y)\\))  

  $$
  \psi_{t+h}=\psi_h\circ\psi_t,\quad
  u(x)
  =\left.\frac{d}{dh}\right|_{h=0}\psi_h(x)
  =\lim_{h\to0}\frac{\psi_h(x)-x}{h}.
  $$  

- **Non-Autonomous**  

  Define the two-time propagator  

  $$
  \Phi_{t,t+h}=\psi_{t+h}\circ\psi_t^{-1}.
  $$  

  Then  

  $$
  u_t(x)
  =\dot\psi_t\bigl(\psi_t^{-1}(x)\bigr)
  =\lim_{h\to0}\frac{\Phi_{t,t+h}(x)-x}{h}.
  $$  

In both cases the compact relation  

$$
u_t(x)=\dot\psi_t\bigl(\psi_t^{-1}(x)\bigr)
$$  

holds, but in the non-autonomous setting the inverse is essential to “re-anchor” at the current configuration.



<img src="/images/planes.png"
     alt="Figure 1: Flow of diffeomorphisms over time"
     width="50%"
     style="height:auto;">

In case of the flow, it is better to think of a series of "planes" stacked on top of each other in time. The first plane is the plane of initial conditions. We can think of paths as curves piercing the planes. What is happening in the non-autonomous case is that we cannot simply start from zero time ignoring where we are in time and take the usual 

$$
 \lim_{h\to0}\frac{\psi_h(x)-x}{h},
$$ 


always from "zero" time on the plane to delta time \\( h \\). Instead we need to bring back the point to the initial plane and then move the point to \\( t+ h \\) plane right "above" \\(x\\) on the plane \\(t\\). In our flow representation \\(\psi_t (x)\\) always requires \\(x\\) to be on the 
initial plane and thus when we take its derivative, the argument for the derivative has to be starting from initial plane as well. The derivative of \\(\psi\\) brings the point to \\(t\\) plane just as \\(\psi \\) does; it needs to know the initial point where \\(x\\) came from which is exactly what we are doing here:

$$
u_t(x)=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr)
$$


where we have for have 

$$ \psi_t^{-1}(x) = y$$ 

$$
\dot{\psi}_t(y)=\frac{d}{dt}\,\psi_t(y)
=\lim_{h\to 0}\frac{\psi_{t+h}(y)-\psi_t(y)}{h}.
$$

Note here we are using \\(y\\) label instead of \\(x\\) for points on the initial plane. Note also how the limit definition shows that the derivative too needs to defined from the initial plane to time \\(t \\) inheriting that property from  \\(\psi\\)