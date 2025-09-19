---
title: "Discrete Flow Matching-3"
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


For a simple system (fixed $N$), the fundamental identity is:

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
\mathcal{F}(q,\theta) = \mathbb{E}_q[\log q(z)] - \mathbb{E}_q[\log p_\theta(x,z)].
$$

This can be rewritten as:

$$
\mathcal{F}(q,\theta) = \underbrace{-\mathbb{E}_q[\log p_\theta(x,z)]}_{U \;\text{(expected energy)}} \;-\; \underbrace{H[q]}_{TS \;\text{(entropy)}}.
$$

The relation to the log-likelihood is:

$$
\log p_\theta(x) = -\mathcal{F}(q,\theta) + \mathrm{KL}\!\left(q(z)\,\|\,p_\theta(z|x)\right).
$$

\bigskip

\begin{center}
\begin{tabular}{|c|c|}
\hline
\textbf{Physics} & \textbf{Variational Inference (VAE)} \\
\hline
Microstate $s$ & Latent variable $z$ \\
Energy $E(s)$ & $-\log p_\theta(x,z)$ \\
True Gibbs distribution $p(s) \propto e^{-\beta E(s)}$ & True posterior $p_\theta(z|x)$ \\
Trial distribution $q(s)$ & Variational posterior $q_\phi(z|x)$ \\
Free energy functional $\mathbb{E}_q[E] - TS[q]$ & $\mathbb{E}_q[-\log p_\theta(x,z)] - H[q]$ \\
Minimizer $q^*(s)$ is Gibbs distribution & Minimizer $q^*(z)$ is true posterior \\
\hline
\end{tabular}
\end{center}

