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

\documentclass{article}
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage[margin=1in]{geometry}

\begin{document}


For any smooth flow of diffeomorphisms \\(\psi_t:\mathbb{R}^d\to\mathbb{R}^d\\), define the Eulerian velocity field by
$$
u_t(x)=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr),
$$
where
$$
\dot{\psi}_t(y)=\frac{d}{dt}\,\psi_t(y)
=\lim_{h\to 0}\frac{\psi_{t+h}(y)-\psi_t(y)}{h}.
$$

\section*{Autonomous Case \\(u_t \equiv u\\)}

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

\section*{One–Line Comparison}

\begin{itemize}
  \item \textbf{Autonomous:} \quad
    \\(u(x)=\displaystyle\lim_{h\to0}\frac{\psi_h(x)-x}{h}\\).
  \item \textbf{Time‐Dependent:} \quad
    \\(u_t(x)=\displaystyle\lim_{h\to0}\frac{\Phi_{t,t+h}(x)-x}{h}\\),
    where \\(\Phi_{t,t+h}=\psi_{t+h}\circ\psi_t^{-1}\\).
\end{itemize}

In both cases the compact relation
$$
u_t(x)=\dot{\psi}_t\bigl(\psi_t^{-1}(x)\bigr)
$$
holds without changing notation.

\end{document}




## Training Procedure {#training-procedure}

1\. Sample a data point:

$$
X_1 \sim q_{\mathrm{data}}.
$$

This is your “target” $(X_i^1)$ in every coordinate.

2\. Sample a “source”:

$$
X_0
$$

from your simple prior (e.g. uniform or noise).

3\. Pick a time:

$$
t \sim U[0,1].
$$

4\. Draw the intermediate state:

$$
X_t \sim p_{t|0,1}(x|X_0,X_1) = \prod_{i=1}^D \left[ \kappa(t)\,\delta(x_i,X_i^1)+(1-\kappa(t))\,\delta(x_i,X_i^0) \right].
$$

In other words, each coordinate $i$ of $X_t$ is $X_i^1$ with probability $\kappa(t)$, otherwise $X_i^0$.

5\. Compute the loss using the true generator (depends on $X_i^1$) and your model’s posterior:

$$
p^{\theta,i}_{1|t}(\cdot|X_t).
$$

Because $X_i^1$ is always the data, it drives the true velocity in every coordinate, and $X_t$ is where you evaluate velocities.

The posterior $p^{\theta,i}_{1|t}(\cdot|X_t)$ is learned via a cross-entropy objective:

$$
L(\theta) = -\mathbb{E}_{t,X_0,X_1,X_t}\sum_{i=1}^d \log p^{\theta,i}_{1|t}(X_1^i|X_t)+\text{const}.
$$

## Inference Procedure {#inference-procedure}

During inference, reverse the process:

1\. Start from $X_0$ drawn from the prior.
2\. Iteratively step forward in $t$ using the learned posterior $p^{\theta,i}_{1|t}$ to sample each coordinate’s jump until you reach $X_1$, your generated sample.

$X_i^1$ in formulas is always the data’s coordinate $i$.
$X_t$ during training is sampled via the chosen mixture path $p_{t|0,1}$, ensuring the model learns the velocity field of this path.

## Factorized Source→Sink Velocities {#factorized-source-sink-velocities}

Because probability path and velocity factorize over coordinates, we handle each dimension independently:

$$
q_t(x)=\prod_{i=1}^d q^i_t(x_i), \quad u_t(y,x)=\sum_{i=1}^d \delta(y_{\bar i},x_{\bar i})u^i_t(y_i,x).
$$

## Mixture-path Construction

Define the conditional marginal path:

$$
p^i_{t|0,1}(x_i|x_0,x_1)=t\,\delta(x_i,x_{1,i})+(1-t)\,\delta(x_i,x_{0,i}).
$$

Thus, each coordinate:

$$
\Pr[X_t^i=X_1^i]=t, \quad \Pr[X_t^i=X_0^i]=1-t.
$$

