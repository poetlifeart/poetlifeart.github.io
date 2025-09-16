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
