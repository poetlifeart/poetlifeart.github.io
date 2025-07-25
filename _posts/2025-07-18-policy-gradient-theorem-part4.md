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


In this post, we walk through the step-by-step derivations of some of the math in the 
paper [ Offline Reinforcement Learning: Tutorial, Review, and Perspectives on Open Problems](https://arxiv.org/abs/2005.01643), at times provide a deeper discussion and correct minor mistakes and typoes in that paper. 




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




