---
title: "Offline RL-2"
date: 2025-07-13
categories:
  - policy-gradient
permalink: /policy-gradient-theorem/part-2/  
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


In this post, we walk through the step-by-step derivations of some of the math in the 
paper [ Offline Reinforcement Learning: Tutorial, Review, and Perspectives on Open Problems](https://arxiv.org/abs/2005.01643), at times provide a deeper discussion and correct minor mistakes and typoes in that paper. 


Starting on page 3. 

$$
H(\pi) \;=\; - \sum_{s} d_\pi(s) \sum_{a} \pi(a \mid s)\,\log \pi(a \mid s),
$$

where \\(d_\pi(s)\\) is the (discounted) state visitation distribution under policy \\(\pi\\).
The weighting by \\(d_\pi(s)\\) ensures that each state's contribution to the entropy
is proportional to how often it is actually encountered under the policy.
In this sense, the regularizer promotes stochasticity in states along real trajectories,
rather than uniformly across all states.
The term *weighted* refers to the state-distribution weighting, while *causal* highlights
that these states are reached through the forward dynamics induced by the policy.





where 

$$D_f = \mathbb{E}_{\mu_E}\big[f\!\left(\tfrac{\mu_\pi(s,a)}{\mu_E(s,a)}\right)\big]$$ 

1. Start from the definition. For the reverse KL with \\(f(t)=-\log t\\),

$$
D_{f_{\mathrm{RKL}}}(\mu_\pi\|\mu_E)
= \mathbb{E}_{(s,a)\sim \mu_E}\!\left[-\log\frac{\mu_\pi(s,a)}{\mu_E(s,a)}\right]
= \mathrm{KL}(\mu_E\|\mu_\pi).
$$

2. Factorize occupancy as \\(\mu_\pi(s,a)=\rho_\pi(s)\pi(a|s)\\), 
\\(\mu_E(s,a)=\rho_E(s)\pi_E(a|s)\\).

3. Plugging in gives

$$
\mathrm{KL}(\mu_E\|\mu_\pi)
= \sum_{s,a}\rho_E(s)\pi_E(a|s)\log\frac{\rho_E(s)\pi_E(a|s)}{\rho_\pi(s)\pi(a|s)}.
$$

4. Separate terms inside the log:

$$
\log\frac{\rho_E(s)}{\rho_\pi(s)} + \log\frac{\pi_E(a|s)}{\pi(a|s)}.
$$

5. Collapse each sum:

$$
\sum_s \rho_E(s)\log\frac{\rho_E(s)}{\rho_\pi(s)} = \mathrm{KL}(\rho_E\|\rho_\pi),
$$

$$
\sum_{s,a}\rho_E(s)\pi_E(a|s)\log\frac{\pi_E(a|s)}{\pi(a|s)}
= \sum_s \rho_E(s)\,\mathrm{KL}(\pi_E(\cdot|s)\|\pi(\cdot|s)).
$$

6. Put it together. For any fixed policy \\(\pi\\),

$$
D_{f_{\mathrm{RKL}}}(\mu_\pi\|\mu_E)
= \mathrm{KL}(\rho_E\|\rho_\pi)
+ \mathbb{E}_{s\sim\rho_E}[\,\mathrm{KL}(\pi_E(\cdot|s)\|\pi(\cdot|s))\,].
$$

Therefore

$$
\min_\pi D_{f_{\mathrm{RKL}}}(\mu_\pi\|\mu_E)
= \min_\pi\Big\{
\mathrm{KL}(\rho_E\|\rho_\pi)
+ \mathbb{E}_{s\sim\rho_E}[\,\mathrm{KL}(\pi_E(\cdot|s)\|\pi(\cdot|s))\,]
\Big\}.
$$






## 1.

 Finite MDP, discount \\(\gamma \in [0,1)\\), initial state law \\(d_0\\), kernel \\(P(s' \mid s,a)\\).  
For a stationary policy \\(\pi\\),

$$
\tilde\mu_\pi(s,a) := \sum_{t=0}^\infty \gamma^t \Pr_\pi(s_t=s,a_t=a),
\qquad
\tilde\rho_\pi(s) := \sum_a \tilde\mu_\pi(s,a) = \sum_{t=0}^\infty \gamma^t \Pr_\pi(s_t=s).
$$

So \\(\tilde\mu_\pi(s,a) = \tilde\rho_\pi(s)\,\pi(a\mid s)\\).

Define, for any bounded \\(w:S\to\mathbb{R}\\),

$$
\Psi_w(s,a) := w(s) - \gamma \sum_{s'} P(s'|s,a)w(s').
$$

**Claim.** For any stationary \\(\pi\\) and any bounded \\(w\\),

$$
\sum_{s,a}\tilde\mu_\pi(s,a)\Psi_w(s,a)
= \mathbb{E}_{s_0\sim d_0}[w(s_0)].
$$

In particular, the value is independent of \\(\pi\\).

**Proof A (time-unrolling).**

$$
\sum_{s,a}\tilde\mu_\pi(s,a)\Psi_w(s,a)
= \sum_{t=0}^\infty \gamma^t\,\mathbb{E}_\pi\!\big[w(s_t) - \gamma\,\mathbb{E}[w(s_{t+1})\mid s_t,a_t]\big].
$$

Since \\(\mathbb{E}[w(s_{t+1})\mid s_t,a_t] = \mathbb{E}_\pi[w(s_{t+1})]\\), this is

$$
\sum_{t=0}^\infty \gamma^t\big(\mathbb{E}_\pi[w(s_t)] - \gamma\,\mathbb{E}_\pi[w(s_{t+1})]\big).
$$

Shift index in the second series (\\(u=t+1\\)):

$$
\mathbb{E}_\pi[w(s_0)] + \sum_{t=1}^\infty \gamma^t\mathbb{E}_\pi[w(s_t)]
- \sum_{u=1}^\infty \gamma^u\mathbb{E}_\pi[w(s_u)]
= \mathbb{E}_{s_0\sim d_0}[w(s_0)].
$$

No \\(\pi\\) remains. \\(\square\\)

