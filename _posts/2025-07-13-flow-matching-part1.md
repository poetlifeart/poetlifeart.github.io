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

### Proof of Theorem 4, page 20 repeated to clarify better notation 

The expressions \\(u_t(X_t\mid X_1)\\) and \\(u_t(X_t\mid Z)\\) can be misleading, as they suggest probabilistic conditioning of the vector field itself. Here we write the vector field with a comma. For example, a clearer form of

$$
u_t(x) \;=\; \mathbb{E}\!\big[u_t(X_t\mid X_1)\,\big|\,X_t=x\big]
$$

is

$$
u_t(x) \;=\; \mathbb{E}\!\big[u_t(X_t, X_1)\,\big|\,X_t=x\big],
$$

We are not taking condtional expectation twice. The first bar in the paper is really a way of saying fixed \\(X_1\\) but
when the paper uses \\(Z\\) more generally for \\(X_1\\), it treats it as a random variable. Perhaps 


$$u_t(X_t, X_1=x_1)$$

would have been better notation when \\(X_1\\) is fixed.


We begin from the definition

$$
u_t(x) = \mathbb{E}\!\left[\,u_t(X_t, Z) \,\middle|\, X_t = x\right].
$$

The loss gradient is

$$
\nabla_\theta L_{\mathrm{FM}}(\theta)
= \nabla_\theta\,\mathbb{E}_{t, X_t \sim p_t}
\big[ D\big(u_t(X_t),\, u^\theta_t(X_t)\big) \big]
$$

$$
= \mathbb{E}_{t, X_t \sim p_t}
\big[ \nabla_\theta D\big(u_t(X_t),\, u^\theta_t(X_t)\big) \big].
$$

Using the chain rule gives

$$
= \mathbb{E}_{t, X_t \sim p_t}
\big[ \nabla_v D\big(u_t(X_t),\, u^\theta_t(X_t)\big)
\,\nabla_\theta u^\theta_t(X_t) \big].
$$

Substituting 

$$u_t(X_t) = \mathbb{E}[u_t(X_t, Z) \mid X_t]$$

$$
= \mathbb{E}_{t, X_t \sim p_t}
\big[ \nabla_v D\big(\mathbb{E}_{Z \sim p_{Z\mid t}(\cdot \mid X_t)}[u_t(X_t, Z) \mid X_t],\, u^\theta_t(X_t)\big)
\,\nabla_\theta u^\theta_t(X_t) \big].
$$

Since the outer factor depends only on \\(X_t\\) (and \\(t\\), we can move the conditioning on \\(X_t\\) all the way to the right:

$$
= \mathbb{E}_{t, X_t \sim p_t}
\mathbb{E}_{Z \sim p_{Z\mid t}(\cdot \mid X_t)}
\big[ \nabla_v D(u_t(X_t, Z),\, u^\theta_t(X_t))
\,\nabla_\theta u^\theta_t(X_t) \mid X_t ].
$$

Applying the law of total expectation yields the joint form 

$$
= \mathbb{E}_{t, Z \sim q,\; X_t \sim p_t}
\big[ \nabla_v D(u_t(X_t, Z),\, u^\theta_t(X_t))
\,\nabla_\theta u^\theta_t(X_t) \big].
$$

we can condition on \\(Z\\) and break up the joined distribution the other way (for simplicity we
do not show the \\(|Z_t\\) on the right as we did for \\(X_t\\) )



$$
= \mathbb{E}_{t, Z \sim q,\; X_t \sim p_{t\mid Z}(\cdot \mid Z)}
\big[ \nabla_v D(u_t(X_t, Z),\, u^\theta_t(X_t))
\,\nabla_\theta u^\theta_t(X_t) \big].
$$

Applying equation (4.21) conditionally on \\(X_t\\) gives

$$
= \mathbb{E}_{t, Z \sim q,\; X_t \sim p_{t\mid Z}(\cdot \mid Z)}
\big[ \nabla_\theta D(u_t(X_t, Z),\, u^\theta_t(X_t)) \big],
$$

and therefore

$$
= \nabla_\theta\,\mathbb{E}_{t, Z \sim q,\; X_t \sim p_{t\mid Z}(\cdot \mid Z)}
\big[ D(u_t(X_t, Z),\, u^\theta_t(X_t)) \big]
= \nabla_\theta L_{\mathrm{CFM}}(\theta).
$$

It's fine to read the paper with the conditional notation of the vector field as long as you keep the above explanation in mind. For example: 


In Theorem 4 (Eq. 4.24) we are not learning the conditional vector field \\(u_t(\cdot \mid Z)\\). The network \\(u_t^\theta(x)\\) only receives \\(X_t\\) as input, whereas the conditional target \\(u_t(X_t\mid Z)\\) depends on both \\(X_t\\) and the endpoint variable \\(Z\\) (e.g., \\(Z=X_1\\)), The notation \\(u_t(X_t, Z)\\) is much better in depicting
that we have two inputs and one is missing. Consequently, the regression with this input–target mismatch returns the conditional mean:

$$
u_t^\theta(x)\ \xrightarrow{\ \mathrm{MSE}\ }\ \mathbb{E}\!\left[u_t(X_t\mid Z)\ \middle|\ X_t=x\right].
$$

In particular, with \\(Z=X_1\\), Eq. (4.24) states exactly that

$$
u_t(x)=\mathbb{E}\!\left[u_t(X_t\mid X_1)\ \middle|\ X_t=x\right],
$$

i.e., we learn the Eulerian (marginal) field obtained by averaging the conditional field over the unknown \\(Z\\) at fixed \\(X_t=x\\), not the conditional field itself.


 


### Optimal Transport and linear conditional flow

On page 25, \\(\phi\\) is introduced right after 4.43. Here is where it comes from: 

In dyanamic Optimal Transport, the primary optimization outputs are  
$$p_t^* ,$$ $$u_t^*.$$

The transport map \\(\phi\\) is derived. Given \\(u_t^*\\), define \\(\psi_t\\) by

$$
\dot\psi_t(x)=u_t^*(\psi_t(x)),\qquad \psi_0(x)=x,
$$

hence

$$
p_t^*=(\psi_t)_{\#}p,\qquad \phi(x)=\psi_1(x).
$$

Under quadratic cost as shown at the bottom of that page,

$$
\psi_t(x)=(1-t)x+t\,\phi(x),
$$

so \\(\phi(x)=\psi_1(x)\\) is the endpoint at time \\(1\\).

### Adding some steps to probability flux on page 41:

$$
\begin{aligned}
\sum_{x} u_t(y, x) \, p_t(x) 
&=  \sum_{x: x \ne y} u_t(y, x) \, p_t(x) +  u_t(x, x) \, p_t(x)\\[6pt]
&=  \sum_{x: x \ne y} u_t(y, x) \, p_t(x) +  u_t(y, y) \, p_t(y)\\[6pt]
&=  \underbrace{\sum_{x: x \ne y} u_t(y, x) \, p_t(x)}_{\text{incoming flux}} -\underbrace{\sum_{x: x \ne y} u_t(x, y) \, p_t(y)}_{\text{outgoing flux}} \\[6pt]
&= - \sum_{x: x \ne y} \big[ j_t(x, y) - j_t(y, x) \big] \,.
\end{aligned}
$$


### Factorized paths and velocities




Here, the important observations are twofold. First, in the discrete case there is no underlying differential structure. We cannot define vector fields pointwise as limits of movements along integral curves, since we cannot start from a source and end at a sink and then take the limit of their difference as the endpoint approaches the start point. Our “vectors” are instead specified by sink–source pairs on a "grid". Each grid point is an enumeration of tokens from a vocabulary in different positions. For example, assuming 100 tokens in the vocabulary, the integer \\(x=(3, 100, 4)\\) depicts a sentence with the third, last, and fourth elements, while another vector might be \\(y=(6, 100, 5)\\) A sink–source “vector” is then given by \\( (x,y) \\).

The second observation is that unlike the continuous differentiable case, where one can begin with an ODE and study the Fokker–Planck–type evolution of the induced density, in the discrete case we must begin with the evolution of probabilities directly and define our “vectors” in probabilistic terms as well.

Later in the paper, the authors introduce factorized velocities, which they compare with the coordinate representation of ODEs. This analogy must be treated with some care. It is possible to define ODEs on manifolds abstractly without reference to coordinates. What is really happening here is that the flow is being restricted to one coordinate at a time. Such a restriction has no analogue when the domain admits a differential structure, where the flow may follow complicated smooth twists and turns.

In the discrete FM setting, “factorization” is not just a representation choice — it restricts the model class. You’re constraining the process so that it evolves only one coordinate at a time, which is a modeling assumption, not a coordinate artifact. This restriction does not prevent our ability to transport mass from any point to another point point on the grid. It is similar to  how we get from one point to the other while driving on grid like streets. Except that here, we can make jumps past intersections as long as we move East, West, South, and North one direction at a time. 

<img src="/images/grid.png"
     alt="Figure 2: Flow on the grid"
     width="30%"
     style="height:auto;">

Finally, let's discuss how these factorized velocities work. The paper's notation might at first complicated but it is very simple:

$$
u_t(y,x) \;=\; \sum_{i} \delta(y_{\bar i},x_{\bar i}) \; u^{(i)}_t(y_i,x).
$$


Equivalently Define \\(D(x,y) = \{ j : y_j \neq x_j \}\\). Then

$$
u_t(y,x) \;=\;
\begin{cases}
u^{(i)}_t(y_i,x), & \text{if } D(x,y)=\{i\}, \\[6pt]
0, & \text{if } |D(x,y)| \neq 1 .
\end{cases}
$$


This leade to 7.1, "source to single sink token" coordinate-wise independent transitions  decomosition:

$$
\mathbb{P}\!\left(X_i(t+h)=y_i \mid X_t=x\right)
= \delta(y_i,x_i) + h\,u_i(y_i,x) + o(h).
$$



Once the assumption of "factroized velocities" 7.12 is made, the velocities become \\(\textit{effectively}\\) "token to token"; i.e, not only the sink is factorized the source is computationally factorized as well. The reason we say effectively is that under factorized assumtion, the evolution of the probability paths conditioned on the state becomes a weighted 
sum of sink-source single token-to-token first oderder Markov transitions. Below we provide more details as how this comes about. 



When using factorized velocities, we can sample CTMC models coordinate-wise (Campbell et al., 2024):

$$
P(X_{t+h} = y \mid X_t = x)
= \delta(y, x) 
+ h \sum_i \delta(y_{\bar i}, x_{\bar i})\,u^i_t(y_i, x) 
+ o(h)
$$

$$
= \prod_i \Big( \delta(y_i, x_i) + h\,u^i_t(y_i, x) + o(h) \Big),
$$

where the second equality follows from 

$$
\delta(y, x) = \prod_i \delta(y_i, x_i)
$$

and the identity

$$
\prod_i \left(a_i + h b_i\right)
= \prod_i a_i 
+ h \sum_i \Bigg(\prod_{j\neq i} a_j\Bigg)b_i 
+ o(h).
$$


Assume the product form

$$
q_t(x) \;=\; \prod_i q^i_t(x_i),
$$

Paper shows that the  joint distribution obeys sum-velocities: 

$$
\frac{d}{dt}q_t(y)
= \sum_{x}\left[\sum_i \delta(y_{\bar i},x_{\bar i})\,u^i_t(y_i,x_i)\right] q_t(x).
$$

Fix an index \\(i\\) and sum the previous display over \\(y_{\bar i}\\):

$$
\begin{align*}
\frac{d}{dt}q^i_t(y_i)
&= \sum_{y_{\bar i}}\frac{d}{dt}q_t(y) \\
&= \sum_{y_{\bar i}} \sum_x \left[\sum_{j}\delta(y_{\bar j},x_{\bar j})\,u^j_t(y_j,x_j)\right] q_t(x) \\
&= \sum_{j}\sum_x q_t(x)\,\left[\sum_{y_{\bar i}}\delta(y_{\bar j},x_{\bar j})\,u^j_t(y_j,x_j)\right].
\end{align*}
$$

Consider the inner sum. If \\(j=i\\), then

$$
\sum_{y_{\bar i}}\delta(y_{\bar i},x_{\bar i})\,u^i_t(y_i,x_i)
= u^i_t(y_i,x_i).
$$

If \\(j\neq i\\), then the Kronecker delta fixes all coordinates except \\(y_j\\), so

$$
\sum_{y_{\bar i}}\delta(y_{\bar j},x_{\bar j})\,u^j_t(y_j,x_j)
= \sum_{y_j} u^j_t(y_j,x_j) \;=\; 0,
$$

by the rate condition \\(\sum_{y_j}u^j_t(y_j,x_j)=0\\).

Thus only the \\(j=i\\) term survives, giving

$$
\frac{d}{dt}q^i_t(y_i)
= \sum_x u^i_t(y_i,x_i)\,q_t(x).
$$

Now decompose \\(x=(x_i,x_{\bar i})\\) and use the product form:

$$
\begin{align*}
\frac{d}{dt}q^i_t(y_i)
&= \sum_{x_i}\sum_{x_{\bar i}} u^i_t(y_i,x_i)\,q^{\bar i}_t(x_{\bar i})\,q^i_t(x_i) \\
&= \sum_{x_i} u^i_t(y_i,x_i)\,q^i_t(x_i)\,\underbrace{\sum_{x_{\bar i}} q^{\bar i}_t(x_{\bar i})}_{=\,1}.
\end{align*}
$$

Therefore,

$$
\frac{d}{dt}q^i_t(y_i)
= \sum_{x_i} u^i_t(y_i,x_i)\,q^i_t(x_i),
$$

which is exactly the Kolmogorov equation for the marginal \\(q^i_t\\) with generator \\(u^i_t\\).
\end{proof}
This shows the decomposition is complete in both source and sink Markov transition process. In the paper the above
proof is missing. This proof works just as well in the conditional case as well. So why did we say effectively? 
It is because our Markov process evolves the conditional expection and that process depends on the entire state not
individual tokens of the state:

$$
u^{\,i}_t(y_i, x)
= \sum_{z} u^{\,i}_t(y_i, x_i \mid z)\, p_{Z\mid t}(z \mid x)
= \mathbb{E}\!\left[\,u^{\,i}_t\!\big(y_i, X^{i}_t \mid Z\big)\,\middle|\, X_t = x \right].
$$


This eventually leads for example for the mixture model to update sampling rule 7.34:

$$
\begin{aligned}
\mathbb{P}\!\left(X_i^{\,t+h} = y_i \,\middle|\, X_t = x\right)
&= \delta(y_i, x_i) + h\,u_i(y_i, x) + o(h)  \\
&= \sum_{x_i^{1}}
\Bigl[
\delta(y_i, x_i)
+ h\,\frac{\dot{\kappa}_t}{1-\kappa_t}\bigl(\delta(y_i, x_i^{1}) - \delta(y_i, x_i)\bigr)
+ o(h)
\Bigr]\,
p_{i^{1}\mid t}(x_i^{1}\mid x).
\end{aligned}
$$

Each sample of the above sum is a \\(x-\\) token \\(i\\) to \\(y)-\\)token \\(i\\) transition only after \\(X_1^i \sim p_{i^{1}\mid t}(x_i^{1\mid x)\\) is chosen. The whole of the sentence \\( x \\) controls the sampling but once the sample is created, the transition is \\(x-\\)token \\(i\\) to \\(y-\\)token \\(i\\). 

Lastly, because we lack a differential strcuture, we cannot evaluate the velocity fields by plugging the state into some derevative; we have to extract the velocity fields from the contuity equation as it is shown in 7.24. 





Then, we build the factorized conditional paths

$$
p_{t \mid 0,1}(x \mid x_0, x_1) = \prod_i p^i_{t \mid 0,1}(x^i \mid x_0, x_1) \tag{7.21}
$$

as mixtures

$$
p^i_{t \mid 0,1}(x^i \mid x_0, x_1) = \kappa_t \, \delta(x^i, x^i_1) 
+ (1-\kappa_t) \, \delta(x^i, x^i_0), \tag{7.22}
$$


where \\(\kappa : [0,1] \to [0,1]\) is a \(C^1([0,1])\\) scheduler.  
Note that a random variable \\(X^i_t \sim p^i_{t \mid 0,1}(\cdot \mid x_0, x_1)\\) follows

$$
X^i_t =
\begin{cases}
x^i_1 & \text{with prob. } \kappa_t, \\
x^i_0 & \text{with prob. } 1-\kappa_t,
\end{cases} \tag{7.23}
$$

i.e. it assumes either the source or the target states with a probability depending on the time \\(t\\).  
If \\(\kappa_0 = 0\\) and \\(\kappa_1 = 1\\), then the marginal \\(p_t(x)\\) in (7.1) satisfies the boundary constraints.  

We also need generating velocities \\(u^i_t(y^i, x^i \mid x_0, x_1)\\) for \\(p^i_{t \mid 0,1}(x^i \mid x_0, x_1)\\), which are solutions to (7.18). We derive these as follows:

$$
\frac{d}{dt} p^i_{t \mid Z}(y^i \mid z)
\overset{(7.22)}{=} \dot{\kappa}_t \big( \delta(y^i, x^i_1) - \delta(y^i, x^i_0) \big)
$$

$$
= \dot{\kappa}_t \left[ \delta(y^i, x^i_1) - 
\frac{p^i_{t \mid Z}(y^i \mid z) - \kappa_t \delta(y^i, x^i_1)}{1-\kappa_t} \right]
$$

$$
= \frac{\dot{\kappa}_t}{1-\kappa_t} \Big( \delta(y^i, x^i_1) - p^i_{t \mid Z}(y^i \mid z) \Big)
$$

$$
= \sum_{x^i} \frac{\dot{\kappa}_t}{1-\kappa_t} 
\Big( \delta(y^i, x^i_1) - \delta(y^i, x^i) \Big) p^i_{t \mid Z}(x^i \mid z),
$$

where we have used \\(z = (x_0, x_1)\\) and \\(Z = (X_0, X_1)\\) interchangeably to keep notation concise.  

In conclusion, we have found a conditional velocity generating the path in (7.22), namely

$$
u^i_t(y^i, x^i \mid x_0, x_1) =
\frac{\dot{\kappa}_t}{1-\kappa_t} \Big( \delta(y^i, x^i_1) - \delta(y^i, x^i) \Big). \tag{7.24}
$$



$$
u_t^{\,i}(y_i, x)
= \sum_{z} u_t^{\,i}\big(y_i, X_i^{\,t} \mid z\big)\, p_{Z\mid t}(z \mid x)
= \mathbb{E}\!\left[\, u_t^{\,i}\big(y_i, X_i^{\,t} \mid Z\big) \,\middle|\, X_t = x \right].
$$

