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

Note here we are using \\(y\\) label instead of \\(x\\) for points on the initial plane. Note also how the limit definition shows that the derivative too needs to be defined from the initial plane to time \\(t \\) inheriting that property from  \\(\psi\\)


We next turn to page 14 and proof of (3.30):


### Flow identity for \\(\log p_t\\) (3.30) from the continuity equation

Setup and notation:
Let \\(\Omega\subset\mathbb{R}^d\\). For \\(t\in[0,1]\\):


- \\(u_t:\Omega\to\mathbb{R}^d\\) is \\(C^1\\) in \\(x\\) (measurable in \\(t\\)).
- \\(p_t:\Omega\to(0,\infty)\\) is \\(C^1\\) in \\(x\\) and solves the continuity equation

$$
\partial_t p_t + \nabla\!\cdot(p_t\,u_t)=0.
$$

The flow \\(\psi_t:\Omega\to\Omega\\) solves

$$
\dot\psi_t(x)=u_t(\psi_t(x)),\qquad \psi_0(x)=x.
$$



We use the evaluation convention 

$$(\nabla\!\cdot u_t)(\psi_t(x)),$$

meaning that the differential operator (here divergence) is applied first with respect to the spatial variable, producing a scalar field, and \emph{then} this resulting scalar field is evaluated at the point \\(\psi_t(x)\\) along the flow trajectory

We need the following: 


If \\(f:[0,1]\times\Omega\to\mathbb{R}\\) is \\(C^1\\) in \\((t,x)\\), then for every \\(x\in\Omega\\),

$$
\frac{d}{dt}\,f_t(\psi_t(x))
=\partial_t f_t(\psi_t(x))
+\nabla f_t(\psi_t(x))\cdot u_t(\psi_t(x)). (*)
$$


proof:

Define \\(F(t,y):=f(t,y)\\) and \\(g(t):=F(t,\psi_t(x))\\). By the multivariable chain rule,

$$
g'(t)
=\partial_t F(t,\psi_t(x))
+ D_yF(t,\psi_t(x))\,[\dot\psi_t(x)].
$$

Since \\(D_yF(t,y)=\nabla f_t(y)\\) and \\(\dot\psi_t(x)=u_t(\psi_t(x))\\), we obtain the formula.


We will now show: Along the flow trajectory \\(t\mapsto\psi_t(x)\\),

$$
\frac{d}{dt}\log p_t(\psi_t(x))
= -\,(\nabla\!\cdot u_t)(\psi_t(x)).
$$

Consequently,

$$
\log p_1(\psi_1(x))
= \log p_0(\psi_0(x))
-\int_0^1 (\nabla\!\cdot u_t)(\psi_t(x))\,dt.
$$



From the continuity equation,

$$
\partial_t p_t
= -\nabla\!\cdot(p_t u_t)
= -p_t\,\nabla\!\cdot u_t - u_t\cdot\nabla p_t.
$$

Divide by \\(p_t>0\\) to get

$$
\partial_t \log p_t
= -\,\nabla\!\cdot u_t \;-\; u_t\cdot\nabla \log p_t.
$$

Apply \\(*\\) with \\(f_t=\log p_t\\):

$$
\frac{d}{dt}\log p_t(\psi_t(x))
=\partial_t\log p_t(\psi_t(x))
+ \nabla\log p_t(\psi_t(x))\cdot u_t(\psi_t(x)).
$$

Insert the previous equation and cancel the \\(u_t\cdot\nabla\log p_t\\) terms to obtain the differential form.

Integrate over \\(t\in[0,1]\\) to get the stated identity.

$$
\log p_{1}(\psi_{1}(x)) - \log p_{0}(\psi_{0}(x))
= - \int_{0}^{1} \big( \nabla \!\cdot u_{t} \big)\big( \psi_{t}(x) \big) \, dt.
$$



The expressions \\(u_t(X_t\mid X_1)\\) and \\(u_t(X_t\mid Z)\\) can be misleading, as they suggest probabilistic conditioning of the vector field itself. Here we write the vector field with a semicolon to indicate a parameter: \\(u_t(x;z)\\). For example, a clearer form of

$$
u_t(x) \;=\; \mathbb{E}\!\big[u_t(X_t\mid X_1)\,\big|\,X_t=x\big]
$$

is

$$
u_t(x) \;=\; \mathbb{E}\!\big[u_t(X_t; X_1)\,\big|\,X_t=x\big],
$$

which emphasizes that the second argument is a parameter, not a conditioning operation on the field. Let \\(p_Z(z)\\) be the \\(t\\)-independent density of the latent \\(Z\\). For each fixed \\(z\\), the conditional density \\(p_{t\mid Z}(x\mid z)\\) in \\(x\\) is transported by the velocity \\(u_t(x;z)\\) via the conditional continuity equation

$$
\partial_t p_{t\mid Z}(x\mid z) + \operatorname{div}_x\!\big(u_t(x;z)\,p_{t\mid Z}(x\mid z)\big)=0,
$$

and the marginal in \\(x\\) is

$$
p_t(x)=\int p_{t\mid Z}(x\mid z)\,p_Z(z)\,dz.
$$

Because \\(u_t(\cdot;z)\\) generates \\(p_{t\mid Z}(\cdot\mid z)\\), we have

$$
\frac{d}{dt}\,p_t(x)
= \int \partial_t p_{t\mid Z}(x\mid z)\,p_Z(z)\,dz
\tag{4.14}
$$

$$
= - \int \operatorname{div}_x\!\Big(u_t(x; z)\,p_{t\mid Z}(x\mid z)\Big)\,p_Z(z)\,dz
\tag{4.15}
$$

$$
= -\,\operatorname{div}_x\!\int u_t(x; z)\,p_{t\mid Z}(x\mid z)\,p_Z(z)\,dz
\tag{4.16}
$$

$$
= -\,\operatorname{div}_x\!\big(\bar u_t(x)\,p_t(x)\big),
\tag{4.17}
$$

where

$$
\bar u_t(x) := \frac{\int u_t(x; z)\,p_{t\mid Z}(x\mid z)\,p_Z(z)\,dz}{p_t(x)},
\qquad p_t(x) > 0.
$$

Equality (4.14) follows by differentiating under the \\(z\\)-integral (Leibniz rule), using the \\(C^1\\) regularity of \\(p_{t\mid Z}\\) and the fact that \\(p_Z\\) has bounded support. Equality (4.15) follows from the conditional continuity equation for each \\(z\\). Equality (4.16) follows from the linearity of \\(\operatorname{div}_x\\) and the fact it acts only on \\(x\\), allowing it to pass through the \\(z\\)-integral. Equality (4.17) follows from multiplying and dividing inside the integral by \\(p_t(x)\\) and using the definition of \\(\bar u_t\\).






We begin with

$$
\nabla_\theta L_{\mathrm{FM}}(\theta)
= \nabla_\theta\,\mathbb{E}_{t, X_t \sim p_t}
\big[ D\big(u_t(X_t),\, u^\theta_t(X_t)\big) \big].
$$

By differentiating inside the expectation we obtain

$$
= \mathbb{E}_{t, X_t \sim p_t}
\big[ \nabla_\theta D\big(u_t(X_t),\, u^\theta_t(X_t)\big) \big].
$$

Using the chain rule gives

$$
\overset{(i)}{=}
\mathbb{E}_{t, X_t \sim p_t}
\big[ \nabla_v D\big(u_t(X_t),\, u^\theta_t(X_t)\big)
\,\nabla_\theta u^\theta_t(X_t) \big].
\tag{4.12}
$$

Now expand \(u_t(X_t)\) as a conditional expectation with respect to \(Z\) given \(X_t\) in the explicit integral form

$$
u_t(X_t)
= \int u_t(X_t; z)\, p_{Z\mid t}(z \mid X_t)\, dz.
$$

Substituting this into (4.12) yields

$$
\mathbb{E}_{t, X_t \sim p_t}
\left[
\nabla_v D\!\left(
\int u_t(X_t; z)\, p_{Z\mid t}(z \mid X_t)\, dz,\;
u^\theta_t(X_t)
\right)
\,\nabla_\theta u^\theta_t(X_t)
\right].
$$

Since the entire integrand is a function of \(X_t\) (and \(t\)), we can move the conditional expectation over \(Z\) to the far right:

$$
= \mathbb{E}_{t, X_t \sim p_t}
\left[
\mathbb{E}_{Z \sim p_{Z\mid t}(\cdot \mid X_t)}
\big[
\nabla_v D(u_t(X_t; Z),\, u^\theta_t(X_t))
\,\nabla_\theta u^\theta_t(X_t)
\big]
\right].
$$

By the total law of expectation, this is equivalent to the joint expectation over \(t\), \(Z\), and \(X_t\) with \(X_t \sim p_{t\mid Z}(\cdot \mid Z)\):

$$
= \mathbb{E}_{t, Z \sim q,\; X_t \sim p_{t\mid Z}(\cdot \mid Z)}
\big[
\nabla_v D(u_t(X_t; Z),\, u^\theta_t(X_t))
\,\nabla_\theta u^\theta_t(X_t)
\big].
$$

Applying equation (4.21) conditionally on \(X_t\) now gives

$$
\overset{(ii)}{=}
\mathbb{E}_{t, Z \sim q,\; X_t \sim p_{t\mid Z}(\cdot \mid Z)}
\big[
\nabla_\theta D(u_t(X_t; Z),\, u^\theta_t(X_t))
\big].
$$

Finally, this is just

$$
= \nabla_\theta\,\mathbb{E}_{t, Z \sim q,\; X_t \sim p_{t\mid Z}(\cdot \mid Z)}
\big[ D(u_t(X_t; Z),\, u^\theta_t(X_t)) \big]
= \nabla_\theta L_{\mathrm{CFM}}(\theta).
$$

Here, step (i) uses the chain rule, the passage from the explicit integral to the conditional expectation moves the condition to the right since the inside depends only on \(X_t\), the total law of expectation is then used to write the joint over \((t, Z, X_t)\), and step (ii) applies equation (4.21) conditionally on \(X_t\).


