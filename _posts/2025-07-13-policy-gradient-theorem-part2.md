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
paper [ Imitating Language via Scalable Inverse Reinforcement Learning](https://arxiv.org/pdf/2409.01369), at times provide a deeper discussion and correct minor mistakes and typoes in that paper. 


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





We drive 4 next as we note there is a "min" missing from right hand side of the equation in the paper: 

$$D_f = \mathbb{E}_{\mu_E}\big[f\!\left(\tfrac{\mu_\pi(s,a)}{\mu_E(s,a)}\right)\big]$$ 

 Start from the definition. For the reverse KL with \\(f(t)=-\log t\\),

$$
D_{f_{\mathrm{RKL}}}(\mu_\pi\|\mu_E)
= \mathbb{E}_{(s,a)\sim \mu_E}\!\left[-\log\frac{\mu_\pi(s,a)}{\mu_E(s,a)}\right]
= \mathrm{KL}(\mu_E\|\mu_\pi).
$$

Factorize occupancy as 

$$\mu_\pi(s,a)=\rho_\pi(s)\pi(a|s),$$

\\(\mu_E(s,a)=\rho_E(s)\pi_E(a|s)\\).

Plugging in gives

$$
\mathrm{KL}(\mu_E\|\mu_\pi)
= \sum_{s,a}\rho_E(s)\pi_E(a|s)\log\frac{\rho_E(s)\pi_E(a|s)}{\rho_\pi(s)\pi(a|s)}.
$$

Separate terms inside the log:

$$
\log\frac{\rho_E(s)}{\rho_\pi(s)} + \log\frac{\pi_E(a|s)}{\pi(a|s)}.
$$

Collapse each sum:

$$
\sum_s \rho_E(s)\log\frac{\rho_E(s)}{\rho_\pi(s)} = \mathrm{KL}(\rho_E\|\rho_\pi),
$$

$$
\sum_{s,a}\rho_E(s)\pi_E(a|s)\log\frac{\pi_E(a|s)}{\pi(a|s)}
= \sum_s \rho_E(s)\,\mathrm{KL}(\pi_E(\cdot|s)\|\pi(\cdot|s)).
$$

Put it together. For any fixed policy \\(\pi\\),

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


Next in 5 note that we have pointwise fucntion of state and action:

$$
D(s,a) = -\mathbb{E}_{\mu_E}\!\big[f^{*}(g(s,a))\big] \;+\; \mathbb{E}_{\mu_\pi}\!\big[g(s,a)\big],
$$

such that The variational representation requires that the discriminator function \\(g:S\times A \to \mathbb{R}\\) satisfies

$$
g(s,a) \in \operatorname{effdom}(f^{*})
\quad \text{for all } (s,a)\in S\times A,
$$

where the effective domain of the convex conjugate is defined as

$$
\operatorname{effdom}(f^{*}) := \{\, y \in \mathbb{R} \;\mid\; f^{*}(y) < \infty \,\}.
$$

In particular, \\(g\\) will include functions that are "nearly onto", except at infinite values. Then 

$$
D_f(s,a) = \sup_{g: S\times A \to \operatorname{effdom}(f^{*})} D(s,a;g) 
$$


Next note there is a typo in 9 and the second term on the right should be discounted by \\(\gamma\\) not by \\(\lambda\\). We prove it below: 

**Claim (soft Bellman optimality identity).** For any \\(\lambda>0\\) and discount \\(\gamma\in(0,1)\\), the soft-optimal pair \\((v_r,\pi_r)\\) for reward \\(r\\) satisfies, for all \\(s\in S, a\in A\\),

$$
r(s,a)\;-\;\lambda \log \pi_r(a\mid s)
\;=\;
v_r(s)\;-\;\gamma\,\mathbb{E}_{s'\sim P(\cdot\mid s,a)}[\,v_r(s')\,].
$$

**Proof.** Define the soft state value and state–action value as

$$
Q_r(s,a)\;:=\;r(s,a)\;+\;\gamma\,\mathbb{E}_{s'\sim P(\cdot\mid s,a)}[\,v_r(s')\,],
\qquad
v_r(s)\;:=\;\max_{\pi(\cdot\mid s)}\;\mathbb{E}_{a\sim \pi(\cdot\mid s)}\!\big[\,Q_r(s,a)\;-\;\lambda \log \pi(a\mid s)\,\big].
$$

The maximization is strictly concave in \\(\pi(\cdot\mid s)\\), yielding the Gibbs optimum

$$
\pi_r(a\mid s)\;=\;\frac{\exp(Q_r(s,a)/\lambda)}{\sum_{b}\exp(Q_r(s,b)/\lambda)},
\qquad
v_r(s)\;=\;\lambda \log \sum_{b}\exp\!\big(Q_r(s,b)/\lambda\big).
$$






(proof: Fix a state \\(s\\), discount \\(\gamma\in[0,1)\\), temperature \\(\lambda>0\\), and a value function \\(v:S\to\mathbb{R}\\).
Define the soft state–action score as above 


$$
Q(s,a) := r(s,a) + \gamma\,\mathbb{E}_{s'\sim P(\cdot\mid s,a)}[\,v(s')\,].
$$

We maximize over the simplex \\(\Delta(\mathcal{A})\\) the per-state objective

$$
F_s(\pi(\cdot\mid s)) \;=\; \sum_a \pi(a\mid s)\,Q(s,a) \;+\; \lambda\,H\!\big(\pi(\cdot\mid s)\big),
$$

where \\(H(\pi) = -\sum_a \pi(a)\log \pi(a)\\).

Strict concavity: The entropy \\(H\\) is strictly concave on the probability simplex, and
\\(\sum_a \pi(a\mid s)Q(s,a)\\) is linear. Hence \\(F_s\\) is strictly concave. Equivalently, on the
interior \\(\{\pi(a\mid s)>0\}\\), the Hessian is

$$
\frac{\partial^2 F_s}{\partial \pi(a\mid s)\,\partial \pi(b\mid s)}
\;=\; -\,\lambda\,\frac{\delta_{ab}}{\pi(a\mid s)},
$$

which is negative definite on the tangent subspace \\(\{x:\sum_a x_a=0\}\\). Therefore the maximizer is unique and lies in the interior (so \\(\pi(a\mid s)>0\\) for all \\(a\\)).

Gibbs optimum via KKT.
Introduce the Lagrangian with multiplier \\(\alpha_s\\) for \\(\sum_a \pi(a\mid s)=1\\):

$$
\mathcal{L}(\pi,\alpha_s)
= \sum_a \pi(a\mid s)Q(s,a) + \lambda\Big(-\sum_a \pi(a\mid s)\log \pi(a\mid s)\Big)
+ \alpha_s\Big(\sum_a \pi(a\mid s)-1\Big).
$$

Stationarity gives, for each \\(a\\),

$$
0 \;=\; \frac{\partial \mathcal{L}}{\partial \pi(a\mid s)}
= Q(s,a) - \lambda(\log \pi(a\mid s)+1) + \alpha_s.
$$

Thus

$$
\log \pi(a\mid s) \;=\; \frac{Q(s,a)}{\lambda} + \frac{\alpha_s-\lambda}{\lambda}
\;\;\Longrightarrow\;\;
\pi(a\mid s) \;=\; \frac{\exp\!\big(Q(s,a)/\lambda\big)}{\sum_{b}\exp\!\big(Q(s,b)/\lambda\big)}.
$$

This is the (unique) Gibbs/softmax optimum.


Taking logs and rearranging (soft Bellman identities).

Let

$$
v(s) \;:=\; \lambda \log \sum_{b}\exp\!\big(Q(s,b)/\lambda\big)
\quad\Longrightarrow\quad
\log \pi(a\mid s) \;=\; \frac{Q(s,a) - v(s)}{\lambda}.
$$

Substitute \\(Q(s,a)=r(s,a)+\gamma\,\mathbb{E}[v(s')\mid s,a]\\) to obtain, for all \\((s,a)\\),

$$
r(s,a) \;-\; \lambda \log \pi(a\mid s)
\;=\; v(s) \;-\; \gamma\,\mathbb{E}_{s'\mid s,a}[\,v(s')\,],
$$

which is the soft Bellman optimality relation. (Equivalently, \\(v(s)=\lambda\log\sum_a \exp(Q(s,a)/\lambda)\\).)

Note: If \\(\lambda=0\\), \\(F_s\\) reduces to a linear program and the maximizer is the deterministic argmax of \\(Q(s,\cdot)\\); strict concavity and the Gibbs form require \\(\lambda>0\\).
)





Taking logs of \\(\pi_r\\) and rearranging,

$$
-\lambda \log \pi_r(a\mid s)\;=\;v_r(s)\;-\;Q_r(s,a).
$$
Substitute \\(Q_r(s,a)=r(s,a)+\gamma\,\mathbb{E}_{s'}[v_r(s')]\\) to get

$$
r(s,a)\;-\;\lambda \log \pi_r(a\mid s)\;=\;v_r(s)\;-\;\gamma\,\mathbb{E}_{s'}[v_r(s')],
$$

as claimed. 

Note equaltiy 9 is used twice once in 10 and again in 12. 























## A telescoping argument

Let's first remind ourselves of our notation: 

Finite MDP, discount \\(\gamma \in [0,1)\\), initial state law \\(d_0\\), kernel \\(P(s' \mid s,a)\\).  
For a stationary policy \\(\pi\\),

$$
\tilde\mu_\pi(s,a) := \sum_{t=0}^\infty \gamma^t \Pr_{\pi}(s_t=s,a_t=a),
\qquad
\tilde\rho_\pi(s) := \sum_a \tilde\mu_\pi(s,a) = \sum_{t=0}^\infty \gamma^t \Pr{\pi}(s_t=s).
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

**Proof (time-unrolling).**

$$
\sum_{s,a}\tilde\mu_\pi(s,a)\Psi_w(s,a)
= \sum_{t=0}^\infty \gamma^t\,\mathbb{E}_\pi\!\big[w(s_t) - \gamma\,\mathbb{E}[w(s_{t+1})\mid s_t,a_t]\big].
$$

Apply the tower property (law of total expectation):

$$
\mathbb{E}_\pi\!\big[\mathbb{E}[w(s_{t+1})\mid s_t,a_t]\big]
= \mathbb{E}_\pi[w(s_{t+1})].
$$

$$
\sum_{t=0}^\infty \gamma^t\big(\mathbb{E}_\pi[w(s_t)] - \gamma\,\mathbb{E}_\pi[w(s_{t+1})]\big).
$$

Shift index in the second series (\\(u=t+1\\)):

$$
\mathbb{E}_\pi[w(s_0)] + \sum_{t=1}^\infty \gamma^t\mathbb{E}_\pi[w(s_t)]
- \sum_{u=1}^\infty \gamma^u\mathbb{E}_\pi[w(s_u)]
= \mathbb{E}_{s_0\sim d_0}[w(s_0)].
$$

No \\(\pi\\) remains.

fianlly note in using soft Bellman optimality to drive 13, there is a typo; the correct expression is

$$
r_{(v_r,\pi_r)}(s,a) = v_r(s)+ \lambda\,\log \pi_r(a\mid s) - \mathbb{E}_{s'\sim P(\cdot\mid s,a)}\!\big[\,\gamma\, v_r(s')\,\big]
$$

