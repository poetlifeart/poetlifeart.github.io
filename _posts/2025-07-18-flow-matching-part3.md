---
title: "Free Energy minimization in VAEs"
date: 2025-07-18
categories:
  - flow-matching
permalink: /flow-matching-theorem/part-3/    
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


For a simple system (fixed number of particles), the fundamental identity is:

$$
dU = T\,dS - p\,dV.
$$

Euler's homogeneous theorem gives the integrated form:

$$
U = TS - pV.
$$

The Helmholtz free energy is defined as:

$$
F = U - TS = -pV.
$$

In statistical mechanics, the Helmholtz free energy has a variational characterization:

$$
F = \min_q \Big( \mathbb{E}_q[E] - T S[q] \Big),
$$

where

$$
\mathbb{E}_q[E] = \sum_s q(s)\,E(s), \qquad S[q] = - \sum_s q(s)\,\log q(s).
$$

At the minimizer,

$$
q^*(s) = \frac{e^{-\beta E(s)}}{Z}, \qquad Z = \sum_s e^{-\beta E(s)},
$$

we recover the Gibbs (Boltzmann) distribution. At this point,

$$
\mathbb{E}_{q^*}[E] = U, \qquad \mathcal{F}_\beta[q^*] = F.
$$

For latent-variable models, the variational free energy is defined as:

$$
\mathcal{F}(q,\theta) = \mathbb{E}_q[\log q(z)] - \mathbb{E}_{z~(q|x)}[\log p_\theta(x,z)].
$$

This can be rewritten as:

$$
\mathcal{F}(q,\theta) = \underbrace{-\mathbb{E}_q[\log p_\theta(x,z)]}_{U \;\text{(expected energy)}} \;-\; \underbrace{H[q]}_{TS \;\text{(entropy)}}.
$$

The relation to the log-likelihood is:

$$
\log p_\theta(x) = -\mathcal{F}(q,\theta) + \mathrm{KL}\!\left(q(z)\,\|\,p_\theta(z|x)\right).
$$

Thus, in physics the minimizer of the free energy functional is the Gibbs distribution, while in VAEs the minimizer is the true posterior. In both cases, the free energy principle provides a way to define a functional over trial distributions 
\\( q \\) and recover the correct distribution at the optimum. When \\(q\\) is restricted (e.g. Gaussian in VAEs, or mean-field in physics), one obtains the best approximation within that family rather than the exact solution.

Note that in physics, one might choose a \\(q\\) which spreads probability more uniformly over energy states (and toward higher
energy states), thus increasing the entropy term \\(S[q]\\). 
However, such a choice generally increases the expected energy 

$$\mathbb{E}_q[E]$$ 

by allowing high energy states to receive high probabilities and therefore increases the free energy. 

The unique compromise that minimizes the free energy is the Gibbs distribution, 
which represents true thermodynamic equilibrium.

In the VAE setting, increasing the entropy of \\(q_\phi(z|x)\\) means that the distribution 
cannot selectively place higher weight on latent values \\(z\\) where the joint probability 
\\(p_\theta(x,z)\\) is large. 
As a result, the expectation 

$$\mathbb{E}_q[\log p_\theta(x,z)]$$

decreases, its negative 

$$-\mathbb{E}_q[\log p_\theta(x,z)]$$
 
increases,  and the free energy \\(\mathcal{F}(q,\theta)\\) increases. 


...................

$$
\mathcal{L}(x;\theta,\phi) 
= \mathbb{E}_{z \sim q_\phi(z|x)} \big[ \log p_\theta(x \mid z) \big] 
- D_{\text{KL}}\!\big(q_\phi(z|x)\,\|\,p(z)\big).
$$

$$
\mathbb{E}_{z \sim q_\phi(z|x)} [\log p_\theta(x|z)]
$$

- The decoder is trained to give high probability to the original \\(x\\) for the \\(z\\) sampled from the encoder.  
- Hence it learns to decode only those \\(z\\) that the encoder actually produces.  

$$
D_{\text{KL}}(q_\phi(z|x)\,\|\,p(z))
$$

- Keeps the encoder’s distribution close to the prior \\(p(z)\\), usually Gaussian noise.  
- Forces the encoder to only encode the information about \\(x\\) that is truly necessary to reconstruct it.  
- Suppresses spurious assumptions or redundant details, since these would push 

$$q_\phi(z|x)$$

away from the prior and increase the KL cost.  

