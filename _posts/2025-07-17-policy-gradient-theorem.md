---
title: "Offline RL"
date: 2025-07-13
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


This tutorial bridges the gap between the rigorous mathematical formulation of Discrete Flow Matching for continuous time Markov chains and a minimal, practical PyTorch implementation.  We begin by defining a factorized mixture path between a simple “source” distribution and an observed “target” distribution, derive the coordinate‐wise CTMC velocities via the Kolmogorov forward equation, and show how to assemble those into a learnable model.  Each step of the derivation is  followed by the code that implements it.



You will find:
	- A concise statement of each mathematical object (probability path, Bregman divergence, CTMC velocity, posterior parameterization).

	-The exact lines of code—no hidden helpers—showing how to (1) sample the mixture path, (2) compute the matching loss, and (3) run the coordinate‐wise Euler sampler.

	- Tips on how to factorize over dimensions so that both training and sampling remain tractable even in high dimensions.


The basic idea is straightforward: 
	

## Algorithm

1. Sample a data point  
   $$
   X_1 \sim q_{\mathrm{data}}.
   $$  
   This is your “target” \((X_i^1)\) in every coordinate.

2. Sample a “source”  
   $$
   X_0
   $$  
   from your simple prior (e.g.\ uniform or noise).

3. Pick a time  
   $$
   t \sim U[0,1].
   $$

4. Draw the intermediate state  
   $$
   X_t
   $$  
   from the mixture path  
   $$
   p_{t\mid0,1}(x\mid X_0,X_1)
   = \prod_{i=1}^D
   \Bigl[
     \kappa(t)\,\delta(x_i,\,X_i^1)
     +\bigl(1-\kappa(t)\bigr)\,\delta(x_i,\,X_i^0)
   \Bigr].
   $$  
   In other words, each coordinate \(i\) of \(X_t\) is \(X_i^1\) with probability \(\kappa(t)\), or \(X_i^0\) otherwise.

5. Compute the loss using the known true generator (which depends on \(X_i^1\)) and your model’s posterior  
   $$
   p^{\theta,i}_{1\mid t}(\,\cdot\mid X_t).
   $$  
   Because \(X_i^1\) is always the data, it drives the “true” velocity in every coordinate, and \(X_t\) is exactly where you evaluate both true and model velocities.

   $p^{\theta,i}_{1\mid t}(\,\cdot\mid X_t)$ is learned through a simple cross‐entropy objective:  
   $$
   L(\theta)
   = -\mathbb{E}_{\,t,X_0,X_1,X_t}\!\sum_{i=1}^d
     \log p^{\theta,i}_{1\mid t}\bigl(X_1^i\mid X_t\bigr)
     \;+\;\mathrm{const}.
   $$

	
	\medskip
	
	\noindent\textbf{During inference, you reverse this:}
	\begin{enumerate}
		\item Start from \(X_0\) drawn from the prior.
		\item Iteratively step forward in \(t\) using the learned posterior \(p^{\theta,i}_{1\mid t}\) to sample each coordinate’s jump, until you reach \(X_1\)—your generated sample.
	\end{enumerate}
	
	\smallskip
	
	\noindent\emph{Note:} 
	\(X_i^1\) in the formulas is always the data’s coordinate \(i\).  
	\(X_t\) during training is sampled via the chosen mixture path \(p_{t\mid0,1}\), ensuring the model learns to match that path’s velocity field.  
	
	\section{Factorized Source→Sink Velocities}
	
	Because both the probability path and its velocity factorize over coordinates, we may treat each dimension independently. Writing a full state as \(x=(x_1,\dots,x_d)\) and a proposed next state as \(y=(y_1,\dots,y_d)\), we have
	\[
	q_t(x)=\prod_{i=1}^d q^i_t(x_i),
	\]
	and the full rate vector
	\[
	u_t(y,x)
	=\sum_{i=1}^d
	\underbrace{\delta\bigl(y_{\bar i},\,x_{\bar i}\bigr)}_{\displaystyle \prod_{j\neq i}\delta(y_j,x_j)}
	\;u^i_t\bigl(y_i,\,x\bigr).
	\]
	Here
	\[
	\delta(y_{\bar i},x_{\bar i})
	=\prod_{j\neq i}\delta(y_j,x_j)
	\]
	ensures that only coordinate \(i\) is allowed to move, and
	\[
	u^i_t(y_i,x)\in\mathbb{R}
	\]
	is exactly the \emph{source→sink rate} for changing coordinate \(i\) from its current value \(x_i\) (the “source”) to a new value \(y_i\) (the “sink”). By factorizing both \(q_t\) and \(u_t\) this way, we reduce the high-dimensional CTMC rate problem into \(d\) independent scalar rate functions \(u^i_t\).
	
	
\section{Mixture-path Construction}

Introduce an auxiliary coupling \(Z=(X_0,X_1)\).  For each coordinate \(i\in\{1,\dots,d\}\), define the conditional marginal path
\[
p^i_{t\mid0,1}(x_i\mid x_0,x_1)
\;=\;
\kappa(t)\,\delta\bigl(x_i,x_{1,i}\bigr)
\;+\;
\bigl(1-\kappa(t)\bigr)\,\delta\bigl(x_i,x_{0,i}\bigr)\,,
\]
where we choose the simple scheduler
\[
\kappa(t)=t,
\qquad
\dot\kappa(t)=1.
\]
Under this choice, at time \(t\) each coordinate \(X^i_t\) equals its “sink” value \(X^i_1\) with probability \(t\), and its “source” value \(X^i_0\) with probability \(1-t\).  In other words,
\[
\Pr\bigl[X^i_t = X^i_1 \mid X_0=x_0,\,X_1=x_1\bigr] = t,
\qquad
\Pr\bigl[X^i_t = X^i_0 \mid X_0=x_0,\,X_1=x_1\bigr] = 1-t.
\]
Thus the full factorized path is
\[
p_{t\mid0,1}(x\mid x_0,x_1)
\;=\;\prod_{i=1}^d p^i_{t\mid0,1}(x_i\mid x_0,x_1)\,. 
\]

\section{Posterior‐driven Source→Sink Velocities}

Once we have the pure source→sink rates 
\[
u^i_t(y_i, x\mid x_0, x_1)
=\frac{1}{1-t}\Bigl[\delta(y_i,x_{1,i})-\delta(y_i,x_i)\Bigr]
\]
for the mixture path, we introduce a learnable per‐coordinate “target–initial” posterior
\[
p_{\theta,i,1\mid t}(x_{1,i}\mid x)
\approx p_{i,1\mid t}(x_{1,i}\mid x)\,.
\]
We then assemble the marginal velocity as
\[
u^i_t(y_i,x)
=\sum_{x_{1,i}}
u^i_t(y_i,x\mid x_0,x_1)\;p_{\theta,i,1\mid t}(x_{1,i}\mid x)
=\frac{1}{1-t}
\sum_{x_{1,i}}
\Bigl[\delta(y_i,x_{1,i})-\delta(y_i,x_i)\Bigr]\,
p_{\theta,i,1\mid t}(x_{1,i}\mid x).
\]
Plugging this into the Euler‐step transition kernel
\[
P\bigl(X_{t+h,i}=y_i\mid X_t=x\bigr)
=\delta(y_i,x_i)+h\,u^i_t(y_i,x)+o(h)
\]
yields
\[
P\bigl(X_{t+h,i}=y_i\mid X_t=x\bigr)
=\sum_{x_{1,i}}
\Bigl[
\delta(y_i,x_i)
+\tfrac{h}{1-t}\bigl(\delta(y_i,x_{1,i})-\delta(y_i,x_i)\bigr)
\Bigr]\,
p_{\theta,i,1\mid t}(x_{1,i}\mid x)
+o(h).
\]
In practice, sampling is implemented coordinate‐wise by:
\begin{enumerate}
	\item Draw \(x_{1,i}\sim p_{\theta,i,1\mid t}(\cdot\mid x)\).
	\item With probability \(\frac{h}{1-t}\), set \(X_{t+h,i}=x_{1,i}\); otherwise keep \(X_{t+h,i}=x_i\).
\end{enumerate}
This makes explicit how the learned posterior per coordinate drives the source→sink transitions during sampling.
	
	
\section{Coordinate‐wise Euler Sampling}

For a small time step \(h\), the CTMC transition kernel factorizes over coordinates:
\[
P\bigl(X_{t+h,i}=y_i \mid X_t=x\bigr)
=\delta(y_i,x_i)+h\,u^i_t(y_i,x)+o(h).
\]
Substituting our posterior‐assembled velocity
\[
u^i_t(y_i,x)
=\frac{1}{1-t}
\sum_{x_{1,i}}
\bigl[\delta(y_i,x_{1,i})-\delta(y_i,x_i)\bigr]\,
p_{\theta,i,1\mid t}(x_{1,i}\mid x),
\]
we obtain
\[
P\bigl(X_{t+h,i}=y_i \mid X_t=x\bigr)
=\sum_{x_{1,i}}
\Bigl[
\delta(y_i,x_i)
+\frac{h}{1-t}\bigl(\delta(y_i,x_{1,i})-\delta(y_i,x_i)\bigr)
\Bigr]\,
p_{\theta,i,1\mid t}(x_{1,i}\mid x)
+o(h).
\]
Hence the coordinate‐wise sampling algorithm is:
\begin{enumerate}
	\item Draw \(x_{1,i}\sim p_{\theta,i,1\mid t}(\,\cdot\mid x)\).
	\item With probability \(\tfrac{h}{1-t}\), set \(X_{t+h,i}=x_{1,i}\); otherwise keep \(X_{t+h,i}=x_i\).
\end{enumerate}
\section{Per‐Coordinate Posterior Parameterization and Training Loss}

We parameterize the marginal “target–initial” posterior per coordinate
\[
p_{\theta,i,1\mid t}(x_{1,i}\mid x_t)
\]
via a neural network (embedded and flattened as in Section \ref{sec:implementation}).  By Proposition 1 the Conditional Discrete Flow Matching loss over our mixture path reduces to a sum of generalized‐KL Bregman divergences per coordinate:
\[
L(\theta)
=\mathbb{E}_{\,t,X_0,X_1,X_t}\!\Biggl[\sum_{i=1}^d
D_{\mathrm{gKL}}\!\Bigl(\,u^i_t(\cdot,x_t\mid x_0,x_1)\,,\,
u^{\theta,i}_t(\cdot,x_t)\Bigr)\Biggr].
\]
For the mixture‐path generator
\[
u^i_t(y_i,x_t\mid x_0,x_1)
=\frac{1}{1-t}\bigl[\delta(y_i,x_{1,i})-\delta(y_i,x_{t,i})\bigr],
\]
choosing \(D_{\mathrm{gKL}}\) yields the simple cross‐entropy objective
\[
L(\theta)
=-\mathbb{E}_{\,t,X_0,X_1,X_t}\!\sum_{i=1}^d
\log p_{\theta,i,1\mid t}\bigl(X_{1,i}\mid X_t\bigr)
\;+\;\mathrm{const}.
\]

\medskip
In code, using the library’s generalized‐KL loss wrapper:
\begin{verbatim}
from flow_matching.loss import MixturePathGeneralizedKL

loss_fn = MixturePathGeneralizedKL(path=path)

for x_0, x_1 in dataloader:
t      = torch.rand(batch_size) * (1.0 - 1e-3)
sample = path.sample(t=t, x_0=x_0, x_1=x_1)
logits = model(sample.x_t, sample.t)
loss   = loss_fn(
logits=logits,
x_1=sample.x_1,
x_t=sample.x_t,
t=sample.t
)
optimizer.zero_grad()
loss.backward()
optimizer.step()
\end{verbatim}
	
	
	

	


