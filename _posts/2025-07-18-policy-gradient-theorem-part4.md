---
title: "Offline RL-4"
date: 2025-07-18
categories:
  - policy-gradient
permalink: /policy-gradient-theorem/part-4/  
tags:
- reinforcement learning
- policy gradient
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




Initial optimization problem (before any variational trick)
We want a policy whose (discounted) occupancy measure matches the expert's. Define

$$
\rho_\pi(s,a) \;=\; (1-\gamma)\sum_{t=0}^{\infty}\gamma^t\,\Pr_{\pi}\!\big(s_t=s,\,a_t=a\big).
$$

A clean imitation objective is ``occupancy matching'' with entropy regularization:

$$
\min_{\pi}\; \mathcal{D}\big(\rho_E \,\|\, \rho_\pi\big)\;-\;\lambda\,\mathcal H(\pi),
$$

where \\(mathcal{D}\\) is any statistical divergence between distributions on \\( (s,a) \\). Choosing \\(\mathcal{D}=\mathrm{JS}\\) gives

$$
\min_{\pi}\; 2\,\mathrm{JS}\!\big(\rho_E \,\|\, \rho_\pi\big)\;-\;\lambda\,\mathcal H(\pi).
$$

This is the \emph{pre-variational} formulation. Later we rewrite the JS term via a supremum over an auxiliary function (the discriminator), which yields the familiar GAN/GAIL saddle-point training. A useful identity we employ when turning occupancy expectations into RL returns is:

$$
\mathbb{E}_{(s,a)\sim \rho_\pi}\![c(s,a)]
\;=\; (1-\gamma)\,\mathbb{E}_{s_0\sim \rho_0}
\mathbb{E}_{\pi,P}\!\Big[\sum_{t=0}^{\infty}\gamma^t\,c(s_t,a_t)\,\Big|\,s_0\Big].
$$

JS divergence in standard and expanded forms
Let \\(P\\) and \\(Q\\) be distributions with densities \\(p(x)\\) and $q(x)$, and 

$$M=\tfrac12(P+Q)$ with $m(x)=\tfrac12\big(p(x)+q(x)\big)$$.

$$
\mathrm{JS}(P\|Q)
= \tfrac12\,\mathrm{KL}\!\big(P\|M\big) + \tfrac12\,\mathrm{KL}\!\big(Q\|M\big).
$$

Expanding and substituting \\(m(x)\\):

$$
\mathrm{JS}(P\|Q)
= \tfrac12\,\mathbb{E}_{P}\!\left[\log\frac{p}{p+q}\right]
+ \tfrac12\,\mathbb{E}_{Q}\!\left[\log\frac{q}{p+q}\right]
+ \log 2.
$$

Equivalently,

$$
\mathbb{E}_{P}\!\left[\log\frac{p}{p+q}\right]
+ \mathbb{E}_{Q}\!\left[\log\frac{q}{p+q}\right]
= 2\,\mathrm{JS}(P\|Q) - 2\log 2.
$$

Binary classification view and the GAN/GAIL discriminator
Define a binary classification problem: draw \\(Y\in\{1,0\}\\) with prior $1/2$, then \\(X\mid(Y{=}1)\!\sim P\\), 
\\(X\mid(Y{=}0)\!\sim Q\\). For any discriminator \\(D:\mathcal X\to(0,1)\\) estimating \\(\Pr(Y{=}1\mid X{=}x)\\), the (negative) expected log-likelihood (BCE) is

$$
\mathcal{L}_{\mathrm{BCE}}(D)
= -\mathbb{E}_{x\sim P}[\log D(x)] \;-\; \mathbb{E}_{x\sim Q}[\log(1-D(x))].
$$

Maximizing 

$$\mathcal{J}(D):=\mathbb{E}_{P}[\log D]+\mathbb{E}_{Q}[\log(1-D)]$$

yields the Bayes-optimal

$$
D^*(x) \;=\; \frac{p(x)}{p(x)+q(x)}.
$$

Plugging $D^*$ back,

$$
\max_{D}\ \mathcal{J}(D)
= \mathbb{E}_{P}\!\left[\log\frac{p}{p+q}\right]
+ \mathbb{E}_{Q}\!\left[\log\frac{q}{p+q}\right]
= -\log 4 + 2\,\mathrm{JS}(P\|Q).
$$

Hence

$$
\boxed{\ \max_{D}\ \mathbb{E}_{P}[\log D]+\mathbb{E}_{Q}[\log(1-D)]
= -\log 4 + 2\,\mathrm{JS}(P\|Q)\ }.
$$

Where the variational step comes in
The previous display is a variational characterization} of JS:

$$
2\,\mathrm{JS}(P\|Q) - \log 4
\;=\;
\sup_{D:\mathcal X\to(0,1)}
\Big\{\mathbb{E}_{P}[\log D(X)] + \mathbb{E}_{Q}[\log(1-D(X))]\Big\}.
$$

That \\(\sup_D\\) is the variational step. In practice, we parametrize $D$ (NN) and maximize the empirical version. More generally, for any $f$-divergence,

$$
D_f(P\|Q)
=\sup_{T}\ \mathbb{E}_{P}[T(X)] - \mathbb{E}_{Q}[f^*(T(X))],
$$

with $f^*$ the convex conjugate; the JS case with a logistic link recovers the GAN/GAIL discriminator.

From divergence to practical BCE and sums
Expectations become batch means. With samples \\(\{x_i^P\}_{i=1}^m\!\sim P$ and $\{x_j^Q\}_{j=1}^n\!\sim Q\\),

$$
\widehat{\mathcal{L}}_{\mathrm{BCE}}(D)
= -\frac{1}{m}\sum_{i=1}^m \log D(x_i^P)
\;-\;\frac{1}{n}\sum_{j=1}^n \log\big(1-D(x_j^Q)\big),
$$

and we take SGD steps on this batch mean to approximate the variational supremum.

Connection to GAIL (imitation by occupancy matching)
Let \\(P=\rho_E\\) and \\(Q=\rho_\pi\\) be expert and policy occupancies over \\((s,a) \\). Using the variational identity,

$$
\min_{\pi}\ \max_{D:(0,1)}\
\mathbb{E}_{(s,a)\sim \rho_E}[\log D(s,a)]
+\mathbb{E}_{(s,a)\sim \rho_\pi}[\log(1-D(s,a))]
-\lambda\,\mathcal H(\pi)
$$

is exactly 

$$\min_{\pi}\; 2\,\mathrm{JS}(\rho_E\|\rho_\pi)-\lambda\mathcal H(\pi)$$. 

In practice we alternate:
(i) maximize empirical BCE over \\(D\\) (classification on expert vs.\ policy samples),
(ii) update \\(\pi\\) with an RL optimizer using a reward formed from \\(D\\),
e.g.

$$r(s,a)=-\log(1-D(s,a))$ or $r(s,a)=\log\frac{D}{1-D}$$, 

and compute advantages via time-sums (GAE) to drive the policy step.


Before any variational trick, imitation is ``minimize a divergence between occupancies.'' The \emph{variational identity} for JS turns that into a GAN-style discriminator maximization, whose empirical version is just binary cross-entropy. Coupling that with an RL update on the policy (using a reward built from $D$) yields the GAIL training loop.


