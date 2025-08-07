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

Everything is clear till page 11. We will discuss equation 3.20. For those familiar with typical ODEs, it might appear unusual as the definition of the vector field as derivative contains an inverse function. This formulation is needed for Flow Matching because the ODEs used in the paper are not Autonomous and depend on time as a variable. We show below that for Autonomous case, using the semi-group property, that this definition reduces to usual simple differentiation without the inverse.



For any smooth flow of diffeomorphisms \\(\psi_t:\mathbb{R}^d\to\mathbb{R}^d\\), define the Eulerian velocity field by

$$
u_t(x)=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr),
$$

where

$$
\dot{\psi}_t(y)=\frac{d}{dt}\,\psi_t(y)
=\lim_{h\to 0}\frac{\psi_{t+h}(y)-\psi_t(y)}{h}.
$$

-Autonomous Case \\(u_t \equiv u\\)}

Because the velocity is constant in time, the flow satisfies the semigroup property

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