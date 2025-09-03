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

$$
H(\pi) \;=\; - \sum_{s} d_\pi(s) \sum_{a} \pi(a \mid s)\,\log \pi(a \mid s),
$$

where \\(d_\pi(s)\\) is the (discounted) state visitation distribution under policy \\(\pi\\).
This weighting means the entropy contribution of each state is proportional
to how often that state is encountered when following the policy.
Thus, the regularizer encourages stochasticity in states that actually occur
along trajectories, rather than uniformly across all states.







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

