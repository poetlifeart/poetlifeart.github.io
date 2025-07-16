<!-- Load MathJax so LaTeX renders in GitHub Pages without touching layouts -->
<script>
  window.MathJax = {
    tex: {
      inlineMath: [['\\(','\\)'], ['\\[','\\]']]
    }
  };
</script>
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

---
title: "Deriving the Policy Gradient Theorem"
date: 2025-07-13
tags:
  - reinforcement learning
  - policy gradient
  - mathematics
layout: post
---

In this post, we walk through the step-by-step derivation of the **Policy Gradient Theorem**, highlighting the log-derivative trick, reward-to-go (causal) formulation, and the incorporation of a baseline for variance reduction. All equations are rendered with MathJax.

## 1. Objective: Maximizing Expected Return

We aim to maximize the expected return under a parameterized policy \\(\pi_\theta\\):

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

Only future rewards \\(r_{t'}\\) for \\(t'\ge t\\) depend on \\(a_t\\), so we replace the full return with the **reward-to-go**:

$$
R_{t:\,H}(\tau) = \sum_{t'=t}^H \gamma^{t'-t} r(s_{t'},a_{t'}).
$$

The gradient becomes:

$$
\nabla_\theta J(\theta)
= \sum_{t=0}^H \mathbb{E}_{\tau}\bigl[\gamma^t \nabla_\theta\log\pi_\theta(a_t\mid s_t)\,R_{t:\,H}(\tau)\bigr].
$$

## 4. Subtracting a Baseline for Variance Reduction

To reduce variance without bias, subtract a state-dependent baseline \\(b(s_t)\\):

$$
\nabla_\theta J(\theta)
= \sum_{t=0}^H \mathbb{E}_{\tau}\Bigl[\gamma^t \nabla_\theta\log\pi_\theta(a_t\mid s_t)
\bigl(R_{t:\,H}(\tau) - b(s_t)\bigr)\Bigr].
$$

One can show:
$$
\mathbb{E}_{\tau}[\nabla_\theta\log\pi_\theta(a_t\mid s_t)\,b(s_t)] = 0,
$$
so the estimator remains unbiased.

## 5. Infinite-Horizon Form and Discounted Occupancy

Defining the **discounted state occupancy**:

$$
d_{\pi}(s) = (1-\gamma)\sum_{t=0}^\infty \gamma^t d^\pi_t(s),
$$

we obtain the infinite-horizon gradient:

$$
\nabla_\theta J(\theta)
= \frac{1}{1-\gamma} \mathbb{E}_{s\sim d_{\pi},\,a\sim\pi_\theta}
[\nabla_\theta\log\pi_\theta(a\mid s)\,A^\pi(s,a)],
$$

where \\(A^\pi(s,a)\\) is the advantage function estimate.


