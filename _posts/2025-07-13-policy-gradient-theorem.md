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


\bigskip

\noindent\textbf{ Decompose \(\log p_{\pi_{\theta}}(\tau)\):}

$$
p(\tau)
= \rho_0(s_0)\,\prod_{t=0}^{H}\pi_{\theta}(a_t\mid s_t)\,P(s_{t+1}\mid s_t,a_t),
$$
so
$$
\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)
= \sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t).
$$

\bigskip

\noindent\textbf{  Plug in:}

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


proof of (2): 

\[
\nabla_{\theta}J(\pi_{\theta})
= \mathbb{E}_{\tau\sim\pi_{\theta}}\!\Biggl[\sum_{t=0}^{H}\gamma^{t}\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\bigl(\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'})-b(s_t)\bigr)\Biggr]
\]

This form shows the policy gradient as a sum over actions, each scaled by an advantage estimate: a discounted future reward sum minus a baseline.

\bigskip

\noindent\textbf{Maximize}

We want to maximize expected return:

\[
J(\theta)
= \mathbb{E}_{\tau \sim \pi_{\theta}}\bigl[R(\tau)\bigr],
\quad
R(\tau)
= \sum_{t=0}^{H}\gamma^{t}\,r(s_t,a_t),
\quad
\tau=(s_0,a_0,\dots,s_H,a_H).
\]

\bigskip

\noindent\textbf{  Use the log‐derivative trick}

\[
\nabla_{\theta}J(\theta)
= \nabla_{\theta}\,\mathbb{E}_{\tau\sim\pi_{\theta}}\bigl[R(\tau)\bigr]
= \mathbb{E}_{\tau\sim\pi_{\theta}}\!\bigl[\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)\,R(\tau)\bigr].
\]

proof of the trick:

\begin{equation}
\nabla_\theta p(\tau;\theta)
= p(\tau;\theta)\,\nabla_\theta \log p(\tau;\theta)
\end{equation}

\begin{align}
\nabla_\theta \mathbb{E}_{\tau\sim\pi_\theta}[R(\tau)]
&= \nabla_\theta \int p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \int \nabla_\theta p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \int p_{\pi_\theta}(\tau)\,\nabla_\theta \log p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \mathbb{E}_{\tau\sim\pi_\theta}\bigl[\nabla_\theta \log p_{\pi_\theta}(\tau)\,R(\tau)\bigr] \\
&= \nabla_\theta J(\theta)\,.
\end{align}


\bigskip

\noindent\textbf{ Decompose \(\log p_{\pi_{\theta}}(\tau)\):}

\[
p(\tau)
= \rho_0(s_0)\,\prod_{t=0}^{H}\pi_{\theta}(a_t\mid s_t)\,P(s_{t+1}\mid s_t,a_t),
\]
so
\[
\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)
= \sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t).
\]

\bigskip

\noindent\textbf{  Plug in:}

\[
\nabla_{\theta}J(\theta)
= \mathbb{E}_{\tau}\Bigl[\Bigl(\sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\Bigr)\,R(\tau)\Bigr]
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\bigl[\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,R(\tau)\bigr].
\]

\bigskip

\noindent\textbf{  Reward‐to‐Go (Causality)}

Only future rewards depend on \(a_t\). Dropping past rewards:
(mathematically if you write out the integral terms that do not depend on the integrator 
factor out and you are left with the gradient of integral of a probability which is the constant 1 and hence zero)
\[
\nabla_{\theta}J(\theta)
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\underbrace{\sum_{t'=t}^{H}\gamma^{t'}r(s_{t'},a_{t'})}_{\text{reward‐to‐go}}\Bigr].
\]
Factor out \(\gamma^t\):
\[
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\gamma^t\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'})\Bigr].
\]

\bigskip

\noindent\textbf{ Add a Baseline (As to what a baseline is and why we subtract see pp 329 of Sutton, but here we give the proof)}

Subtract a baseline \(b(s_t)\):
\[
\nabla_{\theta}J(\theta)
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\gamma^t\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\Bigl(\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'}) - b(s_t)\Bigr)\Bigr].
\]


proof that you can subtract the base so long as it is a function of state alone:

\begin{align*}
\nabla_{\theta} J(\pi_{\theta}) 
&= \mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
\sum_{t=0}^{H}\gamma^{t}\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\left(\sum_{t'=t}^{H}\gamma^{t'-t}r(s_{t'},a_{t'}) - b(s_{t})\right)
\right]
\end{align*}

We explicitly verify that the baseline is unbiased:
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


\vspace{2em}

% Erratum note: finite‐ to infinite‐horizon carry–over typo
\noindent
In the finite‐horizon derivation we write
\[
\nabla_{\theta}
= \sum_{t=0}^{H}
\mathbb{E}_{s_{t}\sim d_{t}^{\pi},\,a_{t}\sim\pi_{\theta}}
\bigl[\gamma^{t}\,\nabla_{\theta}\log\pi_{\theta}(a_{t}\mid s_{t})\,\hat{A}(s_{t},a_{t})\bigr].
\]
Here both the state‐distribution \(d_{t}^{\pi}\) and the discount weight \(\gamma^{t}\) are explicitly indexed by \(t\).

When passing to the infinite‐horizon form, those two pieces are folded into a single “discounted occupancy” measure
\[
d^{\pi}(s)\;=\;(1-\gamma)\sum_{t=0}^{\infty}\gamma^{t}\,d_{t}^{\pi}(s),
\]
We can now absorb the discount factor and the sum into the measure $d_{t}^{\pi}(s)$ (tutorial characterizes this as "dropping" the discount factor but this is not really an accurate description)  so that the gradient can be written as
\[
\nabla_{\theta}J
= \frac{1}{1-\gamma}\,
\mathbb{E}_{s\sim d^{\pi},\,a\sim\pi_{\theta}}
\bigl[\nabla_{\theta}\log\pi_{\theta}(a\mid s)\,\hat{A}(s,a)\bigr].
\]

The stray subscript “\(_t\)” on the state distribution in the infinite‐horizon equation was simply a leftover from the finite‐horizon version. The correct infinite‐horizon line should read
\[
\nabla_{\theta}J
= \frac{1}{1-\gamma}\,
\mathbb{E}_{s\sim d^{\pi}(s),\,a\sim\pi_{\theta}(a\mid s)}
\bigl[\nabla_{\theta}\log\pi_{\theta}(a\mid s)\,\hat{A}(s,a)\bigr],
\]
with no time index on \(d^{\pi}\).

While a simple time-dependent baseline $b_t = \frac{1}{N} \sum_{i=1}^N R_{i,t}$ is often used to reduce variance in Monte Carlo policy gradient estimators, it does not represent a true value function $V(s_t) = \mathbb{E}[R_t \mid s_t]$. This is because $b_t$ marginalizes over all states $s_t$ encountered at time $t$ rather than conditioning on a specific state. Thus, it captures only the average return under the state visitation distribution $d_t(s)$, not the expected return from a particular state $s_t = s$. Although useful for variance reduction, this approach lacks the precision and generalization capabilities of a learned, state-dependent baseline.

so there is enough data that we achiever epsilon under offline data. Now we use this policy and measure and compare the policy with supposed one which is correct at this point we do not actually have the labels so the l is theoretical 
