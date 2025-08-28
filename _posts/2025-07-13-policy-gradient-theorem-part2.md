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

## 1. Objective: Maximizing Expected Return

 policy \\(\pi_\theta\\):

$$
\begin{align}
\nabla_\theta J(\pi_\theta)
&= \mathbb{E}_{\tau\sim\pi_\theta}\Bigl[\sum_{t=0}^H \gamma^t\nabla_\theta\log\pi_\theta(a_t\!\mid\!s_t)\,R_{t:H}(\tau)\Bigr]\\
&= \sum_{t=0}^H \mathbb{E}_{\tau}\bigl[\gamma^t\nabla_\theta\log\pi_\theta(a_t\!\mid\!s_t)\,R_{t:H}(\tau)\bigr].
\end{align}
$$


$$
J(\theta) = \mathbb{E}_{\tau \sim \pi_\theta}[R(\tau)], \quad R(\tau) = \sum_{t=0}^H \gamma^t r(s_t,a_t).
$$

Here, \\(\tau=(s_0,a_0,\dots,s_H,a_H)\\) is a trajectory, and \\(\gamma\in[0,1]\\) is the discount factor.

## 2. Applying the Log-Derivative Trick

$$
\nabla_{\theta}J(\theta)
= \nabla_{\theta}\,\mathbb{E}_{\tau\sim\pi_{\theta}}\bigl[R(\tau)\bigr]
= \mathbb{E}_{\tau\sim\pi_{\theta}}\!\bigl[\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)\,R(\tau)\bigr].
$$

proof of the trick:

\begin{equation}
\nabla_\theta p(\tau;\theta)
= p(\tau;\theta)\,\nabla_\theta \log p(\tau;\theta)
\end{equation}

$$
\begin{align}
\nabla_\theta \mathbb{E}_{\tau\sim\pi_\theta}[R(\tau)]
&= \nabla_\theta \int p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \int \nabla_\theta p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \int p_{\pi_\theta}(\tau)\,\nabla_\theta \log p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \mathbb{E}_{\tau\sim\pi_\theta}\bigl[\nabla_\theta \log p_{\pi_\theta}(\tau)\,R(\tau)\bigr] \\
&= \nabla_\theta J(\theta)\,.
\end{align}
$$




Decompose  \\(\log p_{\pi_{\theta}}(\tau)\\)

$$
p(\tau)
= \rho_0(s_0)\,\prod_{t=0}^{H}\pi_{\theta}(a_t\mid s_t)\,P(s_{t+1}\mid s_t,a_t),
$$

so

$$
\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)
= \sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t).
$$

Plug in:

$$
\nabla_{\theta}J(\theta)
= \mathbb{E}_{\tau}\Bigl[\Bigl(\sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\Bigr)\,R(\tau)\Bigr]
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\bigl[\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,R(\tau)\bigr].
$$


## 3. Introducing the Reward-to-Go (Causality)

Only future rewards depend on \\(a_t\\). Dropping past rewards:
(mathematically if you write out the integral terms that do not depend on the integrator 
factor out and you are left with the gradient of integral of a probability which is the constant 1 and hence zero)

$$
\nabla_{\theta}J(\theta)
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\underbrace{\sum_{t'=t}^{H}\gamma^{t'}r(s_{t'},a_{t'})}_{\text{reward‐to‐go}}\Bigr].
$$

Factor out \\(\gamma^t\\):

$$

= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\gamma^t\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'})\Bigr].
$$

Add a Baseline (As to what a baseline is and why we subtract see pp 329 of Sutton, but here we give the proof)}

Subtract a baseline \\(b(s_t)\\):
$$
\nabla_{\theta}J(\theta)
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\gamma^t\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\Bigl(\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'}) - b(s_t)\Bigr)\Bigr].
$$


proof that you can subtract the base so long as it is a function of state alone:


$$
\begin{align*}
\nabla_{\theta} J(\pi_{\theta}) 
&= \mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
\sum_{t=0}^{H}\gamma^{t}\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\left(\sum_{t'=t}^{H}\gamma^{t'-t}r(s_{t'},a_{t'}) - b(s_{t})\right)
\right]
\end{align*}
$$

We explicitly verify that the baseline is unbiased:

$$
\begin{align*}
&\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
\sum_{t=0}^{H}\gamma^{t}\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t}) b(s_{t})
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t})\mathbb{E}_{a_{t}\sim\pi_{\theta}(a_{t}|s_{t})}\left[\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\middle|s_{t}\right]
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t})\int_{a_t}\pi_{\theta}(a_{t}|s_{t})\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\,da_{t}
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t})\int_{a_t}\nabla_{\theta}\pi_{\theta}(a_{t}|s_{t})\,da_{t}
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t}) \cdot 0
\right] \\
&\quad= 0
\end{align*}
$$



Erratum note: finite‐ to infinite‐horizon carry–over typo

In the finite‐horizon derivation we write
$$
\nabla_{\theta}
= \sum_{t=0}^{H}
\mathbb{E}_{s_{t}\sim d_{t}^{\pi},\,a_{t}\sim\pi_{\theta}}
\bigl[\gamma^{t}\,\nabla_{\theta}\log\pi_{\theta}(a_{t}\mid s_{t})\,\hat{A}(s_{t},a_{t})\bigr].
$$

Here both the state‐distribution (\\(d_{t}^{\pi}\\) and the discount weight \\(\gamma^{t}\\) are explicitly indexed by \\(t\\).

When passing to the infinite‐horizon form, those two pieces are folded into a single “discounted occupancy” measure

$$
d^{\pi}(s)\;=\;(1-\gamma)\sum_{t=0}^{\infty}\gamma^{t}\,d_{t}^{\pi}(s),
$$

We can now absorb the discount factor and the sum into the measure \\(d_{t}^{\pi}(s)\\) (tutorial characterizes this as "dropping" the discount factor but this is not really an accurate description)  so that the gradient can be written as

$$
\nabla_{\theta}J
= \frac{1}{1-\gamma}\,
\mathbb{E}_{s\sim d^{\pi},\,a\sim\pi_{\theta}}
\bigl[\nabla_{\theta}\log\pi_{\theta}(a\mid s)\,\hat{A}(s,a)\bigr].
$$

The stray subscript “\\(_t\\)” on the state distribution in the infinite‐horizon equation was simply a leftover from the finite‐horizon version. The correct infinite‐horizon line should read


$$
\nabla_{\theta}J
= \frac{1}{1-\gamma}\,
\mathbb{E}_{s\sim d^{\pi}(s),\,a\sim\pi_{\theta}(a\mid s)}
\bigl[\nabla_{\theta}\log\pi_{\theta}(a\mid s)\,\hat{A}(s,a)\bigr],
$$

with no time index on \\(d^{\pi}\\).

While a simple time-dependent baseline \\(b_t = \frac{1}{N} \sum_{i=1}^N R_{i,t}\\) is often used to reduce variance in Monte Carlo policy gradient estimators, it does not represent a true value function \\(V(s_t) = \mathbb{E}[R_t \mid s_t]\\). This is because \\(b_t\\) marginalizes over all states \\(s_t\\) encountered at time \\(t\\) rather than conditioning on a specific state. Thus, it captures only the average return under the state visitation distribution \\(d_t(s)\\), not the expected return from a particular state \\(s_t = s\\). Although useful for variance reduction, this approach lacks the precision and generalization capabilities of a learned, state-dependent baseline.

% Insert this snippet into your document body (no \documentclass or \begin{document}/\end{document})
% Inline math uses \\( ... \\) and displayed equations use $$ ... $$ with a blank line before and after each $$ ... $$.

This note derives the pointwise soft-Bellman identity

$$
r(s,a)-\lambda\log\pi_r(a\mid s)=v_r(s)-\gamma\mathbb{E}_{s'\mid s,a}[v_r(s')]
\qquad\forall s,a,
$$

points out the common typo (confusing \\( \lambda \\) and the discount \\( \gamma \\)), and shows how multiplying the identity by the discounted occupancy \\( \mu_r \\) recovers the global return identity.

Setup and notation. Let an MDP be given by state space \\( \mathcal S \\), action space \\( \mathcal A \\), transition kernel \\( P(s'|s,a) \\), discount \\( \gamma\in[0,1) \\), and per-step reward \\( r:\mathcal S\times\mathcal A\to\mathbb R \\). Fix entropy coefficient \\( \lambda>0 \\).

Define the entropy-regularized return

$$
J(\pi)=\mathbb{E}\!\Big[\sum_{t=0}^{\infty}\gamma^t\big(r(s_t,a_t)-\lambda\log\pi(a_t\mid s_t)\big)\Big],
$$

and the soft value and soft-Q functions

$$
V_\pi(s)=\mathbb{E}_\pi\Big[\sum_{t=0}^\infty \gamma^t\big(r(s_t,a_t)-\lambda\log\pi(a_t\mid s_t)\big)\Bigm| s_0=s\Big],
$$

$$
Q_\pi(s,a)=r(s,a)-\lambda\log\pi(a\mid s)+\gamma\,\mathbb{E}_{s'\sim P(\cdot\mid s,a)}[V_\pi(s')].
$$

Use the discounted occupancy normalized to sum to one:

$$
\mu_\pi(s,a)=(1-\gamma)\sum_{t=0}^\infty \gamma^t \Pr(s_t=s,a_t=a\mid\pi),
$$

so that expectations with respect to \\( \mu_\pi \\) are discounted time averages:

$$
\mathbb{E}_{(s,a)\sim\mu_\pi}[\cdot]=(1-\gamma)\sum_{t=0}^\infty \gamma^t \mathbb{E}[\cdot\ \text{at time }t].
$$

1) Pointwise soft-Bellman optimality (proof).

Fix a reward \\( r \\). Let \\( \pi_r \\) be an optimal policy for the entropy-regularized objective and write \\( v_r=V_{\pi_r} \\), \\( Q_r=Q_{\pi_r} \\).

For any state \\( s \\) define

$$
q(s,a):=r(s,a)+\gamma\mathbb{E}_{s'\mid s,a}[v_r(s')].
$$

The per-state contribution is

$$
\mathcal G_s(\pi(\cdot\mid s))=\sum_{a}\pi(a\mid s)\big(q(s,a)-\lambda\log\pi(a\mid s)\big),
$$

and the optimal policy at \\( s \\) maximizes \\( \mathcal G_s \\) subject to \\( \sum_a\pi(a\mid s)=1 \\). Form the Lagrangian with multiplier \\( \eta(s) \\):

$$
\mathcal L=\sum_a\pi(a)\big(q(s,a)-\lambda\log\pi(a)\big)-\eta\Big(\sum_a\pi(a)-1\Big).
$$

Stationarity with respect to \\( \pi(a) \\) gives

$$
q(s,a)-\lambda(1+\log\pi_r(a\mid s))-\eta(s)=0,
$$

hence

$$
\log\pi_r(a\mid s)=\frac{q(s,a)-\eta(s)-\lambda}{\lambda}.
$$

Normalizing yields the Gibbs policy

$$
\pi_r(a\mid s)=\frac{\exp(q(s,a)/\lambda)}{\sum_{a'}\exp(q(s,a')/\lambda)},
$$

and substituting back gives the state value

$$
v_r(s)=\lambda\log\sum_a\exp\!\bigl(q(s,a)/\lambda\bigr).
$$

From \\( \lambda\log\pi_r(a\mid s)=q(s,a)-v_r(s) \\) and \\( q(s,a)=r(s,a)+\gamma\mathbb{E}_{s'|s,a}[v_r(s')] \\) we obtain the soft-Bellman identity:

$$
r(s,a)-\lambda\log\pi_r(a\mid s)=v_r(s)-\gamma\mathbb{E}_{s'|s,a}[v_r(s')]\qquad\forall s,a.
$$

Remark: the multiplier on \\( v_r(s') \\) is the discount \\( \gamma \\). If you see \\( \lambda v_r(s') \\) instead, that is a typo.

2) Where \\( \mu_r \\) appears: integrating the pointwise identity.

The pointwise identity is local and does not mention \\( \mu_r \\). Multiply both sides by \\( \mu_r(s,a) \\) and sum/integrate over \\( (s,a) \\):

$$
\sum_{s,a}\mu_r(s,a)\bigl(r(s,a)-\lambda\log\pi_r(a\mid s)\bigr)
= \sum_{s,a}\mu_r(s,a)\Bigl(v_r(s)-\gamma\mathbb{E}_{s'|s,a}[v_r(s')]\Bigr).
$$

Compute the right-hand side terms:

$$
\sum_{s,a}\mu_r(s,a)\,v_r(s)=(1-\gamma)\sum_{t\ge0}\gamma^t \mathbb{E}[v_r(s_t)],
$$

and

$$
\sum_{s,a}\mu_r(s,a)\mathbb{E}_{s'|s,a}[v_r(s')]
=(1-\gamma)\sum_{t\ge0}\gamma^t\mathbb{E}[v_r(s_{t+1})]
=(1-\gamma)\sum_{t\ge1}\gamma^{t-1}\mathbb{E}[v_r(s_t)].
$$

Hence the difference telescopes to

$$
\sum_{s,a}\mu_r(s,a)\Bigl(v_r(s)-\gamma\mathbb{E}_{s'|s,a}[v_r(s')]\Bigr)=(1-\gamma)\mathbb{E}[v_r(s_0)].
$$

Therefore

$$
\sum_{s,a}\mu_r(s,a)\bigl(r(s,a)-\lambda\log\pi_r(a\mid s)\bigr)=(1-\gamma)\,\mathbb{E}_{s_0\sim d_0}[v_r(s_0)],
$$

where \\( d_0 \\) is the initial-state distribution used in the trajectory expectation.

Since

$$
\mathbb{E}_{(s,a)\sim\mu_r}[r-\lambda\log\pi_r]=(1-\gamma)J(\pi_r),
$$

we recover \\( J(\pi_r)=\mathbb{E}_{s_0}[v_r(s_0)] \\) as usual.

Summary (plain English).

\\( \bullet \\) The pointwise identity is the soft (entropy-regularized) Bellman optimality condition; it is local and does not require mentioning \\( \mu_r \\).

\\( \bullet \\) The discounted occupancy \\( \mu_r \\) naturally appears when integrating the pointwise identity to yield expectation-level equalities and the connection to \\( J(\pi) \\).

\\( \bullet \\) Watch for typographical errors: the multiplier on the next-state value must be \\( \gamma \\), not \\( \lambda \\).




