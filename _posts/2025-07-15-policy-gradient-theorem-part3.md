---
title: "Offline RL-3"
date: 2025-07-15
categories:
  - policy-gradient
permalink: /policy-gradient-theorem/part-3/  
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


GAIL — imitation (no explicit reward recovery) GAIL (Ho and Ermon, 2016).}

$$
\min_{\pi}\ \max_{D:\,S\times A\to(0,1)}
\ \ \mathbb{E}_{(s,a)\sim \mu_E}[\log D(s,a)]
+\mathbb{E}_{(s,a)\sim \mu_\pi}[\log(1-D(s,a))]
-\lambda\,\mathcal{H}(\pi).
$$

Policy reward for RL update:

$$
r_D(s,a)= -\log\!\big(1-D(s,a)\big)
\quad\text{or}\quad
r_D(s,a)=\log D(s,a)-\log\!\big(1-D(s,a)\big).
$$



AIRL — inverse RL (recovers a reward)
(Fu, Luo, Levine, 2018).}

$$
D_{\theta,\phi}(s,a,s')
= \frac{\exp f_{\theta,\phi}(s,a,s')}{\exp f_{\theta,\phi}(s,a,s')+\pi(a\mid s)},
\qquad
f_{\theta,\phi}(s,a,s') = r_\theta(s,a) + \gamma\,h_\phi(s') - h_\phi(s).
$$

Discriminator loss:

$$
\min_{\theta,\phi}\;
-\,\mathbb{E}_{(s,a,s')\sim \mu_E}\big[\log D_{\theta,\phi}(s,a,s')\big]
-\,\mathbb{E}_{(s,a,s')\sim \mu_\pi}\big[\log\!\big(1-D_{\theta,\phi}(s,a,s')\big)\big].
$$

Policy reward for the RL step:

$$
\hat r_{\theta,\phi}(s,a,s')
= \log D_{\theta,\phi}(s,a,s') - \log\!\big(1-D_{\theta,\phi}(s,a,s')\big)
= f_{\theta,\phi}(s,a,s') - \log \pi(a\mid s).
$$



Soft IQ — Q-only form (non-adversarial, \\(\chi^2\\) surrogate)
Soft IQ / IQ-Learn (Q-only, \\(\chi^2\\) surrogate)

$$
\pi_Q(a\mid s)\ \propto\ \exp Q(s,a),
\qquad
\delta_Q(s,a)
= Q(s,a) - \gamma\,\mathbb{E}_{s'}\!\left[\log \sum_{a'} e^{Q(s',a')}\right].
$$

Minimization objective:

$$
\mathcal{L}^{Q}_{\text{soft-IQ}}(Q)
= \mathbb{E}_{(s,a,s')\sim \mu_E}\!\big[\delta_Q(s,a)^2\big]
\;+\;(1-\gamma)\,\mathbb{E}_{s_0\sim \rho_0}\!\left[\log \sum_{a} e^{Q(s_0,a)}\right].
$$




Goal
Show precisely how the Jensen--Shannon (JS) divergence turns into the GAN/GAIL discriminator objective (binary cross-entropy), and pinpoint where the variational step enters.

JS divergence in standard and expanded forms

Let \\(P\\) and \\(Q\\) be distributions on \\(\mathcal X\\) with densities \\(p(x)\\) and \\(q(x)\\). Define the mixture 

\\(M=\tfrac12(P+Q)\\) with density \\(m(x)=\tfrac12\big(p(x)+q(x)\big)\\). By definition,

$$
\mathrm{JS}(P\|Q)
= \tfrac12\,\mathrm{KL}\!\big(P\|M\big) + \tfrac12\,\mathrm{KL}\!\big(Q\|M\big).
$$

Expanding the KL terms and substituting \\(m(x)=\tfrac12(p(x)+q(x))\\),

$$
\mathrm{JS}(P\|Q)
= \tfrac12\,\mathbb{E}_{x\sim P}\!\left[\log\frac{p(x)}{m(x)}\right]
+ \tfrac12\,\mathbb{E}_{x\sim Q}\!\left[\log\frac{q(x)}{m(x)}\right]
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

Consider the binary classification problem:
draw \\(Y\in\{1,0\}\\) with equal prior \\(1/2\\), then
\\(X\mid (Y{=}1)\sim P\\) and \\(X\mid (Y{=}0)\sim Q\\).
For any discriminator \\(D:\mathcal X\to(0,1)\\) intended to approximate \\(D(x)\approx \Pr(Y{=}1\mid X{=}x)\\), the (negative) expected log-likelihood (binary cross-entropy) is

$$
\mathcal{L}_{\mathrm{BCE}}(D)
= -\mathbb{E}_{x\sim P}[\log D(x)] \;-\; \mathbb{E}_{x\sim Q}[\log(1-D(x))].
$$

Equivalently, maximizing

$$\mathcal{J}(D) := \mathbb{E}_{x\sim P}[\log D(x)] + \mathbb{E}_{x\sim Q}[\log(1-D(x))]$$

gives the Bayes-optimal classifier pointwise in \(x\):

$$
D^*(x) \;=\; \frac{p(x)}{p(x)+q(x)}.
$$

Plugging \\(D^*\\) back into \\(\mathcal{J}(D)\\) yields

$$
\max_{D}\ \mathcal{J}(D)
= \mathbb{E}_{P}\!\left[\log\frac{p}{p+q}\right]
+ \mathbb{E}_{Q}\!\left[\log\frac{q}{p+q}\right]
= -\log 4 + 2\,\mathrm{JS}(P\|Q).
$$

Thus,

$$
\boxed{\ \max_{D}\ \mathbb{E}_{P}[\log D] + \mathbb{E}_{Q}[\log(1-D)]
= -\log 4 + 2\,\mathrm{JS}(P\|Q)\ }.
$$

This is the canonical GAN/GAIL discriminator objective: maximizing the discriminator's expected log-likelihood is \emph{exactly} maximizing a tight lower bound on \\(2\,\mathrm{JS}(P\|Q)-\log 4\\).

Where the variational step comes in

The equality above is a variational characterization of JS divergence: it rewrites the divergence as a supremum over an auxiliary function \\(D\\):

$$
2\,\mathrm{JS}(P\|Q) - \log 4
\;=\;
\sup_{D:\mathcal X\to(0,1)}
\Big\{
\mathbb{E}_{x\sim P}[\log D(x)] + \mathbb{E}_{x\sim Q}[\log(1-D(x))]
\Big\}.
$$

The ``\\(\sup_D\\)'' is the variational operation. In practice, GANs/GAIL \emph{parametrize} \\(D\\) (e.g., a neural net) and maximize the empirical version of the RHS.

More generally, for an \\(f\\)-divergence one has the variational \\(f\\)-GAN form

$$
D_f(P\|Q)
=\sup_{T}\ \mathbb{E}_{P}[T(X)] - \mathbb{E}_{Q}[f^*(T(X))],
$$

where \(f^*\) is the convex conjugate of \(f\). The JS case with the logistic link reduces to the discriminator objective above.

From divergence to the practical BCE and sums

The theoretical expectations become empirical averages over minibatches. Given samples \(\{x_i^P\}_{i=1}^m\sim P\) and \(\{x_j^Q\}_{j=1}^n\sim Q\),

$$
\widehat{\mathcal{L}}_{\mathrm{BCE}}(D)
= -\frac{1}{m}\sum_{i=1}^m \log D(x_i^P)
\;-\;\frac{1}{n}\sum_{j=1}^n \log\big(1-D(x_j^Q)\big),
$$

and one performs gradient steps on this batch mean to approximate the variational supremum.

Connection to GAIL (imitation by occupancy matching

Let \\(\rho_E\\) and \\(\rho_\pi\\) denote the expert and current-policy (discounted) occupancy measures over \\((s,a)\\).
GAIL solves

$$
\min_{\pi}\ \max_{D:(0,1)}\
\mathbb{E}_{(s,a)\sim \rho_E}[\log D(s,a)]
+\mathbb{E}_{(s,a)\sim \rho_\pi}[\log(1-D(s,a))]
-\lambda\,\mathcal H(\pi).
$$

By the variational identity above, the inner maximization is exactly the JS discriminator problem with \\(P=\rho_E\\), \\(Q=\rho_\pi\\). Thus the outer minimization over \\(\pi\\) is minimizing \\(2\,\mathrm{JS}(\rho_E\|\rho_\pi)-\lambda\mathcal H(\pi)\\), i.e., occupancy matching with entropy regularization. In practice, one alternates:
(i) maximize the empirical BCE over \\(D\\) (classification on expert vs policy samples), and
(ii) update \\(\pi\\) with an RL optimizer using a reward formed from \\(D\\), e.g.,
\\(r(s,a)=-\log(1-D(s,a))\\) or \\(r(s,a)=\log\frac{D}{1-D}\\).

The GAN/GAIL discriminator objective is not a heuristic: it is the exact variational form of the JS divergence. ``Variational'' enters at the supremum over functions \\(D\\). Implementations replace expectations by batch sums/means and parametrize \\(D\\), yielding the familiar binary cross-entropy training loop.

.........................................

% Binary classification view and the GAN/GAIL discriminator
% Population MLE → conditional cross-entropy (Route A)

Setup: Priors \\(\pi_1=\Pr(Y{=}1)\\), \\(\pi_0=1-\pi_1\\).
Class-conditionals \\(X\mid(Y{=}1)\sim P\\) with density \\(p(x)\\) and \\(X\mid(Y{=}0)\sim Q\\) with density \\(q(x)\\).
Model (discriminator) \\(D_\omega(x)=p_\omega(Y{=}1\mid x)\in(0,1)\\), so \\(p_\omega(0\mid x)=1-D_\omega(x)\\).

$$
\tilde p_{X,Y}(x,y)=
\begin{cases}
\pi_1\,p(x), & y=1,\\
\pi_0\,q(x), & y=0,
\end{cases}
\qquad
\tilde p_X(x)=\pi_1 p(x)+\pi_0 q(x),
\qquad
\tilde p_{Y\mid X}(y\mid x)=\dfrac{\tilde p_{X,Y}(x,y)}{\tilde p_X(x)}.
$$

Population MLE (conditional model):

$$
J(\omega)=\mathbb{E}_{(X,Y)\sim \tilde p_{X,Y}}\!\big[\log p_\omega(Y\mid X)\big].
$$

Condition on \(X\) (tower property):

$$
J(\omega)=\mathbb{E}_{X\sim \tilde p_X}\!\left[\sum_{y\in\{0,1\}}
\tilde p_{Y\mid X}(y\mid X)\,\log p_\omega(y\mid X)\right].
$$

Expand via the joint \\(\tilde p_{X,Y}=\tilde p_{Y\mid X}\tilde p_X\\)

$$
\begin{aligned}
J(\omega)
&=\sum_{y\in\{0,1\}}\int \tilde p_{Y\mid X}(y\mid x)\,\tilde p_X(x)\,\log p_\omega(y\mid x)\,dx\\
&=\sum_{y\in\{0,1\}}\int \tilde p_{X,Y}(x,y)\,\log p_\omega(y\mid x)\,dx\\
&=\pi_1\int p(x)\log D_\omega(x)\,dx\;+\;\pi_0\int q(x)\log\!\big(1-D_\omega(x)\big)\,dx\\
&=\pi_1\,\mathbb{E}_{x\sim P}\!\big[\log D_\omega(x)\big]\;+\;\pi_0\,\mathbb{E}_{x\sim Q}\!\big[\log(1-D_\omega(x))\big].
\end{aligned}
$$

Conditional cross-entropy (negative population log-likelihood):

$$
\mathrm{CE}(\omega)=-J(\omega)
=-\pi_1\,\mathbb{E}_{x\sim P}\!\big[\log D_\omega(x)\big]
-\pi_0\,\mathbb{E}_{x\sim Q}\!\big[\log(1-D_\omega(x))\big].
$$

Equal priors \\(\pi_1=\pi_0=\tfrac12\\) (rescale by a positive constant—argmax/argmin unchanged):

$$
\mathcal{L}_{\mathrm{BCE}}(D_\omega)
=-\mathbb{E}_{x\sim P}\!\big[\log D_\omega(x)\big]
-\mathbb{E}_{x\sim Q}\!\big[\log(1-D_\omega(x))\big],
$$

$$
\mathcal{J}(D_\omega)
=\mathbb{E}_{x\sim P}\!\big[\log D_\omega(x)\big]
+\mathbb{E}_{x\sim Q}\!\big[\log(1-D_\omega(x))\big].
$$

Bayes-optimal discriminator (pointwise in \(x\)):

$$
D^*(x)
=\arg\max_{d\in(0,1)}\{\pi_1 p(x)\log d+\pi_0 q(x)\log(1-d)\}
=\frac{\pi_1 p(x)}{\pi_1 p(x)+\pi_0 q(x)}.
$$

................................................
................................................


 Unconditional MLE → KL → Cross-Entropy 


 Empirical data marginal \\(\hat{p}_X(x)\\); model \\(p_\theta(x)\\).

$$
J(\theta)\;=\;\mathbb{E}_{x\sim \hat{p}_X}\big[\log p_\theta(x)\big].
$$

Add–subtract \\(\mathbb{E}_{x\sim \hat{p}_X}\log \hat{p}_X(x)\\) (constant in \\(\theta\\)):

$$
J(\theta)
= \mathbb{E}_{\hat{p}_X}\log \hat{p}_X
\;-\;
\mathbb{E}_{\hat{p}_X}\!\left[\log\frac{\hat{p}_X(x)}{p_\theta(x)}\right]
= \mathbb{E}_{\hat{p}_X}\log \hat{p}_X
\;-\; \mathrm{KL}\!\big(\hat{p}_X \,\|\, p_\theta\big).
$$

Thus \\(\arg\max_\theta J(\theta)=\arg\min_\theta \mathrm{KL}(\hat{p}_X\|p_\theta)\\).

Define cross-entropy and entropy:

$$
H(\hat{p}_X,p_\theta):=-\mathbb{E}_{x\sim \hat{p}_X}\big[\log p_\theta(x)\big],
\qquad
H(\hat{p}_X):=-\mathbb{E}_{x\sim \hat{p}_X}\big[\log \hat{p}_X(x)\big].
$$

Identity linking them:

$$
\mathrm{KL}\!\big(\hat{p}_X \,\|\, p_\theta\big)
= H(\hat{p}_X,p_\theta)-H(\hat{p}_X).
$$

Therefore minimizing \\(\mathrm{KL}(\hat{p}_X\|p_\theta)\\) is equivalent to minimizing the cross-entropy \\(H(\hat{p}_X,p_\theta)\\) (since \\(H(\hat{p}_X)\\) is constant in \\(\theta\\)).


